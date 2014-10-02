backbone-pusher
===============

Extension for [Backbone](http://www.backbonejs.org) Collections, just a mixin, that makes for easy-peasy websockets integration with [pusher.js](http://www.pusher.com). 

Make your backbone collection listen to real-time data from pusher. Easy to configure and integrates well with most backbone frameworks & plugins

> I've used this with tools that extend, modify, or wrap Backbone.Collection like Backbone-Deep-Model, Thorax.js, and Chaplin.js with no known issues. Note that does not mean there aren't any.. 

Oh yeah, this needs re-factored, etc.. My current pipeline auto-minifies the assets but I'll minify and upload a dist version soon.

Features
========

+ Automatically adds, updates, and/or removes models from your collection based on the `message` received. e.g. server sends a `add_message` and the plugin will receive that data on the add event listener and update the collection with the new object.
+ Super easy to integrate to an existing Collection. 
+ Filters can be configured in `options` to enable real-time filters based on the models attributes. See __Filters__ below. 


Install & Configure
===================

Clone or download the repo and include the `backbone-pusher.min.js` in your index.html file after Backbone, underscore (or lodash), and the pusher.js script. It does **not** depend on jQuery but does depend on underscore and obviously, Backbone. You also do not need to do any pusher configuration directly as this wraps all of that - just pass your pusher key and the channel name to listen on.

> You can download the latest pusher client source [here](https://github.com/pusher/pusher-js/blob/master/dist/pusher.min.js) or get instructions [here](http://pusher.com/docs) 

In your collection(s) initialize method simply use underscore's `extend` method to mixin all of the BackbonePusher properties & methods onto your collections prototype like so:

```javascript

	var posts = Backbone.Collection.extend({
        model: post,

        initialize: function() {
            // mixin Backbone-Pusher prototype properties
            _.extend(this, BackSocket.prototype);

            // use Backbone-Pusher for websockets on this collection
            if (_.isFunction(this.live)) {
                this.live({
                    key: 'pusher-key',
                    channel: 'yourApp',
                    channelSuffix: 'channel',
                    messageSuffix: 'message',
                    autoListen: true,
                    logEvents: true,
                    logStats: true,
                    filters: {
                        status: 'archived'
                    }
                });
            }

            return this;
        }
    });

```


Filters
=======

You can configure backbone-pusher to check incoming real-time data for model fields with specific values and have them filtered (or removed) from the collection. 

e.g. Lets imagine that our collection is wired to an API endpoint for `posts` and that we want to ensure when data comes over pusher its not stale or something we don't want.. Thus we want to filter on the posts model field `status` and remove any posts that are `archived` (or something similar). During configuration pass the `filters` key in the options with `status: "archived"` like:

```javascript

	// inside of our Posts Collection initialize method
	initialize: function() {
        // mixin websockets object
        _.extend(this, BackbonePusher.prototype);

        // instantiate Backbone-Pusher for websockets on this collection
        if (_.isFunction(this.live)) {
            this.live({
                key: 'pusher-key',
                channel: 'yourApp',
                messageSuffix: 'message',
                autoListen: true,
                filters: {
                    status: 'archived'
                }
            });
        }

        return this;
    }

```


History
=======

I started using [pusher's](http://www.pusher.com) real-time WebSockets as a service platform almost 2 years ago. I originally needed a **simple** way of integrating new web & hybrid apps with pre-existing products based on various, older server-side solutions. Pusher made life simpler & after a couple of projects I decided to integrate a more reusable solution for my backbone based projects that needed real-time'y data. Pusher is a great abstraction, the *why* is well explained [here](http://www.leggetter.co.uk/pusher/pusher-presentations/why_use_pusher.html#1) by Phil Leggetter. Further, none of these existing solutions were built on [node](http://www.nodejs.org), I had just a little wiggle room on the server (enough to implement pushers server libs), and implementing a node & socket.io based solution to work in parallel was more complex & costly...(time is money, productivity, blah blah... s'all true I guess and thats all the beancounters care about.. what can you do? ;)  except maybe quit your job, lock yourself in your mom's basement for a year, & hope that idea you're building is worthwhile before she kicks you out)