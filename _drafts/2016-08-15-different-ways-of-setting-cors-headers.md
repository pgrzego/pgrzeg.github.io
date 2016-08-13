---
layout: post
tags: tools
title: Different ways of setting CORS headers
comments: true
---
When you're building a backend server which will need to answer AJAX calls from different domains, you should know how to make sure that CORS will not be a problem. CORS stands for Cross Origin Resource Sharing (?) and it is a security mechanism implemented in the browsers. When there is an asynchronious call to an external server on a different domain, the browser firstly checks if the server expects calls like that. If not, the request will not be sent. This check is done by what is called a preflight request (?). The browser sends it to the same URL your regular request would be sent, but with an OPTIONS header (instead of your regular GET/POST/PUT/DELETE one). What the backend server is supposed to send back to this request is a list of domains fro; which he is ok to receive a cross origin requests. At the end the browser will check if the fron end domain is on this list. If everything is ok then the request will be clear to go.

Let's see how to set these response headers in the server configuration, in plain PHP and in some of the frameworks. You will be able to choose one of these methods at the end and implement it.
<!--more-->
