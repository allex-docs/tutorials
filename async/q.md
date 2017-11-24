# The `q` library

The `q` library you will work with in the AllexJS is a clone of the original [`q`](https://github.com/kriskowal/q).
The complete AllexJS `q` library was built from scratch, having the very useful "defer-promise" paradigm in mind.

# Run-time location of the `q` library
If you have the `allexlib` (that is actually the result of `require(allex)`, `q` is found as

```javascript
var q = execlib.lib.q;
```

In the rest of this tutorial, it is assumed that the `q` variable is obtained as a reference to the `q` library.

# Defer
A defer is an object that you produce with a simple call `q.defer()`;

```javascript
var defer = q.defer();
```

`defer` has 3 methods:
- resolve
- reject
- notify

and one property:
- promise

## How to program asynchronously using `q` defers
Treat the defer as the "server side" of your asynchronous programming paradigm.
On the other side, treat the defer.promise as the "client side".
Here is the most typical example:

> We have to write a function for which you don't know _when_ it will finish its job. But once its job is finished, we _will know_.

Let's call this function `a`.
Now, the principle is:

1. Create a defer in `a`.
2. Keep the defer for yourself.
3. Pass the defer to any function that will handle asynchronicity produced by `a`.
4. From `a` return the defer.promise.

Steps 1-3 define the "server behavior".
Step 4 enables the "client behavior".

### Server behavior
The term "server" is quoted in this tutorial, because the "server" is actually the asynchronous job that has to be done.
The job is triggered by calling an appropriate function/method.
- Each call to a function should produce a defer.
- The defer produced materializes the asynchronous job.
- When the asynchronous job ends successfully, `resolve` the corresponding defer with the result of the asynchronous job.
- If the asynchronous job raises an exception, `reject` the corresponding defer with the exception raised by the job.
- If the asynchronous job should report a progress during its operation, `notify` the defer with the progress data reported by the job.

### Client behavior
The client calls the function that will perform an asynchronous job.
The client needs to be aware that the function will return a promise.
- Call the function and receive a promise
- Use the `then` method of the promise to attach
  1. a success handler
  2. a reject handler
  3. a progress handler

## Example of the defer/promise programming paradigm
### Server side - the function implementation
Let's program a function `a` that will asynchronously read a file (defined by a path to the file), and report back the contents of the file.

```javascript
var fs = require('fs');
function a (filepath) {
  var d = q.defer(),
    ret = d.promise;
  fs.readFile(filepath, function (err, data) {
    if (err) {
      d.reject(err);
    } else {
      d.resolve(data);
    }
    d = null;
  });
  return ret;
}
```

Let's discuss the above implementation of function `a`.

1. `a` initially creates a defer and the return value (the defer's promise).
2. `a` calls the `fs`'s `readFile` method. This method accepts the path to the file, the read options (skipped in our tiny example), and the callback function.
3. The anonymous inline callback function accepts `err` and `data` in this exact order.
4. If `err` is defined, the defer will be `reject`ed with `err`.
5. If `err` is not defined, the defer will be `resolve`d  with `data`.
6. In both cases (rejection or resolution) the contents of the `d` variable that contains a reference to the defer will be set to `null`. This step is not crucial, but we strongly recommend it. Habit of `null`ing variables from outer scope when they are not needed any more will help you guard your code against possible memory leaks.

### Client side - the caller of the function `a`
```javascript
function a_consumer (filepath) {
  var promise = a(filepath);
  promise.then(
    function (contents) {
      console.log('Contents of '+filepath+' is :'+contents);
      filepath = null;
    },
    function (reason) {
      console.error('File '+filepath+' could not be read because of '+reason);
      filepath = null;
    },
    null
  );
}
```

This is a most trivial example, where `a_consumer` just calls `a` and
1. In case of success (resolution) logs the contents of the file to console.
2. In case of an exception (rejection) error-logs the exception (`reason` is a common term) to the console.

Because `a_consumer` is so trivial, it is not usable that much.

### Client side - a more useful example
```javascript
function json_reader (filepath) {
  return a(filepath).then(
    function (contents) {
      try {
        return q(JSON.parse(contents));
      }
      catch (err) {
        return q.reject(err);
      }
    }
  );
}
```

Now, `json_reader` is a much more serious consumer of `a`, because `json_reader` returns a promise as well.
This returned promise is _implicit_.
The deal is that every promise has the `then` method.
`then` returns a promise.
But, that promise returned by `then` will finally yield the result of the function that is the success handler of `then`.
This is a complicated matter, and it will be explained in more detail in 'defer/promise chaining'.

However, because `json_reader` returns a promise, it can also be chained further by a function that will call `json_reader`.

In our case, we shall just briefly discuss the success handler (the anonymous inline function in `json_reader`):
1. Because it is a success handler, it expects the contents of the file
2. It tries to `JSON.parse` the contents obtained and returns a promise of a defer immediately resolved with the result of `JSON.parse`ing.
```javascript
return q(JSON.parse(contents));
```
3. If `JSON.parse` should fail, the caught exception is returned as a promise of a defer immediately rejected with the exception caught.
```javascript
return q.reject(JSON.parse(err));
```

That should be it, with one important "memory leak" note: the anonymous inline success handler makes use of variable `q` that comes from outer scope.
We cannot `null` the `q` variable, because no other usage of `q` would be possible after that in the whole code of this JavaScript file.
So, we have to leave the `q` variable not `null`ed.
This means that we should be sure that the whole anonymous inline success handler will not be used (kept by the garbage collector) forever.
But, why should the success handler be kept at all?
Who keeps a reference to it?
The answer is: the promise will keep it when its `then` method is called.

So, we need to be sure that noone will keep the promise forever.
In our case, the promise is implicitly produced with `a(filepath)`, noone keeps the reference to it, so we can rest assured that eventually
1. the promise on which we called the `then` method
2. the success handler we provided to the `then` method call
3. the `q` variable used by the success handler

will all be freed eventually.

## Use `bind` whenever possible (i.e. always)
For the end of this tutorial, we'll rewrite both `a` and `json_reader` with no anonymous inline functions:
```javascript
var fs = require('fs');
function _onFileRead (d, err, data) {
  if (err) {
    d.reject(err);
  } else {
    d.resolve(data);
  }
  d = null;
}
function a (filepath) {
  var d = q.defer(),
    ret = d.promise;
  fs.readFile(filepath, _onFileRead.bind(null, d));
  return ret;
}

function json_parse_as_promised (contents) {
  try {
    return q(JSON.parse(contents));
  }
  catch (err) {
    return q.reject(err);
  }
}
function json_reader (filepath) {
  return a(filepath).then(
    json_parse_as_promised
  );
}
```

### Re-implementation of `a`
Here the `bind` had to be used, because we had to bind the local variable `d` to `_onFileRead`.
Mind that `_onFileRead` also `null`s the variable `d` after usage.

### Re-implementation of `json_reader`
In this case, where local defer was not being created at all, the helper function `json_parse_as_promised` has no variables to be bound with.
It turns out that `json_parse_as_promised` is actually a very useful function by itself.
It `JSON.parse`s any given contents and returns a promise (both in the case of success and in the case of an exception).
In the case of success, the promise will be resolved with the `JSON.parse`d contents.
In the case of failuer, the promise will be rejected with the exception.

### How come `a` had to use `bind` and `json_reader` did not have to?
It is much more handy when you can use functions that return promises.
`json_reader` was in that type of situation.
It uses `a` that returns a promise.
So it did not have to create a defer of its own.

On the other side, `a` ha  to use the `fs`'s `readFile` method that does _not_ return a promise.
So it had to create a defer and bind it to the callback provided to `readFile`.


## Wrapping it all up
In this tutorial 
1. You got introduced the fundamental concepts of AllexJS approach to asynchronous programming: the `defer` and its `promise`.
2. You got introduced with the concept of "server side" and "client side" programming.
3. You saw how promise-returning functions _can_ be chained.

## Where to go next
You should dive deeper into [defer/promise chaining](defer_promise_chaining.md).
