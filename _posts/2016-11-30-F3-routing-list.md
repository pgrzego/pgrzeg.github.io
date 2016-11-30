---
layout: post
tags: php F3
title: F3 routing list
comments: true
---
Since I have routing rules spread across different config files (one for each application module) I needed to have a single list of all endpoints in my REST server. Something similar to what we can get by running `debug:router` on a [Symfony console](https://symfony.com/doc/current/routing/debug.html). Here is the sniplet to achieve that.
<!--more-->

<script src="https://gist.github.com/pgrzego/cbd01c0ae8142299346a2af824f042f8.js"></script>