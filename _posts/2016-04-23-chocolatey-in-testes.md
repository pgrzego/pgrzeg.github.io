---
title: Chocolatey in tests
tags: tools
layout: post
---
It seems that a tool similar to yum or apt-get has been developed for Windows. [It's called Chocolatey](https://chocolatey.org/) and is available to download with one command:

{% highlight powershell %}

@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin

{% endhighlight %}

Having this you can install popular packages with simple command:

**Ruby**

{% highlight powershell %}
choco install ruby -y
{% endhighlight %}

**NodeJS**

{% highlight powershell %}
choco install nodejs -y
{% endhighlight %}

I'll give it a try and will let you know if there are any pros or cons.

[Update 2016-04-23]

So far so good. To install Ruby DevKit I didn't have to go through [install steps described on the Git readme page](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit). Instead I might just run:

{% highlight powershell %}
choco install ruby2.devkit
{% endhighlight %}

> Written with [StackEdit](https://stackedit.io/).