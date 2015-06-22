---
layout: default
title: Of Too Long File Names (in Windows) and Git
---

# Of Too Long File Names (in Windows) and Git

I was recently involved in a restructure of git repositories for a moderate sized Java code base. As it's a Java code base you end up with impossibly long paths due to package structure, e.g.:

{% highlight sh %}
~/src/main/java/com/brycecorp/documenthandling/xml/server/rest/util/mock/MockDocumentResourceLoader.java
{% endhighlight %}

In reality, I work with significantly deeper structures (huzzah). I also work on Windows at work. This becomes an issue when I start messing around with git and am greeted by the following:

{% highlight sh %}
Filename too long
{% endhighlight %}

The TLDR; result of this is that you can make git play nice with long file names on Windows with the following:

{% highlight sh %}
git config --system core.longpaths true
{% endhighlight %}

Or you can switch operating systems. People will often suggest this in an attempt to rustle my jimmies. I understand as such a handsome, smart, great, good at video games, athletic, and funny dude, it's essential that my jimmies are regularly tested via a rustling. However, my jimmies remain steadfast, and for the remainder of this post I wish to discuss as to why the above happens, because it's interesting, and me an my jimmies have to do something.

## 8.3 Filenames and Other Old Things

[8.3 filenames](https://en.wikipedia.org/wiki/8.3_filename) are filenames with up to 8 characters, with an optional . and an optional extension of up to 3 characters. This is the convention that was used in versions of Windows prior to 95. That said, it's still around, and you can see this style of file name if you pass the /X switch to the dir command.

This is interesting because the file systems that Windows uses today aren't a clean break from those used historically. There are compatibility considerations to be made (such as being able to still work with 8.3 filenames), and there are bits of the operating system that are made up of bits from other, old operating systems.

## MAX_PATH

The Windows API has a constant, MAX_PATH, [which is defined to be 260]( https://msdn.microsoft.com/en-us/library/aa365247%28VS.85%29.aspx#maxpath). There are various intricacies to how this ends up impacting your paths and file names (see the link). However, the net upshot is that you have a path limit that you can hit with only a moderate amount of effort, as opposed to say 4096, which you really have to make some effort to go for (4096 being a common value for PATH_MAX).

MAX_PATH appears to be one of these historical things. The Windows API enforces this limit in many of it's [ANSI related functions](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365239%28v=vs.85%29.aspx).
