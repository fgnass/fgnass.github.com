---
layout: post
tags : [node]
---

I usually try to avoid using inheritance in JavaScript altogether but sometimes there are situations where using a classic OO-style can make things easier.

Let's assume we have a class `Foo` which we want to extend:

{% highlight js %}
function Foo() {
  // constructor code
}
Foo.prototype = {
  bar: function() {
    // do something
  }
};
{% endhighlight %}

A common pattern is this:

{% highlight js %}
function Bar() {
}
Bar.prototype = Object.create(Foo.prototype);
Bar.prototype.bar = function() {
  // do something else
};
{% endhighlight %}

In contrast to the original definition of `Foo` this code looks quite noisy.

## Using util.inherits()

 In Node.js there's the [util.inherits()](https://github.com/joyent/node/blob/master/lib/util.js#L498) function which helps us to write the same thing a little more compact and also creates a `constructor` property as well as a reference to the super constructor:

{% highlight js %}
function Bar() {
}
util.inherits(Bar, Foo);
Bar.prototype.bar = function() {
  // do something else
};
Bar.prototype.baz = function() {
  // yet another method
};
{% endhighlight %}

## Passing properties to Object.create()

Still we have to repeat `Bar.prototype` for each property we want to add. We can overcome this by passing a property descriptor to `Object.create()`:

{% highlight js %}
function Bar() {
}
Bar.prototype = Object.create(Foo.prototype, {
  bar: { value: function() {
    // do something else
  }},
  baz: { value: function() {
    // yet another method
  }}
});
{% endhighlight %}

Unfortunately this still looks quite noisy as we have to wrap all our properties into descriptor objects. On the other hand this syntax makes it easy to define getters or setters:

{% highlight js %}
function Bar() {
}
Bar.prototype = Object.create(Foo.prototype, {
  bar: { value: function() {
    // do something else
  }},
  boo: {
    get: function() {
      return this._boo;
    },
    set: function(value) {
      this._boo = value && (''+value).toUpperCase();
    }
  }
});
{% endhighlight %}

Otherwise we'd have to call `Object.defineProperty('boo', {...})` instead.

## The get and set operators

Still this isn't as concise as it could be. Since JavaScript 1.8.5 the language supports the [get](https://developer.mozilla.org/en/JavaScript/Reference/Operators/get) and [set](https://developer.mozilla.org/en/JavaScript/Reference/Operators/set) operators which can be used inside an object literal to define getters and setters:

{% highlight js %}
function Foo() {
  // constructor code
}
Foo.prototype = {
  get boo() {
    return this._boo;
  },
  set boo(value) {
    this._boo = value && (''+value).toUpperCase();
  }
};
{% endhighlight %}

Now this is nice, but how can we use this approach for our subclass?

## The deprecated, non-standard way

One possibility is to use the non-standard `__proto__` [property](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/Proto) that allows us to change an objects prototype even after it has been created:

{% highlight js %}
function Bar() {
}
Bar.prototype = {
  __proto__: Foo.prototype,
  get boo() {
    return 'Buh!';
  }
};
{% endhighlight %}

## A standard based solution

Despite being deprecated the solution above is currently my favorite inheritance style. If you want to avoid using `__proto__` for some reason you can write a helper function similar to Node's `util.inherits()` that uses standard ECMAScript 5 features to achieve similar results:

{% highlight js %}
function Foo() {
  // constructor code
}
Foo.prototype = {
  bar: function() {
    // do something
  }
};

function Bar() {
  // constructor code
}
util.prototype(Bar, Foo, {
  get boo() {
    return 'Buh!';
  }
});
{% endhighlight %}

Here is the implementation of the `util.prototype` function:

{% highlight js %}
function prototype(ctor, superCtor, proto) {
  var props = {
    constructor: { value: ctor, writable: true, configurable: true }
  };
  Object.getOwnPropertyNames(proto).forEach(function(name) {
    props[name] = Object.getOwnPropertyDescriptor(proto, name);
  });
  ctor.prototype = Object.create(superCtor.prototype, props);
  ctor.super_ = superCtor;
}
{% endhighlight %}

##TL;DR

The [get/set](#the_get_and_set_operators) operators are cool and `__proto__` [is your friend](#the_deprecated_nonstandard_way). If you don't want to use it you can write a simple [helper function](#a_standard_based_solution) to achive a similar result using standard ECMAScript features.
