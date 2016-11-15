---
layout: post
tags: bash
title: Useful bash command to limit listed files
comments: true
---
When I was going through a directory which holds a significant number of files with data received from an external sources (so the file names and their content is similar) I needed a less common set of `ls` options.
<!--more-->

### Limit number of listed files ###

{% highlight bash %}
ls -tla /path/to/directory/ | head -n 15
{% endhighlight %}

The command above lists 15 most recently modified files.

### List file names with a specific number of characters ###

{% highlight bash %}
ls -la /path/to/directory/??????????
{% endhighlight %}

This command will list only the files which file names have exactly 10 characters (including extension).