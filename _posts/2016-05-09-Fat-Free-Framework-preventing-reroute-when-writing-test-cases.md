---
title: Fat Free Framework - preventing reroute when writing test cases
tags: PHP backend
layout: post
comments: true
---
There are sometimes these small things which can make your life miserable. But then when you stumble upon one you see that the problem has been identified by the makers and it is easily manageable. And you're grateful that it's done and you don't need to figure out how to resolve it without tickering with the source code.

This is what happened to me this morning when I was writing another test case for a Fat Free Framework based RESTful API server. I was [mocking a query](http://fatfreeframework.com/unit-testing#MockingHTTPRequests) which, if provided correct data, was redirecting to another address. I was firstly trying to play with clearing headers like it was suggested on [Stackoverflow by Salman A](https://stackoverflow.com/questions/6123626/php-header-location-gets-sent-even-inside-an-output-buffer). But for some reason it wasn't working for me (I guess I was clearing wrong headers, but it doesn't matter now). And finally I've looked into the code of `reroute` function of the [base class](https://github.com/bcosca/fatfree/blob/3.5.1/lib/base.php).

<!--more-->
It starts with a check on whether there is a handler defined! This was this moment when I knew my prolem was resolved already and I just needed to create a simple handler:

{% highlight php %}

$reroute = "";

$f3->set('ONREROUTE',function($url,$permanent){
    $GLOBALS["reroute"] = $url;
    return true;
});

{% endhighlight %}

Now I can test if after the tested method was processed, the `$reroute` variable holds a url of a site that the method wante to redirect to:

{% highlight php %}
/** @var Base $f3 */
$f3 = require("internal.php");
// Define a routing rule:
$f3->route('GET /user','User->display');

$test = new Test;
// Execute a test case:
ob_start();
$f3->mock('GET /user');
ob_end_clean();

$test->expect( $reroute=="/", "Module redirects correctly user who is not logged.");

{% endhighlight %}

### Update
There is one flaw with this approach - if I override `reroute` function with my custom one, then I don't kill original script. So if originally I assumed that Fat Free Framework kills it inside its `reroute` function (and it does in the end), then I ended up with my method looking like this:

{% highlight php %}

private function validateUser($f3) {
	// Perform a validation based on session, headers or any other solution
	if ( !$logged ) $f3->reroute("/");
}

private function display($f3) {
	$this->validateUser($f3);

	// And here all the stuff is done assuming 
	// that user is logged in as otherwise 
	// the reroute function would be executed 
	// which in the end kills this process 
}

{% endhighlight %}

In this case I have a problem. Because if I will overwrite `reroute` function, my `display` function will not be stopped if user is not logged in. Instead it will firstly do all the validation, but later it will proceed to do all the other things that are declared later. I can't see any good solution out of this problem - the only thing that can be done here is to either let function execute the remaining part of the code (which may cause some unexpected behaviour like executing DB queries with random data) or change the original code of the method which is against the policy "if it works, don't touch it". I am going to go with the second option.