---
layout: post
title:  "Identifying Change Boundaries"
date:   2014-09-26 03:58:39
categories: Architecture
tags: Architecture
---

So you just finished implementing a new widget on your fancy new dashboard in the hottest app since google maps. And now you’re wondering… did I build this right? 

Did I identify the appropriate abstractions to make my code more readable and reusable? In six months when archaeologists are digging through my code will they be cursing my name or singing my praises?

It’s easy to doubt yourself when you’re drowning in a sea of possibilities. For any given feature there are dozens if not hundreds of ways to slice it. The problem is that each combination represents a set of abstractions that, when separated and studied in isolation, are all equally valid and coherent.

The trick is to identify the set of abstractions that will generate the least amount of friction when your product changes.

## The Method

1. What are all the ways the code you just wrote might need to change in the future?

    1. Grab someone from product, head to the nearest whiteboard, and list out these changes.

2. How likely are these things to happen?

    2. An approximate ranking is sufficient

3. How much pain will your current solution cause if anyone one of those things caused a need for change?

    3. How many different abstractions/context boundaries do you need to modify to introduce the desired change?

    4. Would any of them need to be completely rewritten?

Once you’ve fleshed out this list, you’ve now got the building blocks for identifying your change boundaries..

# An Example


## In this example we’ve created an analytics library for tracking events. Its public interface to the app is the event name and any relevant entities to the event. It then serializes the entities and sends them to the databases.

## Reasons to change, ranked by likelihood

1. Adding new events

2. Adding new properties of an entity to be serialized  

3. Adding new entities to track

4. Adding new analytics end-points

## Adding New Events: Easy

	Right away we know that the most likely reason for the code to change is inherently covered by the architecture; at the very least we can be assured we haven’t built this thing upside down. When we need to track some new event in the app, we simply issue a new call within the context of that event. So far, the architecture is correct because adding a new event is easy to do and we only need to change one context boundary.

## Adding New Entities and Properties: Complex

Let’s say we have an existing entity called **ProductEntity**, and we have just implemented a new feature called "Product Popularity" that reports how popular a product is at any given time. We now want to track the product_popularity any time a user interacts with a **ProductEntity**.

Adding this a a property to be serialized requires us to only have to edit one thing: the **ProductEntitySerializer.** This is good, however, that one thing doesn’t live inside the context I would expect when dealing with **ProductEntity**. In order to add this property, I have to know about and modify code across two separate context boundaries. This causes friction.

Adding a new entity just as complex. Since our serializers live in the context of the analytics domain, we need to modify the context of our new entity and the analytics in order to make this change.

## Solution 1: Extract the serialization process

[Image 2]

	 By moving the serializer into the domain of the entity it is serializing, we’re reducing the friction caused by having to switch contexts. As the definition of the entity changes, everything that needs to change as a result now live within a single context.

## Adding new analytics end-points: Very complex

**	**The diagram shows that we only have a single endpoint that we’re sending analytics to. What if we find that we need to add a second service to better analyze our analytics? Since our library is coupled with the persistence logic of the database, we would need to shoehorn in the logic to also persist the data in our new analytics service. This is awkward because the domain of analytics has nothing to do with the domain of this new service. 

## Solution 2: Extract the logger into an adapter

[Image 3]

	Similar to how we extracted the serializers from the analytics domain, we’ve done the same for the logger and called them analytics adapters. This allows us to change how and where we persist our analytics independently from how we define them.

Our final architecture let’s us add new events, properties, entities, and endpoints with minimal friction.
