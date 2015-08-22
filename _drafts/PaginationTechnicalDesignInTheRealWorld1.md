---
layout: default
title: Pagination Technical Design in the Real World Pt.1
---

# Pagination Technical Design in the Real World Pt.1

## Introduction

I've recently been/am involved in tech leading implementation of pagination in a real world architecture that introduces some interesting pain points. I'm going to talk about it, here, now. I anticipate the number of interesting points, challenges, and issues being such that I may create multiple posts about it, as such I've boldly suffixed a "Pt.1" to the title.

The rest of the introduction is a quick intro to pagination and why to do it, if you're already familiar or for whatever reason feel you don't need such a discussion, feel free to skip ahead to the meat of the post starting at the [How to Paginate?](#How to Paginate?) section.

### Pagination: Quickly Described

Pagination is the process of breaking up data into pages. A common place you'll see this is in the results of search engines. If you do a search that yields 47,667,235 results you typically won't see all those results in a single page. Instead, the results are likely to be paginated: the results will be split into pages.

### Why Paginate?

Two of the reasons for pagination, in both the general case and in the work I've been involved in, are as follows:

- It is not ergonomic as a user to view a page with a huge number of results. The presentation of the data may be difficult and/or negatively impacted, and navigating through the data is similarly a difficult matter.
- There are performance issues with sending all the data at once. Sending large amount of data can hurt performance for the sender, and receiving and displaying large amounts of data can hurt performance for the receiver. Poor performance is bad of course, in that we don't want users to have a sluggish experience, or worse, for the system to be unavailable.

There's an interesting mingling of user interface and technical design as to why to do pagination. However in terms of justification as to the why of pagination, that's not the focus of this post. At this point I'm already in the pagination boat for the system I'm working on, and this post is going to talk about the details of the design and the how.

## How to Paginate?

This is the crux of the post. I'm going to talk about some of the common patterns for pagination, how they apply to the real world case I'm dealing with and some of the bugbears involved. I don't have all the answers, but even then I like to think this is going to be an interesting and informative adventure. So let's go!

### Pagination at the Data(base) Layer

Here I use the term "data layer" to refer to the layer from which an application draws the data it's going to paginate. Depending on the application this may be a database, but it's important to remember that it may also not be a database. For example, it may be a service, or a cache, or the file system.

The data layer may also not be a single interface. It's possible that data is drawn from multiple sources and combined within the application to create a final data set, which is then paginated.

There are two big ramifications of the above in my work:

- The data layer cannot be relied on to natively paginate. The services that we use are not guaranteed to provide pagination. Some of our services may, but if we want to have guaranteed pagination at this level we would need to wrap those that do not in our own logic to do so.
- The data later may not have all the necessary information to perform pagination appropriately. Even if we could rely upon data sources to provide pagination, that may not be sufficient, as that information may need to be further processed by the application before pagination.

A hypothetical example of the above: I have a database which I can query to get customer details. This database also supports pagination! However, the visibility of the customer details is filtered based on the active user. The privacy filtering is done via a query to a privacy service.

I obviously can't rely on just the database here to paginate, as if I solely use its pagination and skip hitting my privacy service I'd be leaking data left and right. So I need to take the results of my database queries and feed them into the privacy service also. But this raises another issue: say I want pages of 10 items, and I query the database for 10 items, but privacy filters out 3 of those items, so I'm left with 7 on the first page. This leaks data too! If you expect 10 items per page and some are less than 10 you know there are hidden entries.

So for my problem domain pagination solely at data layer cannot be done.

### Pagination at the Application Layer
