So it's time I start blogging (again). I wrote earlier in my career and stopped caring. I really wish I hadn't because
I'd have 10 years worth of content right now.

[John Sonmez](https://twitter.com/jsonmez) just released [Soft Skills](http://www.manning.com/sonmez/) and I skipped
to the section about marketing myself as a developer. I've been working on personal branding for a year now by presenting
at local meetups and organzing [AngularJS-OC](http://www.meetup.com/AngularJS-OC/). John's arguments for blogging really
hit home though so that's why I'm here.

Let's not have anything to meaty yet for a first post though. A technical blog needs some code so here is something ultra
simple to get us started.

{% highlight javascript %}
var Hapi = require('hapi');

var server = new Hapi.Server();

server.connection({ port: 8080 });

server.route({
    path: '/poke',
    method: 'GET',
    handler: function (request, reply) {
        reply('Hello, world');
    }
});

server.start(function () {
    console.log('Api is up and running at ' + server.info.uri);
});

{% endhighlight %}