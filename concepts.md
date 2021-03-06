---
layout: default
title: Concepts 
---

## Client

On each Seldon deployment, it is possible to provide recommendations for several totally different data sets (for example, two websites). These data sets are divided by using different clients. Each call to the API has client as one of the attributes. The instructions we've provided so far only allow one client to be set up, but watch these docs for updates on how to extend this.

## User

A user of the Seldon Server is any entity that interacts with items and requires recommendations. For example, a person on a news website that is viewing a story and need recommendations on the next story to read.

## Item

Items are the things that are to be recommended from. For example, a web link that shows a news story.

## Action

Actions are any time when a user interacts with an item (viewing it, buying it, adding it to a cart).

*User, Item and Action can be enriched with arbitrary attributes depending on the use case. For example, a newspaper article might have a subtitle and a category.  When the Seldon Server makes its recommendations for a user, it takes the history of items that he/she has interacted with and their attributes into account.*

## Online Predictive Scoring
 
Online prediction scoring algorithms recommend content/items according to the following specification.

{% highlight java %}
ItemRecommendationResultSet recommend(String client, Long user, int dimension,RecommendationContext ctxt, int maxRecsCount, List<Long> recentitemInteractions);
{% endhighlight %}

A couple of things to focus on here are

 1. **RecommendationContext** explained below
 1. **recentItemInteractions** the recent items a user has interacted with. This may be used by algorithms if needed.
 1. **maxRecsCount** the maximum recommendations that this alg should return. Even if the alg cannot create this many, it should still return as many as possible as they can be used in certain combiner configurations.

All algorithms must conform to the ItemRecommendationAlgorithm interface and be defined spring components. This is to allow easy discovery of all algorithms when the server starts. 
 
## Includer
 
Provides a set of items for the algorithm to recommend against. You can have multiple of these in which case the union of the set of items are recommended against, or combine them with excluders. An example would be the RecentItemsIncluder which includes items that have recently been added to the DB.

## Excluder

Opposite of Includer

## Combiner

A combiner takes the output of multiple algorithms and combines them into one set of recommendations. An example would be the FirstSuccessfulCombiner which takes the output of whichever algorithm
provides the requisite number of recs first and outputs those. Combiners have the power to stop other algorithms being run if they find that there is already enough recs.
 
## Strategy

Describes how to map the client/user pair to a set of Alg/Context pairs. Usually this is a simple mapping -- all users get the same alg/context pair. However, when a test is set up, a user may be
given different pair based on the hash of his username.
 
## RecommendationContext

Store for algorithm options. One option that is common to all algorithms is the **MODE**. The possible values are **INCLUSION**, **EXCLUSION** or **NONE**. **INCLUSION** means that the set of items stored in the context are to be recommended from, **EXCLUSION** means that they are excluded from any recs and **NONE** means that the context has no opinion on the item set to be recommended from -- the alg is free to choose any item.
 

