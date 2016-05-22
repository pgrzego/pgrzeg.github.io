---
title: Fat Free Framework error handler
tags: php
layout: post
---
[Official documentation for Fat Free Framework](http://fatfreeframework.com/framework-variables#AbouttheF3ErrorHandler) (in the version available at the day of writing this post) doesn't mention a very useful thing when creating a custom error handler (at least for me that is) - that you can pass additional parameters to the lambda expression. 
<!--more-->
So not only you can do this:

{% highlight php %}
$f3->set('ONERROR',
    function($f3) {
	// recursively clear existing output buffers:
        while (ob_get_level())
            ob_end_clean();
        // your fresh page here:
        echo $f3->get('ERROR.text');
    }
);
{% endhighlight %}

You can also for example do that:

{% highlight php %}
$log = new Log('logFile.log');
$f3->set('ONERROR',
    function($f3, $log) {
        // recursively clear existing output buffers:
        while (ob_get_level())
            ob_end_clean();
        // log your error in you log file:
        $log->write("There has been an error: ".$f3->get('ERROR.text'));
    }
);
{% endhighlight %}


> Written with [StackEdit](https://stackedit.io/).