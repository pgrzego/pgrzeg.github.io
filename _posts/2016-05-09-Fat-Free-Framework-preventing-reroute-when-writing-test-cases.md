---
title: Fat Free Framework - preventing reroute when writing test cases
tags: PHP backend
layout: post
---
There are sometimes these small things which can make your life miserable. But then when you stumble upon one you see that the problem has been identified by the makers and it is easily manageable. And you're grateful that it's done and you don't need to figure out how to resolve it without tickering with the source code.

This is what happened to me this morning when I was writing another test case for a Fat Free Framework based RESTful API server. I was [mocking a query](http://fatfreeframework.com/unit-testing#MockingHTTPRequests) which, if provided correct data, was redirecting to another address. I was firstly trying to play with clearing headers like it was suggested on [Stackoverflow by Salman A](https://stackoverflow.com/questions/6123626/php-header-location-gets-sent-even-inside-an-output-buffer). But for some reason it wasn't working for me (I guess I was clearing wrong headers, but it doesn't matter now). And finally I've looked into the code of `reroute` function of the [base class](https://github.com/bcosca/fatfree/blob/3.5.1/lib/base.php).

It starts with a check on whether there is a handler defined! This was this moment when I knew my prolem was resolved already and I just needed to create a simple handler:

{% highlight php %}

$f3->set('ONREROUTE',function($url,$permanent){
    return true;
});

{% endhighlight %}

Problem solved.