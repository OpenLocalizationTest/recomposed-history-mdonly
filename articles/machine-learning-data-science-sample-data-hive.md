<properties title="Down sampling data in Azure HDInsight Hive Tables" pageTitle="Down sampling data in Azure HDInsight Hive Tables | Azure" description="Down sampling data in Azure HDInsight Hive Tables" metaKeywords="" services="Azure Data Science Process" solutions="" documentationCenter="" authors="hangzh-msft" manager="jacob.spoelstra" editor="" videoId="" scriptId="" />

<tags ms.service="Azure Data Science Process" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/09/2015" ms.author="hangzh-msft" />

# Down sampling data in Azure HDInsight Hive Tables 

If the data is big, in order for data understanding, exploration, feature engineering, and fast prototyping the data processing functions and machine learning models, usually it is a good idea to down sample the data. In this article, we describe how to down sample data in Azure HDInsight Hive tables. We cover three popularly used sampling methods: 

- [Uniform random sampling](#uniform)
- [Random sampling by groups](#group)
- [Stratified sampling](#stratified)

## <a name="uniform"></a> Uniform random sampling ##
Uniform random sampling means that each row in the data set has the equal opportunity to be sampled. This can be implemented by adding an extra field rand() to the data set in the inner "select" query, and in the outer "select" query, condition on that random field. 

Here is an example query:

	SET sampleRate=<sample rate, 0-1>;
	select
		field1, field2, …, fieldN
	from
		(
		select
			field1, field2, …, fieldN, rand() as samplekey 
		from <hive table name>
		)a
	where samplekey<='${hiveconf:sampleRate}'
Here, `<sample rate, 0-1>` is the proportion of records that the users want to sample. 

## <a name="group"></a> Random sampling by groups ##

If your data set has some categorical variable, and you want to sample by it, meaning that you want to make sure that the data from the same value of this categorical variable should all be included or excluded from the sampled data set. For instance, if you have a categorical variable "State", which has levels NY, MA, CA, NJ, PA, …, you want records of the same state be always together, sampled or not. 

Here is an example query:

	SET sampleRate=<sample rate, 0-1>;
    select 
		b.field1, b.field2, …, b.catfield, …, b.fieldN
	from
		(
		select 
			field1, field2, …, catfield, …, fieldN
		from <table name>
		)b
	join
		(
		select
			catfield
		from
			(
			select 
				catfield, rand() as samplekey
			from <table name>
			group by catfield
			)a
		where samplekey<='${hiveconf:sampleRate}'
		)c
	on b.catfield=c.catfield

## <a name="stratified"></a>Stratified sampling

Stratified sampling means sampling the entire population of the data in the way such that each subpopulation has the same percentage of observations randomly sampled. Using the same example as above, suppose your data has subpopulations by states, where NJ has 100 observations, NY has 60 observations, and WA has 300 observations. If you specify the rate of stratified sampling to be 0.7, after stratified sampling, approximately 70, 42, and 210 observations of NJ, NY, and WA should be sampled, respectively.

Here is an example query:

	SET sampleRate=<sample rate, 0-1>;
    select 
		field1, field2, field3, ..., fieldN, state 
	from
		(
		select 
			field1, field2, field3, ..., fieldN, state,
			count(*) over (partition by state) as state_cnt, 
      		rank() over (partition by state order by rand()) as state_rank
      	from <table name>
		) a 
	where state_rank <= state_cnt*'${hiveconf:sampleRate}'


More advanced sampling methods in Hive can be found at [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
