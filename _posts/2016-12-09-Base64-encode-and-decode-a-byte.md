---
layout: post
tags: php backend
title: Base64 encode and decode a byte
comments: true
---
PHP has two built-in methods which handle MIME base64 encoding and decoding: [base64_encode](https://secure.php.net/manual/en/function.base64-encode.php) and [base64_decode](https://secure.php.net/manual/en/function.base64-decode.php). They assume however that the values that you want to encode or decode are strings. In my recent development it wasn't the case. The value I wanted to encode and later decode to was integer. How to achieve it with what PHP offers?
<!--more-->

The answer to the above question is not that hard. PHP gives us a string which is a collection of chars. Each char can be converted to an integer with the [ord method](https://secure.php.net/manual/en/function.ord.php). Its range is 0 to 255. Which means what I needed to do is to change the base of the value I wante to encode from 10 to 256 (by dividing it by 256 and checking the division's remaining value) while encoding or do the opposite while decoding. Here is the code:

{% gist cbd01c0ae8142299346a2af824f042f8 %}