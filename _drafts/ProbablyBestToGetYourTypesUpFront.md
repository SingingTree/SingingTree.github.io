---
layout: default
title: Probably Best to Get Your Types Up Front
---

So I was programming one of the old x86, 64, CISC, computer systems recently. I was programming it in Python, because I like to keep my Python skills sharp (ish), Python is pretty rad.

My project had previously been using Python 3.x, but I wanted to used OpenCV. Because a large amount of Python projects cannot commit the time to the massive technical debt which is changing all of the
{% highlight python %}
print "foo"
{% endhighlight %}
lines into
{% highlight python %}
print("foo")
{% endhighlight %}
lines, I was forced to switch back to Python 2.x.

There's another fun difference between Python 2.x and Python 3.x (but don't let this fool you, all the intertia in updating is the prints): Python 2.x performes integer divions when provided with two integer arguements, 3.x does not. I.e. 1 / 2 is 0 in 2.x, or 0.5 in 3.x.

Okay, so this is all a preamble to what I actually want to say: I like really explicit tyoes. If I want Python 2.x to do what I want I have to do something like this:
{% highlight python %}
1 / float(2)
{% endhighlight %}
or a similar shenanigan, which I'm all too familiar with from C like languages. And at this point I'm already worrying about the underlying type system, and I'd really just like to know what my types are earlier on. I'd like for it to be really explicit that I want the result of this computation to be a float. Traditional knowledge would suggest that converting one of these arguments to a float would render the result a float. I don't think that's a particularly intuitive idea, but it's one that I'm used to, and the above syntax thrills me a lot more than the following:
{% highlight python %}
1 / 2 * 1.0
{% endhighlight %}
which I'm all too familiar with, but I think is even less intuitive.

I think Python 3 takes a step in the right direction. 1 / 2 == 0.5, and that's what I expect.

But then I still have fun doing this in Python 3.x:
{% highlight python %}
print("Bryce guy out of ten?: " + 10)
{% endhighlight %}
which won't work, I need to explicitly create a string out of that 10 by calling str(10). I can't forget about the typing of 10, it's an int, and I can't just add that to a string, that makes no sense! I do like the idea of having to explicitly create a string here, beacause I believe that I should be aware of these types.

I've talked a swag about Python, but I use it primarily because it's on my mind (as you are always Python, I love you, bae). However, my opinion is more general: I care deeply about types, and I'm going to care when I'm writing the program, when I'm compiling it, when I'm running it, whatever. I want and need to understand the types of my data so that my program behaves in the fashion I expect it to. As such, I want my types up front for two, broad, related reasons (it's really just one reason):

- I want to explicitly state the types involved in my program, because these are the types I want
- I want safety checks provided, based on types so that weird stuff doesn't happen

but the real reason, and what I really want is

- I want to know if my implementation is wrong (and I'd really like to know sooner rather than later)