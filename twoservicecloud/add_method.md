# Expose the `multiply` method on the `user` role

Adding new method implementations to the User class has already been described at [Adding a method](../basic_development/plainservice.md#adding_a_methoda).

We will perform almost the exact same steps.

## Add the corresponding method descriptor to the `methoddescriptors`

We edit the `methoddescriptors/user.js` file so that it looks like this:

```javascript
module.exports = {
  multiply: [{
    title: 'Number to Multiply',
    type: 'number'
  }]
};
```

## Add the method implementation

We edit the `users/usercreator.js` file so that it looks like this:

```javascript
function createUser(execlib, ParentUser) {
  'use strict';
  if (!ParentUser) {
    ParentUser = execlib.execSuite.ServicePack.Service.prototype.userFactory.get('user');
  }

  var qlib = execlib.lib.qlib;

  function User(prophash) {
    ParentUser.call(this, prophash);
  }
  
  ParentUser.inherit(User, require('../methoddescriptors/user'), [/*visible state fields here*/]/*or a ctor for StateStream filter*/);
  User.prototype.__cleanUp = function () {
    ParentUser.prototype.__cleanUp.call(this);
  };

  User.prototype.multiply = function (number, defer) {
    return qlib.promise2defer(this.__service.multiply(number), defer);
  };

  return User;
}
```

We implemented the `multiply` method in a more serious manner in this tutorial.
Let's see what _more serious_ means:

### Delegate the very functionality to the Service class

In general, 

1. You should implement the functionality of the exposed method in the Service class.
2. The method of the Service class that performs the exposed functionality should return a (`q`) Promise.
3. The method of the User class that calls the corresponding method of the Service should make use of the `promise2defer` function of the `qlib` (`execlib.lib.qlib`) library.

`promise2defer` "short-circuits" the promise that the Service's method will return with the `defer` the User's method will provide to it.

```javascript
  User.prototype.multiply = function (number, defer) {
    return qlib.promise2defer(this.__service.multiply(number), defer);
  };
```

"Short circuit" between a promise and a defer means that

1. If the promise is __resolved__, the defer will be resolved with the same value.
2. If the promise is __rejected__, the defer will be rejected with the same reason.
3. If the promise is __notified__, the defer will be notified with the same progress value.

Also, mind the `this.__service`.
Every instance of every User class has this property.
It is the reference to the "mother" Service that spawned this User instance.

### Prepare a provisional `multiply` method on the Service

We edit the `servicecreator.js` file in order to add the provisional `multiply` method.

```javascript
MultiplierService.prototype.multiply = function (number) {
  return execlib.lib.q(0);
};
```

This method implementation is all but correct.
However, it will leave the Service code in a runnable state.

# Wrapping it all up

In order to expose the `multiply` method, we

1. Described the input parameters of the `multiply` method in `methoddescriptors/user.js`.
2. Implemented the `multiply` method on the `User` class in `users/usercreator.js` by delegating the call to a Promise-returning `multiply` method of the Service.
3. Implemented a provisional `multiply` method on the Service.


# Where to go next

In order for the Service`s `multiply` method to work correctly, the Service should fetch the `multiplier` configuration parameter from the `Config` service.

Go through the [Listen to the Config Service](listentoconfig.md) tutorial to see how this is done.
