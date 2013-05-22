---
layout: post
title: "Syntax Highlighting with Pygments is failing via Liquid Templates String Error"
author: Infong
category: Code
tags: [jekyll, Pygments, Liquid]
---

I'm using Jekyll to convert my markdown and Pygments for syntax highlighting.

Here is the error maruku displays:

    Liquid error: undefined method `join' for "\n song_info = []\n for song in songs:\n song_info.append(song.name) \n":String

The markup is as follows:

{% highlight python %}
    song_info = []
    for song in songs:
        song_info.append(song.name)                                                                                                                                   
{% endhighlight %}

Testing Pygments in iPython produces no errors.

So I reverted the liquid gem to version 2.2.2 as a workaround. Seems like a bug in the 2.3.0 version's pygments support, or Jekyll's use of it.

{% highlight bash %}
$ sudo gem uninstall liquid
$ sudo gem install liquid --version '2.2.2'
{% endhighlight %}
