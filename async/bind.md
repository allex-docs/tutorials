# The `bind`

In a most generic way, you should be treating `bind` as a function that takes functions and parameters to produce new functions.

## A simple example

```javascript
function multiply (num1, num2) {
  return num1 * num2;
}
```

As you can see, the `multiply` function takes two numbers, multiplies them and returns the result of the multiplication.

Now, how can we make a function that multiplies numbers by 2?

We should reuse the `multiply` function and write

```javascript
function multiplyby2 (num) {
  return multiply(2, num);
}
```

So, `multiplyby2` would actually work just as if the original `multiply` function were

1. accepting just the second parameter
2. having the first parameter be fixed (_bound_) at the value of `2`

Now, what has this got to do with `bind`?
The fact is that you can avoid writing the `multiplyby2` function above, and just `bind` the `multiply` function with 2 being the first parameter.

In order to distinguish from the original `multiplyby2` function, the result of `bind` we shall assign to a `multiplyby2b` variable.

```javascript
var multiplyby2b = multiply.bind(null, 2);
```

So,

```javascript
multiplyby2(2);
```

and

```javascript
multiplyby2b(2);
```

would return the same result `4`.
But, we cannot leave `bind` at this point and consider we've got the whole story done.
Because functions can also be _methods_.

## The `this` situation

Now, let's consider a simple _class_.

```javascript
function Multiplier (factor) {
  this.factor = factor;
}
Multiplier.prototype.multiply = function (number) {
  return this.factor*number;
};
```

The `Multiplier` class is meant to be used like this:

```javascript
var m3 = new Multiplier(3),
  m5 = new Multiplier(5);

m3.multiply(7); // returns 21
m5.multiply(8); // returns 40
```

Now, what does actually happen when you call

```javascript
m3.multiply(7);
```

? Actually, the `Multiplier.prototype.multiply` function is being called with 

- parameter `number` equal to `7`, __but__ also 
- with `this` being equal to `m3`.

Finally, we come to the crucial question of this tutorial:

> What has `bind` got to do with asynchronous programming?

`bind` lets you postpone execution of methods with given parameters on given instances.

If we wanted the `m3` `Multiplier` instance to multiply number 7 somewhere in the future, we would construct a _bound_ function (let's name it `m37`)

```javascript
var m37 = m3.multiply.bind(m3, 7);
```

The `m37` variable would contain a function that we can call anytime later in the program's lifecycle.
Whenever we call `m37`

```javascript
m37(); //returns 21;
```

So, the big deal with the `this` is that the first argument you provide to `bind` will be the `this` parameter during the execution of the function that `bind` returns.

If you take a look at how we wrote the `multiply` function in [A Simple Example](a_simple_example), and how we wrote the `Multiplier.prototype.multiply` method in this situation, it is clear that the `multiply` function makes no use of the `this` parameter, but the `Multiplier.prototype.multiply` method makes use of the `this` parameter.

So, we _bound_ the `multiply` function by providing the `this` parameter of value `null`.
On the other side, it makes no sense to `bind` a method of a class to a `null` value for `this`. You have to provide a "live" instance as the `this` argument instead. In our case, we provided the `m3` value to be the `this` argument.

It might also be useful to note that instead of

```javascript
var m37 = m3.multiply.bind(m3, 7);
```

we could have written

```javascript
var m37 = Multiplier.prototype.multiply.bind(m3, 7);
```

and get the same thing.
However, the first approach appears as syntactically more convenient.
Moreover, in many cases it may be the only approach available, because your code may have access to the instance of the `Multiplier` class (in our case it is `m3`), but not to the very `Multiplier` class code itself.

## Wrapping it all up
Having all said here in mind, remember that every function you produce with `bind` will keep all the parameters you provided to it. This is very important with regard to memory handling (and the notorious _memory leaks_).

Also, for the sake of correctness, the first sentence of this tutorial needs improving: `bind` is actually not a function, but a _method_ of the Function class.
Each and every function in JavaScript is actually an instance of the Function class, and has the `bind` method.

## Where to go next
Get acquainted with the basic concepts of [using the `q` library](q.md)
