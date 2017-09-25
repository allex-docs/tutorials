# Forcing the existence of `propertyhash` configuration properties

So, you're developing a Service, and you expect certain configuration properties to appear in the `prophash`, the one and only input parameter to your Service's constructor?
Let's take a simple case of this kind - a Service that has to `tcp` connect to a remote data source and start detching data from it.

In order to connect, this Service will need an IP address (let's assume that the Port is hardcoded to 80).
The Service implementation (class is named FetchingService) might look like this:

```javascript
function createFetchingService(execlib, ParentService) { 
  'use strict';


  function factoryCreator(parentFactory) {
    return {
      'service': require('./users/serviceusercreator')(execlib, parentFactory.get('service')),
      'user': require('./users/usercreator')(execlib, parentFactory.get('user'))
    };
  }

  function FetchingService(prophash) {
    ParentService.call(this, prophash);
    this.maintainConnectionTo(prophash.remoteip);
  }

  ParentService.inherit(FetchingService, factoryCreator);

  FetchingService.prototype.__cleanUp = function() { 
    ParentService.prototype.__cleanUp.call(this);
  };

  FetchingService.prototype.maintainConnectionTo = function (remoteip) {
    //...
  };

  return FetchingService;
}

module.exports = createFetchingService;
```

One can see from the above code that this Service will run into troubles if the `remoteip` property does not exist in the `prophash`.

This problematic situation arises with the following `.allexlanmanager/needs` file:

```json
[
  {
    "modulename": "allexusername__fetchingproject_fetchingservice",
    "instancename": "Fetcher",
    "propertyhash": {
    }
  }
]
```

## Approach No 1: default the missing prophash property

```javascript
  function FetchingService(prophash) {
    ParentService.call(this, prophash);
    this.maintainConnectionTo(prophash.remoteip || '127.0.0.1');
  }
```

Although this seems an elegant approach, it may lead to confusing boot-time situations.
The probability and severity of the confusions are proportional to the complexity of the Cloud architecture.

## Approach No 2: force the existence of the prophash property

Augment the code for `FetchingService` by adding the `propertyHashDescriptor` to its `prototype`:

```javascript
  FetchingService.prototype.propertyHashDescriptor = {
    remoteip: {
      type: 'string'
    }
  };
```

1. `propertyHashDescriptor` is an `Object`.
2. `propertyHashDescriptor` is a property of the `Service` class `prototype`.
3. Property names of the `propertyHashDescriptor` are the property names of `prophash` that must exist.
4. Value of each property of the `propertyHashDescriptor` is a [jsonschema](http://json-schema.org). `jsonschema` will allow you to define the value of the expected `prophash` property  in detail.

In our case, we simply stated that we want the `remoteip` property of `prophash`:

1. To exist
2. To be a `string`

Excercise is left to the reader to implement a more strict `jsonschema` so that the `remoteip` property may be

1. Only an `ipv4` string (hint: the `format` keyword of the `jsonschema` implementation)
2. Either an `ipv4` string or the `localhost` string literal
3. A `hostname` string (again, the `format` keyword may be helpful here)

#Wrapping it all up

If you have to rely on the value of a certain property of the `prophash` provided to the constructor,

1. Refrain from providing the hardcoded default value for the missing property.
2. Force the existence of the expected property by providing an appropriate `jsonschema` mapped to the expected property name in the `propertyHashDescriptor`.
