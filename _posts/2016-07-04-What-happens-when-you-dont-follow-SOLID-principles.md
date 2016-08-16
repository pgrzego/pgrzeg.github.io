---
layout: post
tags: backend solid
title: What happens when you don't follow SOLID principles
comments: true
---
[SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles are here to help us, programmers, to write a clean code. All of them are more like guidelines as really the code will work even if they are not strictly followed. And there is this ["Done is better than perfect"](http://lifehacker.com/5870379/done-is-better-than-perfect) rule as well. But the truth is that the more you will follow SOLID, the less painful refactoring will be for you. Let's see an example of that.
<!--more-->

*Side note:* Since only an explanation of the current, faulty implementation has grown into a regular post, I will describe ways to fix it in the following parts.

### Example: a class to manage mailing

I was asked to code a class which will be used to manage mailing - prepare it, store it and send it in turns. It was supposed to work with defined templates which were accessible by RESTful API.

I've created two files: one with a Mailing class and the other which holds database queries for each template. Let's take a look at the UML sequence diagram describing how the flow looked like for a task of getting the list of mails prepared for a particular mailing:

![UML diadram](http://plantuml.com/plantuml/png/ZLEnSi8m3Dtz5Hh97PntXWuq7RYZxVLWxEB48hWrDhLbslnzROU6KCArcMJTqzDxaaAADCAwtg4CMfa6za9f3pCmbc3zl5gMJ0Io9kmhT4pKP1q47yFQ6d9MP_nz5-kOHaEAsfnz2UWKUYO5YKfuX7B1AXkC5Au5mlr12y87qoY3PqmgZ39YRK2M6i7ixbUFEU0Nre7mrKmu9-7VEPzohfMdfUIyO9VmCO86QPNWPcipoRassaPmgjaHsErLdKEAbexeWVEFQAzDHTFu-B7p9FR8-OY2IxzPmUQKwbLSaXJiiWPc8qOuzw5wmvew6HwDHDV5H-6Kg3JwKJs31MKnAMud0rVT64-G6xqpU9I-NFHRclIuWfMB6qnfqr4eV2hQmL7e47jkuwdr9fYszf0lppE8tzyJTdaMLsFjHfCQ9469wbzZhgnCJiTR3uzs3aBAnvZOsFoUTEPE-nakgwCiuYyZlm40){: .callout}

The class in the first file acts both as the controller and an executant of the operation (this here is already a problem, but I will perhaps present another use case later which will describe hot to deal with that, so I'm not going to elaborate on this here).

The Mailing class is asked to prepare a list with a given ID (action **1**). In order to do this, it needs to do five things:

1. Get the coordinates of the template of the email.
2. Retrieve the template params.
3. Retrieve data to fill into the template.
4. Render the template.
5. Dump the mail info (it will be picked up later on by a cron script which sends bundles of stored mails).

Let's go over this step by step.

### Get the coordinates of the template of the email

The class has a built-in array like this:

{% highlight php %}
<?php

private $templatesInfo = array (
        32 => array (
            'templateID' => "Sample Template",
            'query' => MailingQueries::SAMPLE_QUERY
        ),
/* ... */
)
{% endhighlight %}

Each entry of the table holds the `templateID` which is used to grab the template and the `query` which is used to grab the database query. This way it can access info needed for the next activities (action **2** of the UML file).

It seemed like a handy thing at first, but immediately turned against me. *Each time I wanted to add a new template, I needed to update the class*. Terrible idea. And the first reason why this design turned out to be far from perfect.

### Retrieve the template params

This one is pretty straightforward. Mailing class needs to ask template engine class for some details about the template: its subject, its default sender and a set of variables which are needed to fill it. These are actions **3** and **4** of the UML schema.

This one doesn't have to blow up in my face quickly, but there is also a room for improvement here as well. The thing is that this method queries the chosen template engine. What happens when there is going to be a need to change it? Let's say that right now it is an internal database which holds all the template infos. What if at later stage the choice will be different and these infos will be stored in the cloud or there is going to be an external mailing provider who will keep them for me? *This should be refactored as an adapter to a template engine*.

### Retrieve data to fill into the template

The next step is to retrieve a list of users who are eligible to receive a mailing together with all the variables that will be set in the template. In my implementation all the info needed to get that list was kept in the local database. So getting this was as simple as running a dedicated query. And at this point already we can see that this implementation has the same problem as the one in a previous step - it is dependent on the provider yet it doesn't have an adapter.

In order to keep the SQL queries outside of the main mailing class, I have created a different one to store them. The link between a template and its corresponding query is kept in the array described above (with `'query' => MailingQueries::SAMPLE_QUERY`). Getting the query is illustrated as actions **5** and **6** of the UML file. The query needs to return values with keys that correspond to template's variables. So when a template is like this:

{% highlight html %}{% raw %}
<h1>Hello {{NAME}}</h1>
<p>You have {{POINTS}} points in our system. You can use them to get our products at discounted prices.</p>
<!-- ... -->
{% endraw %}{% endhighlight %}

The query needs to return `NAME` and `POINTS`:

{% highlight sql %}
SELECT
	u.user_name as NAME,
	SUM(o.points) as POINTS
FROM
	users as u
	LEFT JOIN orders as o ON (u.id=o.user_id)
/* ... */
{% endhighlight %}

This process is presented as actions **7** and **8** of the UML file.

This solution worked as long as the queries were simple. But I've just been asked to define a new template and retrieve params for it. In order to get these params in a single query, the query was around 1000 lines long! Maybe it could've been optimised or I could've defined some views in the SQL database, but it was troublesome anyway. It needed to be changed.

### Render the template

This step is very similar to a **Retrieve the template params**. Having all the variable values, I ask the template engine to render the template and I expect a body of the email in response. As it is similar to one of the previous steps, it also shares its flaw: it is dependent on the provider. On top of that, the way this process is designed, forces controller to render the body even though it shouldn't be doing that. But that's for later.

### Dump the mail info

This is the last step - action **11** of the UML file. The idea is that the process of sending an unknown number of emails may take some time. Therefore, I wanted to separate it from the process of preparing mails. In this step I take each rendered body of the mail and I put it together with all other parameters (receiver and subject of the mail) to a text file. The other process will take these mails on a regular basis one by one and will be sending them.

### Summary

I've covered the process of sending mailings as it was designed initially. The process was working correctly, but had some limitations which became problematic sooner than I expected. And since the design did not follow the SOLID principles, I need now to refactor it. I will try to address each of the exposed flaws and then some as the fixes will impact the overall architecture which will need to be rewritten almost from the scratch. I will describe it in the following posts.

----------
UML diagram was made with [PlantUML](http://plantuml.com)