---
layout: default
title: WebM Encryption (with Clearkey): Why and How
---

Encryping WebMs: why? how? let's go:

# Encrypted Media

In this post we're going to have a look at why anyone would want to encrypt WebMs. It's going to involve a lightning (or at least close to lightning) tour of how we got to this point.

In case it's not clear, we're talking Digital Rights Management (DRM) here. There are a lot of debates to be had about DRM, this post isn't going to get into that -- aside from where historically relevant. We may or may not like its existence, but we probably can accept that it does exist and that there is a lot of audio/video content that currently utilises DRM. So, let's get started!

## DRM Through the Ages

DRM as a concept has been around a long time. It's gone by other names (e.g. copy protection), but we've had it for quite awhile now. DVDs were scrambled ([Content Scramble System](Content Scramble System)), games had and continue to have license keys, ebooks will always have tiny watermarks used to track when you share them and earn you black marks in the ledgers of the powerful ebook cabals. We know these things.

Back in the old-ish days of the web, when everything cost a nickel, we consumed a lot of media via Flash and Silverlight. Flash and Silverlight were popular plugins for media playback. There are many reasons for their popularity, but the important one for this discussion is they have DRM. This made these plugins attractive for those serving content that, for whatever reason, was to be DRM protected.

## A Transitional Period

The scene is sometime around 2012: HTML 5 has not yet been officially released, but we're already seeing it adopted. With the popularity of HTML 5's media elements we've seen a move away from Flash and Silverlight for media playback. However, there remained a question about DRM with these new elements. At this stage in the proceedings we have a number of large companies in the streaming business who have previously and still rely on the DRM offerings of Flash and Silverlight. Which companies? Google (Youtube), Netflix, and The BBC, to name a few.

At this stage, there is not a specification that covers encryption for content delivered via HTML 5 media elements.

## Modern DRM (for Audio/Vido)
