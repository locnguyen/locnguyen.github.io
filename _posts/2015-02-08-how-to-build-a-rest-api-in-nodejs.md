Building a REST API in NodeJS is simple with a framework specifically made for the task.
[Eran Hammer](http://hueniverse.com) and the team at Walmart Labs created [HapiJS](http://hapijs.com) to connect their 
mobile platforms with a legacy Java backend. Sounds like complex integrations coupled with with large scale
maintainability requirements to me!

I use Hapi to build my platform APIs for that very reason. I'll demonstrate how simple it is to construct a good
foundaton for your REST API.

Today let's focus on the first step of creating a server. We'll go over the basics and touch on some handy features.

## Installing Hapi

First make sure you have a recent version of NodeJS installed. To get started let's create a new Node project with 
``npm init```. Just hit enter to accept all the defaults if you want.

{% highlight bash %}
npm init
npm install --save hapi nodemon
touch server.js
{% endhighlight %}

The ```--save``` flag will write the module dependencies to your package.json file. For local development I use
[nodemon](https://www.npmjs.com/package/nodemon) to run my API. It watches the project folder for changes and restarts
automatically.

## Creating a server

Open the server.js file and type in the following code:
<figure>
    <figcaption>File: server.js</figcaption>
{% highlight javascript linenos %}
var port = process.env.PORT || 9000;

var Hapi = require('hapi');

var server = new Hapi.Server();

server.connection({ port: port });

server.start(function () {
    console.log('Server started at port %d', port);
    console.log('Resistance is futile');
});
{% endhighlight %}
</figure>

No ceremony here! If you want you can provide a port number to start your API at. Otherwise it will default to port 9000.

{% highlight bash %}
PORT=9001 nodemon server.js
{% endhighlight %}

### Application settings

Real world projects usually have some kind of configuration file with environment specific values.
<figure>
    <figcaption>File: config.js</figcaption>
    
{% highlight javascript %}
module.exports = {
    "development": {
        "someProvider": "http://dev.some.provider.com"
    },
    "qa": {
        "someProvider": "http://qa.some.provider.com"
    }
};
{% endhighlight %}
</figure>

Instead of having ```require('./config.js')``` wherever you need configuration values, you can DRY up your code by 
giving your configuration values to the server when you call the constructor function.

<figure>
    <figcaption>File: server.js</figcaption>
{% highlight javascript linenos %}
var port = process.env.PORT || 9000;
var env = process.env.NODE_ENV || 'development';

var Hapi = require('hapi');

var server = new Hapi.Server({ app: require('./config')[env] });

server.connection({ port: port });

server.start(function () {
    console.log('app setings', server.app);
});
{% endhighlight %}
</figure>

### Multiple servers

Sometimes it's useful to partition your app into different servers. Maybe you want the admin part of your API to be
completely separate from your core services. A lot of times I need to run a proxy to legacy APIs too.

<figure>
    <figcaption>File: server.js</figcaption>
{% highlight javascript linenos %}
var port = process.env.PORT || 9000;
var env = process.env.NODE_ENV || 'development';

var Hapi = require('hapi');

var server = new Hapi.Server({ app: require('./config')[env] });

server.connection({ port: 9000, labels: ['core'] });

server.connection({ port: 9001, labels: ['admin'] });

server.connection({ port: 9002, labels: ['proxy'] });

server.start(function () {
    console.log('core', server.select('core').info);
    console.log('admin', server.select('admin').info);
    console.log('proxy', server.select('proxy').info);
});
{% endhighlight %}
</figure>


That's a wrap on some Hapi server methods I find useful. Next time we'll learn how to configure routes and add request
handlers.