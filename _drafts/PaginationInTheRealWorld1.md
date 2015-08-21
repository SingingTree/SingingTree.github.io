---
layout: default
title: Pagination in the Real World Pt.1
---

# Pagination in the Real World Pt.1

## Introduction

I've recently been/am involved in tech leading implementation of pagination in a real world architecture that introduces some interesting pain points. I'm going to talk about it, here, now. I anticipate the number of interesting points, challenges, and issues being such that I may create multiple posts about it, as such I've boldly suffixed a "Pt.1" to the title.

The rest of the introduction is a quick intro to pagination and why to do it, if you're already familiar or for whatever reason feel you don't need such a discussion, feel free to skip ahead to the meat of the post starting at the [How to Paginate?](#How to Paginate?) section.

### Pagination: Quickly Described

Pagination is the process of breaking up data into pages. A common place you'll see this is in the results of search engines. If you do a search that yields 47,667,235 results it's not necessarily pragmatic to view all the results immediately and in a single page.

### Why Paginate?

Two of the reasons for pagination, in both the general case and in the work I've been involved in, are as follows:

- It is not ergonomic as a user to view a page with a huge number of results. The presentation of the data may be difficult and/or negatively impacted, and navigating through the data is similarly a difficult matter.
- There are performance issues with sending all the data at once. Sending large amount of data can hurt performance for the sender, and receiving and displaying large amounts of data can hurt performance for the receiver. Poor performance is bad of course, in that we don't want users to have a sluggish experience, or worse, for the system to be unavailable.

There's an interesting mingling of user interface and technical design as to why to do pagination. However in terms of justification as to the why of pagination, that's not the focus of this post. At this point I'm already in the pagination boat for the system I'm working on, and this post is going to talk about the details of the design and the how.

## How to Paginate?

This is the crux of the post. I'm going to talk about some of the common patterns for pagination, how they apply to the real world case I'm dealing with and some of the bugbears involved. I don't have all the answers, but even then I like to think this is going to be an interesting and informative adventure. So let's go!

### Pagination at the Data(base) Level
