# Service Interconnectivity

In many cases an instance of a Service needs to connect to other Service instances in order to perform its function.

#### Location of the remote service

A Service may choose to 

* find a remote Service in the cluster (global connection)
* start a sub-Service in its process (local connection)

#### Announcing the connection
Once connected to another Service, it may choose to announce (or not to announce) the established connection. If the connection is announced, the clients connected to the Service will be capable of sub-connecting to the remote Service. Therefore, in terms of being announced, the connections may be 

* private
* public

In order to sum up the above cases, a connection to a remote Service may be:

* global public
* global private
* local public
* local private

## Global Private connection HowTo

This connection type is achieved by implementing a `RemoteServiceListenerServiceMixin` provided by the `execSuite` (`).

In the following examples it will be assumed that your Service class is named `MyService`.

### Grab the needed references
```javascript
var execSuite = execlib.execSuite,
    RemoteServiceListenerServiceMixin = execSuite.RemoteServiceListenerServiceMixin;
```

### Initiate the Mixin in the constructor

```javascript
//after the lines
  function UserUsersService(prophash) {  
    ParentService.call(this, prophash);
//add the line
    RemoteServiceListenerServiceMixin.call(this);
```

### addMethods after inheriting from the ParentService

```javascript
//after the line
ParentService.inherit(MyService, factoryCreator, require('./storagedescriptor'));
//add the line
RemoteServiceListenerServiceMixin.addMethods(MyService);
```

### Destroy the Mixin in __cleanUp
```javascript
MyService.prototype.__cleanUp = function() {
//add the line
  RemoteServiceListenerServiceMixin.prototype.destroy.call(this);
//before the lines
  ParentService.prototype.__cleanUp.call(this);
};
```

### Use the Mixin

#### Start the find procedure
Wherever you find it suitable (usually it is the best to do it in the constuctor)

```javascript
this.findRemote('OtherService');
this.findRemote([{name: 'DB', identity: {name: 'user', role: 'user'}}, 'UserBank']);
```

in the short form of `findRemote`.

Actually, `findRemote` takes 3 parameters:

1. Path to the remote Service (`String` or `Array` of `String`s)
2. Identity object (if not provided as an `Object`, the default form will be used:
```javascript
{name: 'user', role: 'user'}
```
3. Name under which the obtained Sink to remote Service will be stored (`String`). If not provided, the default will be evaluated from parameter 1, the Path to the remote Service, depending on its `typeof`:
- If Path is a `String`, Name will equal that `String`
- If Path is an `Array` of `String`s, Name will equal the last element of that array (that has to be a `String`).

#### (optionally) Handle the moment when the sink is obtained
```javascript
this.state.data.listenFor('OtherService', this.onOtherService.bind(this), true);
this.state.data.listenFor('UserBank', this.onUserBank.bind(this), true);
```
The `true` parameter in above calls means _call me only when you obtain a sink, not when you lose it_.

#### (optionally) Make a method dependent on the sink presence
As a general case
```javascript
MyService.prototype.callOtherService = execSuite.dependentServiceMethod([], ['OtherService'], function (otherservicesink, param_1, ..., param_N, defer) {
  otherservicesink.call('someremotemethodname', p_1, ..., p_M).then(
    resolve_handler,
    reject_handler,
    notify_handler
  );
});
```
However, a very common use case is just to resolve the defer with the promise from `call` from the obtained sink. `qlib` is of help here (`var qlib = execlib.lib.qlib;`)
```javascript
MyService.prototype.callOtherService = execSuite.dependentServiceMethod([], ['OtherService'], function (otherservicesink, param_1, ..., param_N, defer) {
  qlib.promise2defer(otherservicesink.call('someremotemethodname', p_1, ..., p_M), defer);
});
```


