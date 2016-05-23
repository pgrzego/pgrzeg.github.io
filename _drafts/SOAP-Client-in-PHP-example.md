---
title: SOAP Client in PHP - example
tags: PHP backend
layout: post
---
I'm used to connect to other services through RESTful protocol. But there are other ways and recently I had to create an adapter which would send a request to a SOAP server and get its response. // Cos o SOAP ze to XML i w ogole //

I've started with a PHP page which give some useful tips which helped me to understand how the process works. And I had to make a choice. Since SOAP operates on XML files it is possible to build a request and parse a reply with simple XML // cos tam jak to sie nazywa //. But I've decided to go with a SOAP 