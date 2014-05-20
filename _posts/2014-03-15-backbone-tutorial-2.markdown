---
layout: post
comments: true
title:  "Backbone.JS Tutorial part 2"
date:   2014-03-15 22:07:58
categories: learning
tags: [backbone, javascript, tutorial]
---

Here's the 2nd post on Backbone.js. If you missed the first one check it out [here](/learning/backbone-tutorial-1/#header).

This time I'll cover the `Backbone.Collection` component, it's basics and how useful they can be.

<h1 id="collections">Collections</h1>


Collections represent an agregation of Models. The collection's models can either be an object (which will be translated into Backbone Models):


{% highlight javascript %}
> var RawCollection = Backbone.Collection.extend({});
> var foo = new RawCollection([ {bar:"1"},
                        {bar:"2"} ]);
{% endhighlight %}

<p id="collection_create">Or an existing model, in this case, our `Book`:</p>

{% highlight javascript %}
> var BooksCollection = Backbone.Collection.extend({
  model: Book,
    url: '/books'
});
{% endhighlight %}

The way to access the whole list of models on your collection is:

{% highlight javascript %}
> foo.models;
< [Backbone.Model, Backbone.Model]
{% endhighlight %}

<h2 id="pushaddreset">push(), add() and reset()</h2>
Pushing models to your collection is pretty simple too:

{% highlight javascript %}
> foo.push({bar:"3"});
> foo.length;
< 3
> // or you may want to push an array of models
> foo.add([{bar:"4"}, {bar:"5"}]);
> foo.length;
< 5
{% endhighlight %}

Resetting your collection removes all the models from it:

{% highlight javascript %}
> foo.reset();
> foo.length;
< 0
{% endhighlight %}


<h2 id="pluck">pluck()</h2>
Pluck returns an array with the specified attribute from your collection's models.

{% highlight javascript %}
> foo.reset()
> foo.push([
  {bar: 1},
    {bar: 2},
    {bar: 3}]);
> foo.pluck('bar');
< [1,2,3]
{% endhighlight %}

<h2 id="comparator">comparator</h2>
Setting the collection comparator automatically sorts the collection on insertion:

{% highlight javascript %}
> foo.reset()
> foo.comparator = 'baz';
> foo.push([
  {bar: 1, baz: 'z'},
    {bar: 2, baz: 'y'},
    {bar: 3, baz: 'x'}]);
> foo.pluck('bar');
< [3,2,1]
{% endhighlight %}


<h2 id="where">where() and findWhere()</h2>
These are used to query the collection for retrieving models.

{% highlight javascript %}
> foo.reset()
> foo.push([
  {bar: 1, baz: 'z', qux: 1},
    {bar: 2, baz: 'y', qux: 2},
    {bar: 3, baz: 'x', qux: 1}]);
> var result = foo.where({qux: 1});
< [Backbone.Model, Backbone.Model]
> result.length;
< 2
> result[0].bar;
< 1
> result[1].bar;
< 2
> // you may join several attributes
> result = foo.where({qux: 1, baz: 'x'});
< [Backbone.Model]
> result[0].bar;
< 3
> // when querying to get one/the first result, use findWhere
> result = foo.findWhere({bar: 1});
< Backbone.Model
> result.toJSON();
< {bar: 1, baz: 'z', qux: 1}

{% endhighlight %}

<h2 id="fetch">fetch()</h2>

Remember the `url` on the [`BooksCollection`](#collection_create)? This defines the API endpoint for the Collection. Cool thing is that it also applies to the `model` it encloses! This means if you follow the rules, you won't have to define the `url` attribute to your models as [we did](/learning/backbone-tutorial-1/#models) in the previous post.

`fetch()` will make a GET request to the `url` endpoint, which for `BooksCollection` is `/books`.

{% highlight javascript %}
> var Book = Backbone.Model.extend({
    // no url here!
  defaults: {
      'type':'hardcover'
    }
});
> var books = new BooksCollection();
> books.fetch();
> books.length;
< 2
> books.pluck('id');
< [1,2]
> // pretty neat, let's play around with it's models
> books.first().toJSON();
< { title: 'Fahrenheit 451', author: 'Douglas Adams', type: 'paperback', 'number_of_pages': 251 }
> books.first().save({'number_of_pages': 192});
{% endhighlight %}

This last `save()` will POST this model to the URL `/books/1`, which is the collection's URL with the model ID. Backbone is smart enough for this :-)

<h2 id="events">events</h2>
Just like what happens with models, collections also trigger events whenever you change them in some way. The possible events are: `"change"`, `"add"`, `"remove"` and `"reset"`. We'll get into this on the next post!


The [next post](/learning/backbone-tutorial-3) will be about `Backbone.View` which handles rendering the eye candy on your web-app!

Feel free to leave props or flames in the comment section ;-)