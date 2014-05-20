---
layout: post
title:  "Backbone.JS Tutorial part 1"
date:   2014-03-02 22:07:58
comments: true
categories: learning
tags: [backbone, javascript, tutorial]
---

<h1 id="introduction">introduction</h1>
This will be a series of posts focusing on [Backbone.js](http://backbonejs.org). It is an event-driven client-side framework that enables today's web-app developers to deliever responsive dynamic Single Page Applications (SPA).

The examples given will be on the context of a crowd-sourcing site where your visitors can search and populate your database with books. And yes, it will make you millionaire, even if you don't have a monetization strategy.

Take into account that you have many ways to code the architecture and the logic by which you glue all the components. Always take what you read on the intertubes with a grain of salt ;-)

#components

Backbone.js is a sum of several components: Models, [Collections](/learning/backbone-tutorial-2), [Views](/learning/backbone-tutorial-3) and Router. On this blog post I'm going to focus on Models.

<h1 id="models">Models </h1>
Models represent the main entities on your web-app. In our case this will be Books. So, a Book could be something like:

{% highlight javascript %}
var Book = Backbone.Model.extend({
  urlRoot: '/books',
  defaults: {
      'type':'hardcover'
    %}
});
{% endhighlight %}

Fuck yeah! You've got a Book Model!
Backbone.js gives you off the shelf - pun not intended - some cool stuff you can do.

If you create a new Book:

{% highlight javascript %}
var my_book = new Book({
  id: 1,
  type: 'paperback',
    title: 'Siddharta',
    author: 'Herman Hesse'
});
{% endhighlight %}

You'll have a new Book with the attributes you specified.
You can mess around with the attributes:

{% highlight javascript %}
> my_book.get('author');
< 'Herman Hesse'
> my_book.set('author','Douglas Adams');
> my_book.get('author');
< 'Douglas Adams'
{% endhighlight %}

It is also possibe to set many attributes:

{% highlight javascript %}
my_book.set({title: 'Fahrenheit 451', author: 'Ray Bradbury'});
{% endhighlight %}

It is always possible to get the JSON of this model through `toJSON()`:

{% highlight javascript %}
> my_book.toJSON();
< { title: 'Fahrenheit 451', author: 'Douglas Adams', type: 'paperback'}
{% endhighlight %}

So far so good. Now onto the cool stuff. Let's assume you have an REST API for your books, witch the endpoint is `/books` (yup, you guessed it, this is where the attribute `urlRoot` comes in handy!).

Backbone.js gives you three extremely useful methods to interact with your API: `save`, `fetch` and `destroy`.

<h2 id="save">save()</h2>
Following our example, executing:

{% highlight javascript %}
> my_book.save();
{% endhighlight %}

Will trigger an HTTP POST request to the server's endpoint `/books`. This POST will send the JSON representing the attributes in it's body.

If your API endpoint returns the created model, that should have an `id`, the model will automatically update itself with the id:

{% highlight javascript %}
> my_book.get('id');
< 1
{% endhighlight %}

Subsequent `save()` requests will trigger a HTTP PUT request to `/books/1` to update the model:

{% highlight javascript %}
> my_book.set('number_of_pages':251);
> my_book.save();
> my_book.get('id')
< 1
{% endhighlight %}

An alternative would be:

{% highlight javascript %}
> my_book.save({'number_of_pages':251});
{% endhighlight %}

The later format allows you to patch attributes. This means Backbone will only send the specified attributes to the API endpoint instead of the whole model:

{% highlight javascript %}
> my_book.save({'number_of_pages':251},{patch: true});
{% endhighlight %}

<h2 id="fetch">fetch()</h2>
Fetch triggers a HTTP GET request to the API endpoint for the Book model.

{% highlight javascript %}
> my_book = undefined;
> my_book = new Book({id:1});
> my_book.fetch();
> my_book.get('title');
< 'Fahrenheit 451'
{% endhighlight %}

This can be used when in a variety of contexts:
- Need to reflect server side changes on the model
- Get the model when you only have the model `id` and need to populate the remaining attributes
- Someone hits "Undo" on the webapp
- On a polling mechanism.
##destroy()
Destroy instructs your API to delete a model via a HTTP DELETE request. In our case, this request would be sent to `/books/1`.

{% highlight javascript %}
> my_book.destroy();
{% endhighlight %}

<h2 id="callbacks">success and error callbacks</h2>
On all 3 REST requests you can pass a Hash to handle success and error results from the API:

{% highlight javascript %}
> my_book.save(
  { 'title' : 'new title' %},
    {
      patch: true,
        success: function(model, response){
          console.log("Great success!");
        %},
        error: function(response){
          console.log("Doh!");
        %}
    %}
);
{% endhighlight %}



<h2 id="events">events</h2>
One of the most awesome things about Backbone.js are events. You have asynchronous signalling of model and collections changes through your app for free.

I'll dig deeper on this on the post for Backbone.View, but just keep in mind that every time you `set`, [`unset`](http://backbonejs.org/#Model-unset), [`clear`](http://backbonejs.org/#Model-clear), `save`, `destroy` and `fetch` a model, an event will be triggered. The following events can be triggered: `"change"`, `"request"`, `"sync"`,`"destroy"` and `"invalid"`.

It is possible to not trigger this event by specifying you want to be `silent`:

{% highlight javascript %}
> var Foo = Backbone.Model.extend({});
> var bar = new Foo();
> bar.set({'shizzle': 'dizzle'}); // triggers an event
> bar.set({'mizzle': 'srizzle'}, {silent: true}); //doesn't trigger an event
{% endhighlight %}

And that's it for `Backbone.Model`! [Next post](http://trenpixster.ghost.io/learning/backbone-tutorial-2/#header) will be about `Backbone.Collection`, which is a container for `Backbone.Model`s.

Feel free to leave props or flames in the comment section ;-)