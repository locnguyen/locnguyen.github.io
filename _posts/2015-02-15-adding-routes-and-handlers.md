---
layout: post
title: Add Routes and Handlers to Hapi
description: 
modified: 2015-02-01
tags: [hapijs, nodejs, rest, api]
image:
  feature: abstract-5.jpg
---

In the first [post]({% post_url 2015-02-08-how-to-build-a-rest-api-in-nodejs %}) of the HapiJS series we learned how to
use the server interface to build a server. Today let's make an API we can use by adding routes and request handlers. 
Create a new file like so:


<figure>
    <figcaption>File: server.js</figcaption>
{% highlight javascript %}

var Hapi = require('hapi');

var server = new Hapi.Server();

server.connection({ port: 9000 });

server.start(function () {
    console.log('API up and running at %s', server.info.uri);
});
{% endhighlight %}
</figure>

Because HapiJS is designed for configuration over code, adding routes is just a matter of passing a JavaScript object 
to the `server#route` function.

<figure>
    <figcaption>File: server.js</figcaption>
{% highlight javascript %}
server.route({
    path: '/lotto-numbers/{unixDate}',
    method: 'GET',
    handler: function (request, reply) {
        console.log('headers as obj', request.headers);
        console.log('query params as obj', request.query);
        console.log('client IP is %s', request.info.remoteAddress);
        console.log('path param unixDate is %s', request.params.unixDate);
    
        reply({ numbers: '1 12 36 22 50 2' });
    }
});
{% endhighlight %}
</figure>

You can follow how easy it is to add a route:

1. Providg a URI for the path
1. Specify the HTTP method to respond to
1. Write a handler function for the incoming HTTP request

The first parameter in the handler function is Hapi's [request object](http://hapijs.com/api#request-object). It
encapsulates the data you would expect to have access to like headers, query parameters, and path parameters but includes
other goodies that are part of the Hapi ecosystem too. I suggest looking at the documentation to see what else is 
available to you.

<figure>
    <figcaption>File: server.js</figcaption>
{% highlight javascript %}
server.route({
    path: '/bankaccount/{id}/deposit',
    method: 'POST',
    handler: function (request, reply) {
        // A JSON request body might look like { "amount": 100000000 }
        var amount = request.payload,
            accountId = request.params.id;
        
        var deposited = BankAccount.deposit(accountId, amount);
        if (deposited) {
            reply({ status: 'deposit complete').status(201);
        }
        else {
            reply({ status: 'deposit incomplete' }).status(500);
        }
    }
});
{% endhighlight %}
</figure>