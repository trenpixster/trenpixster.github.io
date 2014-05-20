---
layout: post
title:  "The wonders of .bind()"
comments: true
date:   2014-03-02 22:07:58
categories: tip
tags: [javascript]
---

Here's a quick cool tip to improve code smoothness.

How many times have you

{% highlight javascript %}
var self = this;
{% endhighlight %}
or

{% highlight javascript %}
var _this = this;
{% endhighlight %}
and all these variants? I always hated typing this - pun not intended. Usually it is required to do it when the scope for `this` is overridden.

E.g.:

{% highlight javascript %}
var array = [1,2,3,4],
  self  = this;
this.another_function = function(args){/*...*/};
array.forEach(function(element){
  // verbose iterating code
    self.another_function(element);
});

{% endhighlight %}

How to overcome this little issue? Through [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)! Applying to the piece of code above:

{% highlight javascript %}
var array = [1,2,3,4];
this.another_function = function(args){/*...*/};
array.forEach(function(element){
  // verbose iterating code
    this.another_function(element);
}.bind(this));
{% endhighlight %}

One recurrent use case is `setTimeout`:

{% highlight javascript %}
this.value = 1;
setTimeout(function(){ this.value++; }.bind(this), 1000);
{% endhighlight %}
Another recurrent use case is on object scoping:

{% highlight javascript %}
var obj = {
  value: 1,
    getValue: function(){ return this.value; }
};

this.value = 2;
obj.getValue(); // Evals 1
obj.getValue.bind(this)(); // Evals 2
{% endhighlight %}

And yes, this is vanilla Javascript, no libs required.