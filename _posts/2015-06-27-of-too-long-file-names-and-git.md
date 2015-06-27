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

Or you can switch operating systems. People will often suggest this in an attempt to rustle my jimmies. I understand as such a handsome, smart, great, good at video games, athletic, and funny dude, it's essential that my jimmies are regularly tested via a rustling. However, my jimmies remain steadfast, and for the remainder of this post I wish to discuss as to why the above happens, and how it all works under the hood, because it's interesting, and me an my jimmies have to do something.

## 8.3 Filenames and Other Old Things

[8.3 filenames](https://en.wikipedia.org/wiki/8.3_filename) are filenames with up to 8 characters, with an optional . and an optional extension of up to 3 characters. This is the convention that was used in versions of Windows prior to 95. That said, it's still around, and you can see this style of file name if you pass the /X switch to the dir command.

This is interesting because the file systems that Windows uses today aren't a clean break from those used historically. There are compatibility considerations to be made (such as being able to still work with 8.3 filenames), and there are bits of the operating system that are made up of bits from other, old operating systems.

## MAX_PATH

The Windows API has a constant, MAX_PATH, [which is defined to be 260]( https://msdn.microsoft.com/en-us/library/aa365247%28VS.85%29.aspx#maxpath). There are various intricacies to how this ends up impacting your paths and file names (see the link). However, the net upshot is that you have a path limit that you can hit with only a moderate amount of effort, as opposed to say 4096, which you really have to make some effort to go for (4096 being a common value for PATH_MAX).

MAX_PATH appears to be one of these historical things. The Windows API enforces this limit for its [ANSI file manipulation functions](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365239%28v=vs.85%29.aspx). These functions also have Unicode versions, which have a much greater character limit of 32767 characters.

The file manipulation functions that rely on MAX_PATH being 260 have been around for a long long time, and I imagine the value remains unchanged to avoid a horrifying compatibility nightmare (though the current path limit situation isn't great either). Just as 8.3 filenames are still with us, so too do historical path limits continue to hang about.

## \\\\?\\

[\\\\?\\ is the special prefix used by Windows to designate an extended path](https://msdn.microsoft.com/en-nz/library/windows/desktop/aa365247%28v=vs.85%29.aspx#maxpath). E.g. \\\\?\D:\\BryceIsRad. The \\\\?\\ prefix is used by the OS to signify an extended path, but is not part of the path. The actual wrangling on long paths in done under the hood, and the prefix just means that the OS will be passed to the system with little modification. There are ramifications to this, such as a paths using the \\\\?\\ prefix cannot be relative (all relative paths are limited by MAX_PATH).

This prefix can also be used with the [Universal Naming Convention](https://msdn.microsoft.com/en-us/library/gg465305.aspx), a Microsoft specification for paths to resources that may be on a network. In such cases the \\\\?\\UNC\\ prefix is used. E.g. \\\\?\\UNC\\BrycesServer\\BryceIsRad

And with all of this discussed with can move on to how it impacts git on Windows!

## core.longpaths

The core.longpaths config option is special to msysgit. If you grep through the git source you won't find a reference to it. And as a trap for new players, if you do a grep of the source for msysgit without checking out submodules you also won't find it. But if you grep the source of a msysgit with its submodules inited and updated, then you're in for a treat.

Msysgit has its own git submodule with modifications. This submodule lives in the git directory which hangs directly off the root of the msysgit directory structure. This is where the adventure starts:

git/config.c has the following change:

{% highlight c %}
if (!strcmp(var, "core.longpaths")) {
	core_long_paths = git_config_bool(var, value);
	return 0;
}
{% endhighlight %}

to handle the longpaths var being set. While you can set the var on a non msys version of git, it won't do anything, as there's no code to handle it.

Internally the core_long_paths variable is used to track if long paths are enabled. The variable is declared in environment.c which is described by a comment as follows:

*We put all the git config variables in this same object file, so that programs can link against the config parser without having to link against all the rest of git.*

Aside from that, the core_long_paths variable is used in a few other files, importantly mingw.h and mingw.c. It's used by the function xutftowcs_long_path, which is a special long path version of xutftowcs, which in turn is a function that converts UTF-8 names into UTF-16LE. This is [because Windows likes its wide chars at 16 bits](https://msdn.microsoft.com/en-us/library/windows/desktop/aa367308%28v=vs.85%29.aspx), and the path should be expressed in wide chars.

Diving further into the code one can find the handle_long_path function, which does kinda what it sounds like. handle_long_path has an arg, expand, which stores the value from core_long_paths, and is used to gate the creation of long paths starting with \\\\?\\. If the variable is not true, an error will be returned if the path is too long:

{% highlight c %}
if (!expand || result >= MAX_LONG_PATH - 6) {
	errno = ENAMETOOLONG;
	return -1;
}
{% endhighlight %}

and after this code is the juicy bit where long paths are created:

{% highlight c %}

if (buf[0] == '\\') {
	if (buf[1] == '\\' && (buf[2] == '?' || buf[2] == '.'))
		return len;

	wcscpy(path, L"\\\\?\\UNC\\");
	wcscpy(path + 8, buf + 2);
	return result + 6;
} else {
	wcscpy(path, L"\\\\?\\");
	wcscpy(path + 4, buf);
	return result + 4;
}
{% endhighlight %}

and these are the paths that will be passed to the Windows API by msysgit! Neat.
