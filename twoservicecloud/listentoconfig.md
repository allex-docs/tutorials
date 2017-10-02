# Listen to the Config Service

In the [Read time from the Service](../first_steps/readtimeservicestate.md) tutorial you learnt how to listen to the `state` of a remote Service.
However, the tutorial teaches you how to build a client-based `allexruntask` script in order to achieve the task.

This time we have to tell a running Service instance (that acts as a Server towards its Clients) to consume another Service (so that it actually acts as a Client towards another Service).
For this purpose we cannot use the Client-based `allexruntask` mechanism.

We have to turn to the [Service Interconnectivity](../development_basics/serviceinterconnectivity.md) mechanism.


## Making use of `RemoteServiceListenerServiceMixin`

Let's take a look at the `servicecreator.js` Service implementation created by the `allex-service-createrepo` scaffolder:

```javascript
function createMultiplierService(execlib, ParentService) {
  'use strict';
  

  function factoryCreator(parentFactory) {
    return {
      'service': require('./users/serviceusercreator')(execlib, parentFactory.get('service')),
      'user': require('./users/usercreator')(execlib, parentFactory.get('user')) 
    };
  }

  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
  }
  
  ParentService.inherit(MultiplierService, factoryCreator);
  
  MultiplierService.prototype.__cleanUp = function() {
    ParentService.prototype.__cleanUp.call(this);
  };
  
  return MultiplierService;
}

module.exports = createMultiplierService;
```

And we alter it so that implements the `RemoteServiceListenerServiceMixin` mixin:

```javascript
function createMultiplierService(execlib, ParentService) {
  'use strict';
  
  var execSuite = execlib.execSuite,
    RemoteServiceListenerServiceMixin = execSuite.RemoteServiceListenerServiceMixin;

  function factoryCreator(parentFactory) {
    return {
      'service': require('./users/serviceusercreator')(execlib, parentFactory.get('service')),
      'user': require('./users/usercreator')(execlib, parentFactory.get('user')) 
    };
  }

  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
  }
  
  ParentService.inherit(MultiplierService, factoryCreator);
  RemoteServiceListenerServiceMixin.addMethods(MultiplierService);
  
  MultiplierService.prototype.__cleanUp = function() {
    RemoteServiceListenerServiceMixin.prototype.destroy.call(this);
    ParentService.prototype.__cleanUp.call(this);
  };
  
  return MultiplierService;
}

module.exports = createMultiplierService;
```

## Start listening for the Config Service

First thing we need to address at this point is:

_What is the name of the Config service in the cloud?_

### Resist the temptation to harcode the name of the remote Config service

Here is the constructor code once again:

```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
  }
```

The point is that one should _not_ say:

```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
    this.findRemote('Config');
  }
```

because hardcoding the word `'Config'` destroys the configurability of the Multiplier Service.
Instead, make use of the `propertyhash` configuration object provided from `.allexlanmanager/needs`:


```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
    this.findRemote(prophash.configsinkname, null, 'Config');
  }
```

Here we will utilize the long form of `findRemote`:

1. Path is taken from the `propertyhash`
2. Identity is the default one
```javascript
{name: 'user', role: 'user'}
```
3. Name is `'Config'`, so that the internals of the Multiplier service refer to the remote Config service as `Config`.

#### Will `prophash.configsinkname` exist at all?

Last, but not least, `servicecreator.js` has to take care about the existence of the `prophash.configsinkname` property.

Following the guidelines from [Forcing the Existence of `propertyhash` properties](../development_basics/forceprophash.md),

we will __not__ provide a default value for `configsinkname` like

```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
    //avoid this:
    this.findRemote(prophash.configsinkname || 'Config', null, 'Config');
  }
```

__but__ we will __force__ the `configsinkname` to exist in `propertyhash` and to be either a `String` or an `Array`.

```javascript
  MultiplierService.prototype.propertyHashDescriptor = {
    configsinkname: {
      type: ['string', 'array']
    }
  };
```
with no changes in the constructor code.

## Once the Config sink is obtained, start listening for `multiplier`

We have to be sure we'll be notified whenever the `Config` sink is obtained.

> This is the crucial part of keeping the Cloud together in a functional state: The `Config` sink may come and go (for whatever reasons, the connection towards the `Config` service may break and the the sink is lost). However, the `findRemote` mechanism will start the search whenever the Sink is lost.

Hence, we update the constructor code:

```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
    this.findRemote(prophash.configsinkname, null, 'Config');
    this.state.data.listenFor('Config', this.onConfig.bind(this), true);
  }
```

so that we will be aware when the `Config` sink is obtained (mind the `true` as the third parameter, this means we won't be notified when the `Config` sink is lost).

Furthermore, we will introduce the handling `onConfig` method.
It will take just one parameter - the new value of the `Config` sink.
We will use the obtained Sink to listen for the `multiplier` value.
Since the `Config` sink is a Sink to an instance of the `allex_leveldbconfigservice`, we will utilize the `queryLevelDB` `Task`:

```javascript
  MultiplierService.prototype.onConfig = function (configsink) {
    taskRegistry.run('queryLevelDB', {
      queryMethodName: 'query',
      sink: configsink,
      scanInitially: true,
      filter: {
        keys: {
          op: 'eq',
          field: null,
          value: 'multiplier'
        }
      },
      onPut: this.onMultiplierSet.bind(this),
      onDel: this.onMultiplierRemoved.bind(this)
    });
  };
```

First, the discussion of the parameters provided:

| property   |  value   | comment |
| -------    | ------   | ----    |
| queryMethodName | `'query'` | The value may be `'query'` or `'queryLog'`, because `Config` also has a change log. We're interested not in the log, but in the data, hence `'query'`. |
| sink       | `configsink` | `queryLevelDB` _IS-A_ `SinkTask`. It will auto-destruct when the provided `sink` is destroyed. |
| scanInitially | true      | We want not just to listen for the data changes, but we also want to be informed about the initial state of the `Config`'s data. |
| filter     | `Object` | Two properties can be set: `keys` and `values`. We are interested only for the data associated to the key `multiplier`. So, we set the `keys` property to be an `Object` that has `op: 'eq'` (equals), `field: null` (the key is a scalar, and equality applies to the very value of the key itself), `value: 'multiplier'` (this is the value the scalar key has to be equal to). |
| onPut      | Bound `onMultiplierSet` method | Here we will be receiving the changes to the `multiplier` configuration parameter in the form of a 2-element array: ['multiplier', _value_]. The first element will always be `'mutliplier'` because of the filter applied. The second element will be the latest value of the `'mutliplier'` parameter. |
| onDel      | Bound `'onMultiplierRemoved'` method | Here we will be receiving the event when the `mutliplier` configuration parameter is _removed_ on the `Config` service. The event will just provide the name of the configuration parameter that was removed. In our case it will have the value `'multiplier'`, because of the filter applied. |

Second, have in mind that `queryLevelDB` is a Task introduced by `allex_leveldblib`.
If we do not load the `allex_leveldblib` (`allex:leveldb:lib` in _colon notation_), we would get an error saying that `queryLevelDB  does not map to a registered Task).

## Introduce a dependency to `allex:leveldb:lib`

First of all, we should be clear if the dependency on `allex:leveldb:lib` is needed

1. Only for the server side
2. Only for the client side
3. Both for the server side and the client side

In our case, we need `allex:leveldb:lib` to start a task on the server side, so the answer is 1. above.

Therefore, we edit `index.js` that originally looks like this:

```javascript
function createServicePack(execlib) {
  'use strict';
  return {
    service: {
      dependencies: ['.']
    },
    sinkmap: {
      dependencies: ['.']
    }, /*
    tasks: {
      dependencies: []
    }
    */
  }
}
```

so that it looks like this:

```javascript
function createServicePack(execlib) {
  'use strict';
  return {
    service: {
      dependencies: ['.', 'allex:leveldb:lib']
    },
    sinkmap: {
      dependencies: ['.']
    }, /*
    tasks: {
      dependencies: []
    }
    */
  }
}
```

This change will affect the _creator function_ implemented in `servicecreator.js`.

The fingerprint of the _creator function_ in `servicecreator.js` originally looks like this (the first line of `servicecreator.js`):

```javascript
function createMultiplierService(execlib, ParentService) {
```

1. `execlib` is the mandatory first parameter
2. `ParentService` corresponds to the `'.'` in the service dependencies

Now that the service dependencies have grown from

```javascript
['.']
```

to 

```javascript
['.', 'allex:leveldb:lib']
```

The fingerprint of the _creator function_ needs to change to

```javascript
function createMultiplierService(execlib, ParentService, leveldblib) {
```

`leveldblib` will hold a reference to the `LevelDB` library.
However, it should be noted that `leveldblib` will _not_ be used in `createMultiplierService` directly.
Just the `queryLevelDB` Task will be used.

## Prepare the event handling methods

These are the method skeletons for handling the `onPut` and `onDel` events from the `queryLevelDB` Task, respectively:

```javascript
  MultiplierService.prototype.onMultiplierSet = function (keyvalarray) {
    var key = keyvalarray[0], val = keyvalarray[1];
  };
```

```javascript
  MultiplierService.prototype.onMultiplierRemoved = function (key) {
  };
```

# Wrapping it all up

This tutorial has shown how to listen to and consume remote Services in the cloud:

1. Implement the `RemoteServiceListenerServiceMixin`.
2. Accept the remote Service name from `propertyhash` in the Service ctor.
3. Once the Sink to the remote Service is obtained, run the `queryLevelDB` task, because the remote Service is a `LevelDB` service by nature.
4. Implementing the `queryLevelDB` asks for handler methods - named `onMultiplierSet` and `onMultiplierRemoved` in our case.

# Where to go from here

You should go through the [Using the remote multiplier value](useremotemultiplier.md) tutorial now.
