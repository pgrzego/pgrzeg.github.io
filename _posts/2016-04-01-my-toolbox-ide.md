---
layout: post
tags: tools
title: My toolbox - IDE
comments: true
---
There are so many great tools available for any developer that it is hard to choose from. It takes a bit of time to play around with a new one to see if it suits your needs. And after you configure it and get used to it, it may be hard to check a new one as it will force you to change your habits. So for anyone who just sets up his/hers environment, here are is my toolbox and why I chose it.
<!--more-->
### The very beginning: PSPad ###
In the old days I had only three requirements for an editor:

1. PHP syntax highlighting.
2. Charset conversion (being Polish I had to struggle with ISO-8859-2, latin-2 and UTF-8).
3. Built-in FTP client (so I was able to edit files directly on the server - totally unprofessional right now, but well... these were the times when I wasn't doing any serious projects... anyway I will cover that matter in a later post).

That's it. I didn't want any other support. I felt like I was in total control of the code that way. My choice was [PSPad](http://www.pspad.com/) which was able to do all of that for me and then some: 

* it was available in many languages, 
* it had a portable settings file (in case I needed to reset my hard drive),
* it had plug-ins (not that I was using any, but just in case).

### Moving on to IDE: NetBeans ###
Later on I wanted to try something more complex. I've learned that serious programmers work with serious tools and these are called IDE. There were two free ones available: [Eclipse](https://eclipse.org/downloads/) and [NetBeans](https://netbeans.org/downloads/). I spent some tile researching which one is better and it all came down to the fact that NetBeans was more PHP oriented.

The problem was I was still doing simple stuff - small websites with a simplified CMS. I've had my pieces of code that I was reusing. I played a bit with OOP which started being available in PHP, but I wasn't using any patterns. So I wasn't really using NetBeans as a IDE. It was still an enhanced text editor for me as this was what I needed, but I wanted to get used to working with IDE just in case.

### Recent days: PHPStorm ###
I've had a break from web development and it seems I chose a bad time for that. My favourite PHP evolved a lot. Within few years it moved forward to support OOP much better with interfaces, namespaces and stuff. I had a lot to catch up.

At the same time I've learned a lot about theory of software development. I became to understand how it really works when you are a part of the team who works on the same project and develops multiple functionalities at the same time. I've learned that on JAVA with firstly Eclipse and then [Intellij](https://www.jetbrains.com/idea/). I got used to these small yet powerful functionalities:

* code completion,
* hints,
* quick help.

Some argue that you can work without them. Or that they are available in lightweight editors as well. And they are right. [Sublime Text](https://www.sublimetext.com/) is a trending piece of software which is a perfect example of that. It is extensible, it has a great community to help you get started and customize it to fit your needs. 

So why am I not using that? I chose to go with [PhpStorm](https://www.jetbrains.com/phpstorm/), because I wanted something ready, a piece of software that comes with all the functionalities built-in. Ready to go right after install process is done. The functionalities that I use regularly (on top of the ones mentioned earlier):

* Built-in console.
* Git versioning.
* Templates to start your new project with (with Composer just in case you need one that is not in the menu).
* Remote host (yes, but this time to edit files on integration server, not the production one).

**Important notice:** PhpStorm is not free. Being a student I am able to use academic licence which gives me 12 months licence for no costs. [There are some other discounts available](https://www.jetbrains.com/phpstorm/buy/#edition=discounts). If you can't use any then the initial price may be discouraging. I admit that.

### Summary ###
How I see it, the choice is for you to make. If you want an editor that is lightweight and which you want to be able to deeply customize to suit your needs, go with Sublime Text. If you prefer something ready and you are ready to pay for it, by all means go with PhpStorm.