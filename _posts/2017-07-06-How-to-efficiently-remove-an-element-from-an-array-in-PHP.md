---
layout: post
tags: php
title: How to efficiently remove an element from an array in PHP
comments: true
---
Recently I was looking for an efficient way to remove an element from a given offset in a given PHP array. And my first thought was to use the [array_splice](https://secure.php.net/manual/function.array-splice.php) method, but I began to wonder if it is the best option to do so. I searched through the net and I've stumbled upon a [Steven Vachon's webpage](https://www.svachon.com/blog/benchmark-php-array_splice-vs-unset-array_values/), where the author compared this option with another one: a combination of [unset](https://secure.php.net/manual/function.unset.php) and [array_values](https://secure.php.net/manual/function.array-values.php) methods. His benchmark proved that it was the latter that was over 10% faster. But I couldn't believe that a combination of two methods will be faster than a single one. So I've made my own benchmark.
<!--more-->

The code for my benchmark:

{% gist 65b8a2f98c97abf9dac11bd53cd846d4 %}

The results:

- Splice: 2.041 seconds. Array size: 10000
- Unset with array_values: 25.069 seconds. Array size: 10000

Splice is a clear winner.