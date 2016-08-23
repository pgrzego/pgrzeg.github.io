---
layout: post
tags: backend
title: Useful HTTP status codes
comments: true
---
Each backend server should return responses which are as descriptive as possible for any type of client - whether it is a browser or a frontend application or website. It is not sufficient to always return all the information just as a JSON object and, depending on the final outcome, have `{ "success": true }` or `{ "success": false }`. We shouldn't forget about the other way to signal if the request was processed correctly: HTTP status codes. Here is a list of which ones to use depending on the request type and the information that is sent back.
<!--more-->

### GET ###

GET is used to fetch an existing resource from the server. Nothing is expected to change on the back end side - every existing resource should remain as it was before.

**Responses**

*200 OK*

If everything went fine, resource was found and returned correctly, this is it. Return OK in the header and the resource in the body of the response. ***Notice:*** Sometimes you can get this response with an empty body. It doesn't mean that the resource was not found. It means that the resource is empty. Example: in a bookstore every book should have an author, yet not all of books have authors assigned. When you will query to get an author for a book like that, you should get an OK response (meaning "yes, there is a book and therefore there is an author"), but with an empty body ("we don't know yet anything about this author").

*404 Not Found*

If resource was not found and was not expected to be found here.

*400 Bad Request*

If some additional parameters, related to the way information is presented, are not formed correctly. For example, pagination or sorting is requested, but not like a server would expect it to be.

### POST ###

POST is there to create a new resource. Server should expect all the information in a body of the request. ***Notice:*** It is a better practice to send the data as a raw, not as x-www-form-urlencoded. The same goes for the server - raw should be the default form to handle. The other should be optional.

**Responses**

*403 Forbidden*

When request tries to force something that is not supported or is not allowed. The best example for the latter is when there is an ID of the resource defined in the body of the request. If a back end server doesn't allow it, this is the reply.

*201 Created*

This one is the default one if a resource has been created successfully. The back end server should also add a `Location` header which points to the resource (it should be a URL which can be used with a GET request to retrieve this resource). The resource itself should be in the body of a response.

*202 Accepted*

This one is used when the request was ok, but the resource will be created later.

*204 No Content*

Similar to `201 Created` (which means that the resource has been created successfully), but when for some reason the server doesn't want to return this resource. So it sends an empty response with this code. It is also used if the created resource can't be identified by a URI.

*409 Conflict*

When the resource already exists or when the type of resource does not match the information given in a body of the request.

### PATCH ###

Used to update an existing resource. The body may contain the entire resource object or just these properties which should be updated.

**Responses**

*204 No Content*

The default response if everything was done correctly. No other information, nothing in the body of the response.

*202 Accepted*

The request is correct, but it will be processed later on or it hasn't been finished by the time the response was sent.

*200 OK*

The request has been accepted, the changes have been implemented, but something else was updated. For example, a hashed representation of the resource or an `updated at` field. The updated resource should be sent back in a body of the response.

*403 Forbidden*

Sent back when the request wanted to ask server to do something which was not supported.

*404 Not Found*

Can't update a resource that doesn't exist...

*409 Conflict*

This one should be expected when new values of resource parameters violate some rules. For example, a book may have a unique ISDN number and the updated one is exactly like one assigned to another book.

### DELETE ###

This type of request is used to ask a server to remove a resource.

**Responses**

*204 No Content*

The request was processed successfully. The resource is removed and there is nothing to send in a body of the response.

*202 Accepted*

Everything is ok, but the removal process takes some time, so it wasn't finished by the time the response was sent.

*200 OK*

The request was processed successfully, but there is something which can be sent back in a body of the response.

### Other errors ###

*405 Method Not Allowed*

Sent by the server when the request method is not among ones that the server accepts.

*500 Internal Server Error*

Sent by the server which was unable to complete the request, because something unexpected has happened.

### Resources ###

* [Wikipedia](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) - always a good starting point
* [W3 organisation website](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) - when you want to go directly to the source
* [Description at AddedBytes](https://www.addedbytes.com/articles/for-beginners/http-status-codes/) - well done summary
* [JSON API](http://jsonapi.org) - a standard on how the REST API server should respond if JSON was chosen as a form of communication
* [Decision diagram](https://raw.githubusercontent.com/for-GET/http-decision-diagram/master/httpdd.png) - the latest version of a decision diagram which includes more responses for more complex use cases