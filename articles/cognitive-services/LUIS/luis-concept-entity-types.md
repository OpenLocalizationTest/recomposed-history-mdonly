---
title: Understanding entity types in LUIS apps in Azure | Microsoft Docs
description: Add entities (key data in your application's domain) in Language Understanding Intelligent Services (LUIS) apps.
services: cognitive-services
author: DeniseMak
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 03/01/2017
ms.author: cahann
---
# Entities in LUIS

<!--
Entities are key data in your application’s domain. An entity represents a class including a collection of similar objects (places, things, people, events or concepts). Entities describe information relevant to the intent, and sometimes they are essential for your app to perform its task. For example, a News Search app may include entities such as “topic”, “source”, “keyword” and “publishing date”, which are key data to search for news. In a travel booking app, the “location”, “date”, "airline", "travel class" and "tickets" are key information for flight booking (relevant to the "Bookflight" intent). 
--> 
Entities are important words in utterances that describe information relevant to the intent, and sometimes they are essential to it. Entities belong to classes of similar objects. 

In the utterance "Book me a ticket to Paris", "Paris" is an entity of type location. By recognizing the entities that are mentioned in the user’s input, LUIS helps you choose the specific actions to take to fulfill an intent.

You do not need to create entities for every concept in your app, but only for those required for the app to take action. You can add up to **30** entities in a single LUIS app. 

You can add, edit or delete entities in your app through the **Entities list** on the **Entities** page. Luis offers many types of entities; prebuilt entities, custom machine learned entities and close list entities.


# Types of entities

LUIS offers the following types of entities:


| Type          | Description           | Maximum number  |
| ------------- |-----------------------|:-----------------:|
| Prebuilt      | System-defined types | ? |
| List      | List entities represent a fixed set of synonyms or related words in your system. They aren't machine learned, and are best used for a known set of variations on ways to represent the same concept. List entities don't have be labeled in utterances or trained by the system.  <br/> A list entity is an explicitly specified list of values. Each value may have one or more synonyms. Unlike other entity types, a list entity cannot have additional values discovered during training.   | ?  |
| Simple | A simple entity is a generic entity that describes a single concept.     |  ?   |
| Hierarchical | You can define relationships between entities based on hereditary hierarchical patterns, where the generic entity acts as the parent and the children are sub-types under the parent, and they share the same characteristics.      |  ?   |
| Composite | A composite entities is made up of other entities that form parts of a whole      |  ?    |


