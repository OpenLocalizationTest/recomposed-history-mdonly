---
title: Full text search engine (Lucene) architecture in Azure Search | Microsoft Docs
description: Explanation of Lucene query processing and document retrieval concepts for full text search, as related to Azure Search.
services: search
manager: jhubbard
author: yahnoosh
documentationcenter: ''

ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/31/2017
ms.author: jlembicz
---

# How full text search works in Azure Search

This article is for developers who need a deeper understanding of how Lucene full text search works in Azure Search. For text queries, Azure Search will seamlessly deliver expected results in most scenarios, but occasionally you might get a result that seems "off" somehow. In these situations, having a background in the four stages of Lucene query execution (query parsing, lexical analysis, document matching, scoring) can help you identify specific changes to query parameters or index configuration that will deliver the desired outcome. 

> [!Note] 
> Azure Search uses Lucene for full text search, but Lucene integration is not exhaustive. We selectively expose and extend Lucene functionality to enable the scenarios important to Azure Search. As a developer, using the Azure Search APIs, and not Lucene APIs, is required for any custom work related to full text search. 

## Architecture overview and diagram

Processing a full text search query starts with parsing the query text to extract search terms, passing text inputs over to an analyzer. During analysis, individual query terms are sometimes broken down and reconstituted into new forms to cast a broader net over what could be considered as a potential match. The search engine uses the reconstituted query to search an index and retrieve documents with matching terms. A result set is then sorted by a relevance score assigned to each individual matching document.

Restated, query execution has four stages: 

1. Query parsing 
2. Lexical analysis 
3. Document retrieval 
4. Scoring 

The diagram below illustrates the components used to process a search request. 

 ![Lucene query architecture diagram in Azure Search][1]


| Key components | Functional description | 
|----------------|------------------------|
|**Query parsers** | Separate query terms from query operators. Creates the query structure (a query tree) to be sent to the search engine. Azure Search supports two kinds of query syntax (simple and full) for different types of queries.|
|**Analyzers** | Perform lexical analysis on query terms. This process can involve transforming, removing, or expanding of query terms. Azure Search offers a collection of predefined analyzers, including language analyzers, and allows defining custom analyzers.|
|**Index** | An efficient data structure used to store and organize searchable terms extracted from indexed documents. |
|**Search engine** | Retrieves and scores matching documents based on the contents of the inverted index. |

## Anatomy of a search request

A search request is a complete specification of what should be returned in a result set. In simplest form, it is an empty query with no criteria of any kind. A more realistic example includes parameters, several query terms, perhaps scoped to certain fields, with possibly a filter expression and ordering rules.  

The following example is a search request you might send to Azure Search using the [REST API](https://docs.microsoft.com/en-us/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2016-09-01 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

For this request, the search engine does the following:

1. Filters out documents where the price is at least $60 and less than $300.
2. Executes the query. In this example, the search query consists of phrases and terms: `"Spacious, air-condition* +\"Ocean view\""` (users typically don't enter punctuation, but including it in the example allows us to explain how analyzers handle it). For this query, the search engine scans the description and title fields specified in `searchFields` for documents that contain "Ocean view", and additionally on the term "spacious", or on terms that start with the prefix "air-condition". 
3. Orders the resulting set of hotels by proximity to a given geography location, and then returned to the calling application. 

The majority of this article is about processing of the *search query*: `"Spacious, air-condition* +\"Ocean view\""`. Filtering and ordering, in this case by geographic location, are out of scope. For more information, see [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## Stage 1: Query parsing 

As noted, the query string is the first line of the request: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

The query parser separates operators (such as `*` and `+` in the example) from search terms, and deconstructs the search query into *subqueries* of a supported type: 

+ *term query* for standalone terms (like spacious)
+ *phrase query* for quoted terms (like ocean view)
+ *prefix query* for terms followed by a prefix operator `*` (like air-condition)

Operators associated with a subquery determine whether the query "must be" or "should be" satisfied in order for a document to be considered a match. For example, `+"Ocean view"` is "must" due to the `+` operator. 

The query parser restructures the subqueries into a *query tree* (an internal structure representing the query) it passes on to the search engine. In the first stage of query parsing, the query tree looks like this.  

 ![Boolean query searchmode any][2]

> [!Note]
> A search query is executed independently against all searchable fields in the Azure Search index unless you limit the fields set with the `searchFields` parameter, as illustrated in the example search request.  

### Supported parsers: Simple and Full Lucene 

 Azure Search exposes two different query languages, *simple* (default) and *full*. By setting the `queryType` parameter with your search request, you tell the query parser which query language you choose so that it knows how to interpret the operators and syntax. The Full Lucene query language, which you get by setting `queryType=full`, extends the default Simple query language by adding support for more operators and query types like wildcard, fuzzy, regex, and field-scoped queries. For example, a regular expression sent in Simple query syntax would be interpreted as a query string and not an expression. The example request in this article uses `queryType=full`.

### Impact of searchMode on the parser 

Another search request parameter that affects parsing is the `searchMode` parameter. It controls the default operator for Boolean queries: any (default) or all.  

When `searchMode=any`, which is the default, the space delimiter between spacious and air-condition is OR (`|`), making the sample query text equivalent to: 

~~~~
Spacious,|air-condition*+"Ocean view" 
~~~~

Explicit operators, such as `+` in `+"Ocean view"`, are unambiguous in boolean query construction (the term *must* match). Less obvious is how to interpret the remaining terms: spacious and air-condition. Should the search engine find matches on ocean view *and* spacious *and* air-condition? Or should it find ocean view plus *either one* of the remaining terms? 

By default (`searchMode=any`), the search engine assumes the broader interpretation. Either field *should* be matched, reflecting "or" semantics. The initial query tree illustrated previously, with the two "should" operations, shows the default.  

Suppose that we now set `searchMode= all`. In this case, the space is interpreted as an "and" operation. Each of the remaining terms must both be present in the document to qualify as a match. The resulting sample query would be interpreted as follows: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

A modified query tree for this query would be as follows, where a matching document is the intersection of all three subqueries: 

 ![Boolean query searchmode all][3]

> [!Note] 
> Choosing`searchMode=any` over `searchMode=all` is a decision best arrived at by running representative queries. Users who are likely to include operators (common when searching document stores) might find results more intuitive if `searchMode=all` informs boolean query constructs. For more about the interplay between `searchMode` and operators, see [Simple query syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## Stage 2: Lexical analysis 

Lexical analyzers process *term queries* and *phrase queries* after the query tree is structured. An analyzer accepts the text inputs given to it by the parser, processes the text, and then sends back tokenized terms to be incorporated into the query tree. 

The most common form of lexical analysis is *linguistic analysis* which transforms query terms based on rules specific to a given language: 

* Reducing a query term to the root form of a word 
* Removing non-essential words (stopwords, such as "the" or "and" in English) 
* Breaking a composite word into component parts 
* Lower casing an upper case word 

All of these operations tend to erase differences between the text input provided by the user and the terms stored in the index. Such operations go beyond text processing and require in-depth knowledge of the language itself. To add this layer of linguistic awareness, Azure Search supports a long list of [language analyzers](https://docs.microsoft.com/rest/api/searchservice/language-support) from both Lucene and Microsoft.

> [!Note]
> Analysis requirements can range from minimal to elaborate depending on your scenario. You can control complexity of lexical analysis by the selecting one of the predefined analyzers or by creating your own [custom analyzer](https://docs.microsoft.com/en-us/rest/api/searchservice/Custom-analyzers-in-Azure-Search?redirectedfrom=MSDN). Analyzers are scoped to searchable fields and are specified as part of a field definition. This allows you to vary lexical analysis on a per field basis. Unspecified, the *standard* Lucene analyzer is used.

In our example, prior to analysis, the initial query tree has the term "Spacious," with an uppercase "S" and a comma that the query parser interprets as a part of the query term (a comma is not considered a query language operator).  

When the default analyzer processes the term, it will lowercase "ocean view" and "spacious", and remove the comma character. The modified query tree will look as follows: 

 ![Boolean query with analyzed terms][4]

### Exceptions to lexical analysis 

Lexical analysis applies only to query types that require complete terms – either a term query or a phrase query. It doesn’t apply to query types with incomplete terms – prefix query, wildcard query, regex query – or to a fuzzy query. Those query types, including the prefix query (air-condition\*) in our example, are added directly to the query tree, bypassing the analysis stage. 

<a name="stage3"></a>
## Stage 3: Document retrieval 

Document retrieval refers to finding documents with matching terms in the index. This stage is understood best through example. Let's start with a hotels index having the following simple schema: 

~~~~
{   
    "name": "hotels",     
    "fields": [     
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },     
        { "name": "title", "type": "Edm.String", "searchable": true },     
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Further assume that this index contains the following four documents: 

~~~~
{ 
    "value": [
        {         
            "id": "1",         
            "title": "Hotel Atman",         
            "description": "Spacious rooms, ocean view, walking distance to the beach."   
        },       
        {         
            "id": "2",         
            "title": "Beach Resort",        
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."     
        },       
        {         
            "id": "3",         
            "title": "Playa Hotel",         
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },       
        {         
            "id": "4",         
            "title": "Ocean Retreat",         
            "description": "Quiet and secluded"
        }    
    ]
}
~~~~

**How terms are indexed**

To understand retrieval, it helps to know a few basics about how terms are organized in an index. The unit of storage is an inverted index, one for each searchable field. Within an inverted index is a sorted list of all terms from all documents. Each term maps to the list of documents in which it occurs, as evident in the example below.

To produce the terms in the index, the search engine performs parsing and analysis steps similar to those described for query execution. For parsing, terms are extracted, except indexing extracts terms from *documents* instead of a query string. For analysis, text inputs are passed to an analyzer, lower-cased, stripped of punctuation, and so forth. It's common, but not required, to use the same analyzers for both operations so that processed terms look more like terms stored inside the index.

> [!Note]
> In contrast with Lucene, Azure Search lets you specify different analyzers for indexing and search via additional `indexAnalyzer` and `searchAnalyzer` field parameters. If unspecified, the analyzer set with the `analyzer` property is used for both indexing and searching.  

**Inverted index for example documents**

Returning to our example, for the title field, the inverted index looks like this:

| Term | Document list |
|------|---------------|
| atman | 1 |
| beach | 2 |
| hotel | 1, 3 |
| ocean | 4  |
| playa | 3 |
| resort | 3 |
| retreat | 4 |

In the title field, only *hotel* shows up in two documents: 1, 3.

For the description field, the index is as follows:

| Term | Document list |
|------|---------------|
| air |	3
| and |	4
| beach | 1
| conditioned |	3
| comfortable |	3
| distance | 1
| island | 2
| located |	2
| north | 2
| ocean | 1, 2, 3
| of | 2
| on |2
| quiet | 4
| rooms	 | 1, 3
| secluded | 4
| shore	| 2
| spacious | 1
| the | 1, 2
| to | 1
| view | 1, 2, 3
| walking |	1
| with | 3


**Matching query terms against indexed terms**

Given the inverted indices above, let’s return to the sample query and see how matching documents are found for our example query. Recall that the final query tree looks something like this: 

 ![Boolean query with analyzed terms][4]

During query execution, individual queries are executed against the searchable fields independently. 

+ The TermQuery, "spacious", matches document 1 (Hotel Atman). 

+ The PrefixQuery, "air-condition*", doesn't match any documents. 

  This is a behavior that sometimes confuses developers. Although the term air-conditioned exists in the document, it is split into two terms by the default analyzer. Recall that prefix queries, which contain partial terms, are not analyzed. Therefore terms with prefix "air-condition" are looked up in the inverted index and not found.

+ The PhraseQuery, "ocean view", looks up the terms "ocean" and "view" and checks the proximity of terms in the original document. Documents 1, 2 and 3 match this query in the description field. Notice document 4 has the term ocean in the title but isn’t considered a match, as we're looking for the "ocean view" phrase rather than individual words. 

On the whole, for the query in question, the documents that match are 1, 2, 3. 

As a side note, if your requirements included finding a match on permutations of "air-condition", you could make it a term or phrase query instead of a prefix query. Doing so would deliver additional matches for that term.

## Stage 4: Scoring  

Every item in a search result set is assigned a relevance score, then ranked highest to lowest. Assuming no custom sort, results are ranked by search score.

Scoring is part of full text search that includes analysis. Given a full text search query, you cannot turn off scoring.

Scoring is not applied in unspecified queries (search=*) or in specialized query types that bypass analysis. Specifically, wildcard search, prefix, regex, and fuzzy search queries are routed through an internal query rewriting process and thus return constant scores of 1.0. Whenever you see a constant of 1.0, you know that scoring was not applied. 

> [!Note]
> Although scoring is not used for full-syntax-only query types (such as wildcard, fuzzy, prefix, regex searches), you can use field or tag boosting to customize rank scores. For more information, see [Full Lucene query syntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) and [Scoring profiles](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index).
>

### Scoring example

The following example illustrates scoring on results from the [built-in realestate-us-sample index](search-get-started-portal.md#query-index), on a search term of *water*. For brevity, the results are the top 5 out of a total of 376 matches, limited to the listingId field and search score (query is "search=water&$select=listingId&$count=true&$top=5").

The example is meant to illustrate a range of scores. The highest score in this case is 0.8197428. 

 ![scoring example on realestate index][5]

In the full results, which you can see if you run the query without the `$select=listingID` parameter, the two highest ranking documents have a street address of *Waters Avenue South*. Different scores for the two highest ranked documents exist because of variations in the documents themselves. Specifically, the description is shorter for the first document, which gives more weight to the matching term.

All remaining documents have identical search scores of 0.5567084. This is because the description is mostly the same for all documents (*water frontage* in virtually identical descriptions). Descriptions in the sample dataset include generated strings that are reused multiple times.

### Search score computation and ranking

The score is computed based on statistical properties of the data and the query, with the scoring formula derived from [TF-IDF (term frequency-inverse document frequency)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Base relevance is computed using term frequencies, but proximity of terms (within the same document, or across different documents) are also factors.  

Search score values can be repeated throughout a result set. For example, you might have 10 items with a score of 1.2, 20 items with a score of 0.9, and 20 items with a score of 0.5. When multiple hits have the same search score, the ordering of same scored items is not defined, and is not stable. Run the query again, and you might see items shift position. Given two items with an identical score, there is no guarantee which one appears first. 

## Next steps

+ Build the sample index, try out different queries and review results. For instructions, see [Build and query an index in the portal](search-get-started-portal.md#query-index).

+ Try additional query syntax from the [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) example section or from [Simple query syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) in Search explorer in the portal.

+ Review [scoring profiles](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) if you want to impose custom ranking logic.

+ [Choose a different language analyzer](https://docs.microsoft.com/rest/api/searchservice/language-support) for non-English linguistic analysis.

+ [Configure custom analyzers](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) for either minimal processing or specialized processing on specific fields.

## See also

[Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

[Simple query syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)

[Full Lucene query syntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

[Handle search results](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-query-architecture/architecture-diagram2.png
[2]: ./media/search-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-query-architecture/azSearch-queryparsing-spacious2.png
[5]: ./media/search-query-architecture/azSearch-scores-realestate.png
