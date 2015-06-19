---
layout: default
title: Null is Bollocks
---

*This is a blog post I wrote while working on a Java heavy project. It was spurred by adoption of Java 8 being on the horizon, as well as some frustrating code dives to figure out which code paths in a document handling pipeline could lead to null values.*

Null is bollocks.

Null and popular misspellings of null, such as nil, exist in a swag of programming languages, and a lot of what I say here I'd say about any version of null, but let's talk about it in Java.

Null subverts the type system. Null says for some non primitive type you can have something of that type, or you can have nothing. A special type of nothing that explodes when you try to interact with it. Fantastic, really good.

Null is used to represent a lot of things, uninitialized objects, return value for failed methods, termination conditions, objects that have been deleted... But we're living in the future, and we can do better than just calling all of these things null. Before we get to how we do better, let's talk about why we should.

That references can implicitly be null complicates code: you need explicit null checks and you need code to handle null pointers!

Okay, but here's the real wind up (for me, anyway): it complicates mental processing of code. If we're looking at some non primitive reference, we better have some understanding of the context, because that sucka could be nulled! Calling a function that returns a String? Could be a null! We now need to check some docs, read some code, and/or add null checks to code. And if we are given back a null instead of a real, honest String, what does that tell us? Probably that something went wrong, but it doesn't tell us much about what went wrong.

Null is a bollocks way to indicate anything. A null return from a method doesn't tell me much about what happened in the method. A null reference alone doesn't tell me why the reference is nulled (have we yet to initialise, explitly nulled to delete, or assigned from a method that returned a null? I just don't know!). In both these cases I need to go look for more information to find out why something is null. I'm gonna need to look at some docs, trawl some code, or consult dark spirits.

I dunno about y'all, but I'm not super chuffed when I get a null reference and/or return value and then have to spend my precious time to find out what that null means.

So, what do?

Get on the hipster using stuff that's not null buzz. That said, the idea that maybe we shouldn't use null for everything is so wildly unhip and mainstream that even Java is catching on (heyoooooooo). How does one get on said buzz?

1) Don't return null from methods. If a method does not have a failure case, easy, not need to go throwing around nulls. If a method does, then use an algebraic type to indicate that your returned value could be a result, or a failure case. This indicates to the caller that a method can fail, and gives them an idea of how.

2) Don't let references be null. Always initialise your references to some value. Leave something more meaningful in a reference when deleting (or make sure the reference doesn't persist after what it references is gone).

Easier said than done, right? For sure, and the language you're writing in is gonna have a big impact here. There's some hip and amazing languages around that are gonna help enable you to do the above: Haskell, Rust, Scala. Check them out, they're pretty sweet (some don't even have the concept of an implicit null!).

But what about in the here and the now? What about for Java? That's harder, because Java grew up loving nulls. But we can still do better, or at least think about maybe doing it, maybe:

1) Clear, concise and up to date documentation helps. Tell people if something can be null or not, and if it is null, what does that mean? Let's save each other from having to trawl to find out what that null means.

2) Return/assign/whatever something more meaningful than a null. Java 8 has introduced the Optional type, which among other things, indicates to me more clearly the intention of code. One day, in the distant future, someone somewhere may program something in Java 8, and it'd be pretty rad if they used optional instead of null. At least I'd think they're pretty cool.

Obviously there's more that can be done than a few points on some numbered lists. This is (IMO) a really freakin' interesting part of language design and usage, and there's a lot of stuff out there from people smarter (but never more handsome) than me. So check it out. Otherwise hopefully this at least interesting, or makes you think I'm totally rad.

Anyway, I think null is bollocks.
