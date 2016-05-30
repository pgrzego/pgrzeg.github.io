---
title: How Dependency inversion may ease your life
tags: php backend solid
layout: post
---
Recently I wanted to improve the way I store logs in my solution and I've encountered a problem. Why? Because even though I always try to keep my code as clean as it's reasonable, there is always a way to do it better. This time it was a case of ignored [Dependency inversion principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle). Most of the modules I create is equipped with a logger. I want to be able to trace the problems and I also want to be able to reply to users about what might have happened when they have encountered some difficulties while using the web service. Since I am using [Fat Free Framework](http://fatfreeframework.com/) it was only natural to rely on [its built-in logger](http://fatfreeframework.com/log).
<!--more-->

### Problem

My idea so far was to use one Logger per class with its own log file. When I use a `write(__METHOD__." received request to process ".$paramName.".")` method, it creates a line like this:

{% highlight text %}
Fri, 20 May 2016 13:32:19 +0200 [127.0.0.1] User::exampleMethod received request to process paramValue.
{% endhighlight %}

Which was enough at first. So I gave each class its own Logger, like this:

{% highlight php %}
<?php

class User {

    private $logger;

    function __construct() {
        $this->logger = new Log('userLog.log');
    }
/* ... */
}
{% endhighlight %}

I did the same with an abstract class I use as a template for all [SQL Mappers](http://fatfreeframework.com/sql-mapper):

{% highlight php %}
<?php

class SQLMapper extends \DB\SQL\Mapper {
	protected $logger;

	/**
     * SQLMapper constructor.
     * @param \DB\SQL $db
     * @param string $tableName
     * @param string $columnPrefix
     */
    public function __construct(\DB\SQL $db, $tableName, $columnPrefix="") {
        parent::__construct($db,$tableName);
        $this->tableName = $tableName;
        $this->columnPrefix = $columnPrefix;
        $this->logger = new \Log('mapper'.ucfirst(strtolower($tableName)).'.log');
    }

/* ... */
}
{% endhighlight %}

As I've said it worked for me at first. The problem I have right now is that I would like to enrich the original Logger. In order to track user actions better, I will definitely need a more precise timestamp (I will do that with `microtime()` function). Maybe I will also add the headers which are sent with an API request. In general I am going to use a Decorator pattern. And in order to do so, I will need to redo all the current declarations of classes which use Logger.

On one hand I could just say that I was not able to predict that I will need to do something like that. But there is one thing I could've done to avoid that anyway: use Dependency inversion principle coupled with Dependency injection pattern. These two would let me stay future proof.

### Dependency inversion principle (DIP)

In order to blindly follow DIP I should create an interface which would allow me to decouple `Logger` class from all the high level modules that use it. Something like this:

{% highlight php %}
<?php
interface iLogger {
    public function write($text);
}

/* ... */

{% endhighlight %}

The thing is that PHP does not use static type checking. "The term [duck typing](https://en.wikipedia.org/wiki/Duck_typing) is now used to describe the dynamic typing paradigm used by the languages like PHP" ([Wikipedia](https://en.wikipedia.org/wiki/Strong_and_weak_typing)). Introducing this interface would change nothing in the implementation of the `SQLMapper` class I defined earlier. What does it give me then? It can help to remember which methods need to be defined in implementations of the `iLogger` interface. So the next time I will get a requirement that will force me to create a new version of a class that collects logs, I can just take implement this interface and I will be 100% sure my class will have all the methods, that are used by external classes, defined.

I will let you decide whether you want to have it or not. Counter argument is that I try to create an interface for a class that already exists and which is not going to implement it (I'm talking about the original `Logger` class defined by Fat Free Framework).

### Dependency injection pattern (DI)

This will help me a lot to achieve what I need. The idea here is to use the `iLogger` type of class which will be "injected". I will not declare it inside the class anymore. Instead, I will expect the class that needs to use my class to provide the `iLogger`:

{% highlight php %}
<?php

class SQLMapper extends \DB\SQL\Mapper {
	protected $logger;

	/**
     * SQLMapper constructor.
     * @param \DB\SQL $db
     * @param string $tableName
     * @param iLogger $logger
     * @param string $columnPrefix
     */
    public function __construct(\DB\SQL $db, $tableName, $logger, $columnPrefix="") {
        parent::__construct($db,$tableName);
        $this->tableName = $tableName;
        $this->columnPrefix = $columnPrefix;
        $this->logger = $this->setLogger($logger);
    }

    public function setLogger($logger) {
        if ( is_object($logger) && property_exists($logger, "write") ) {
            $this->logger = $logger;
        } else {
            // Throw exception
        }        
    }

/* ... */
}
{% endhighlight %}

There are [three ways to inject a dependency](https://en.wikipedia.org/wiki/Dependency_injection#Three_types_of_dependency_injection). I've decided to go with *constructor injection*, because I need logger to be defined. Logging an information in the code should be a simple operation. I don't want to deal with exceptions when logger is NULL, because it wasn't injected with a *setter injection*.

### Conclusion

With a `Logger` class being injected, I can change it without touching all these classes that use it anymore. This will fulfil another important SOLID principle: Open/Closed Principle (in other words: if it works, don't touch it).

### Further reading

1. [An Absolute Beginner's Tutorial on Dependency Inversion Principle, Inversion of Control and Dependency Injection](http://www.codeproject.com/Articles/615139/An-Absolute-Beginners-Tutorial-on-Dependency-Inver) by Rahul Rajat Singh
2. [Dependency Injection in PHP. Create your own DI container.](http://krasimirtsonev.com/blog/article/Dependency-Injection-in-PHP-example-how-to-DI-create-your-own-dependency-injection-container) by Krasimir Tsonev
3. [Symphony DependencyInjection Component](https://symfony.com/doc/current/components/dependency_injection/index.html)