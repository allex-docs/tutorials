# Managing the state of a Service

In this tutorial, we'll be in the "read only" mode.
We will take a look at the code that actually sets the state of the Service.
The same state that the clients will be listening to.

## Diving into the Time service code

As already explained in other tutorials, if you're running/consuming a Service, you can rest assured that you have the corresponding Service code in your global `node_modules` directory.
In the case of this tutorial, the Service in particular is `allex_timeservice`.

On Unix-like systems, it is located in

```
<git config get prefix>/lib/node_modules/allex_timeservice
```

On Windows systems, it is located in

```
<git config get prefix>/node_modules/allex_timeservice
```

For a more thorough understanding of the `allex_timeservice` directory structure, please take a look at [Typical Service module directory structure](../development_basics/servicemoduledirstructure.md)

For a more thorough understanding of the `state` concept, please take a look at the [State basics](../development_basics/state.md)

## State handling mechanics in the Time service

1. `allex_timeservice` updates the `time` property of the Service's `state` through the `setTimeout` mechanics (actually, through `runNext`, the AllexJS flavor of `setTimeout`).
2. Every second the `time` property of the Service's `state` will be set to `Date.now()`.
3. This change on the Service's `state` will result in an equal change on the `time` property on the `state` of every User instance of the Service.
4. Every User instance will broadcast the change to all connected Clients.
5. Remote Clients will receive the change. If the Client code has expressed interest in the `state`, proper callback function will be called with appropriate values (_new value_ and _old value_).


### servicecreator.js code

```javascript
function createTimeService(execlib,ParentService){
  'use strict';
  var lib = execlib.lib;

  function factoryCreator(parentFactory){
    return {
      'service': require('./users/serviceusercreator')(execlib,parentFactory.get('service')),
      'user': require('./users/usercreator')(execlib,parentFactory.get('user')) 
    };
  }

  function TimeService(prophash){
    ParentService.call(this,prophash);
    this.state.set('active', true);
    this.start = Date.now();
    this.ticks = 0;
    this.tick();
  }
  ParentService.inherit(TimeService,factoryCreator);
  TimeService.prototype.tick = function(){
    var currtime, nextin;
    if(this.state && !this.state.get('closed')){
      currtime = Date.now();
      this.ticks++;
      nextin = this.start+this.ticks*1000-currtime;
      if (this.state.get('active')) {
        this.state.set('time',currtime);
        console.log('tick! next in',nextin);
      }
      lib.runNext(this.tick.bind(this),nextin);
    }
  };
  TimeService.prototype.setActive = function (active) {
    this.state.set('active', active);
  };
  
  return TimeService;
}

module.exports = createTimeService;
```

In order to analyze what's going on here, we'll first focus on the constructor:

```javascript
function TimeService(prophash){
	ParentService.call(this,prophash);
	this.state.set('active', true);
	this.start = Date.now();
	this.ticks = 0;
	this.tick();
}
```

So, the whole "magic" of managing the state is in the `set` method of the `Collection` class.


```javascript
this.state.set('active', true);
```

This particular line of code sets the `active` property of the `state` to `true`. If a Client were interested in this property, it would `materializeState` and then `listenState` for the `active` property.

However, the `tick` method implements setting the `time` property of the `state`:

```javascript
TimeService.prototype.tick = function(){
  var currtime, nextin;
  if(this.state && !this.state.get('closed')){
    currtime = Date.now();
    this.ticks++;
    nextin = this.start+this.ticks*1000-currtime;
    if (this.state.get('active')) {
      this.state.set('time',currtime);
      console.log('tick! next in',nextin);
    }
    lib.runNext(this.tick.bind(this),nextin);
  }
};
```

Here you can see how to read the property of a state on the "server side":

```javascript
if(this.state && !this.state.get('closed')){
  ...
}
```

Mind the first part in the `if` clause:
if there is no `state`, that means that this instance is already destroyed, so there is nothing useful to be done here.

```javascript
this.state.get('closed')
```

tests for the `closed` property of the `state`.
This property is set to `true` when the Service instance starts the destruction procedure.
Again, if it is `true`, there is nothing useful to be done here.

Once the `nextin` variable is calculated, the `active` property of the `state` is checked.
If `active` is `true`, the `time` property of the `state` is set (and being broadcast over the AllexJS infrastructure).
Moreover, just for run-time fun, a line is printed out to the console.

```javascript
TimeService.prototype.tick = function(){
  var currtime, nextin;
  if(this.state && !this.state.get('closed')){
    currtime = Date.now();
    this.ticks++;
    nextin = this.start+this.ticks*1000-currtime;
    if (this.state.get('active')) {
      this.state.set('time',currtime);
      console.log('tick! next in',nextin);
    }
    lib.runNext(this.tick.bind(this),nextin);
  }
};
```
In any case, the timeout is set to `nextin` milliseconds.

# Wrapping it all up

1. Set the property `x` of `state` to `y`: `this.state.set('x', 'y);`
2. Get the property `x` of `state` on the "server side": `this.state.get('x');`
3. Completely remove the property `x` of `state`: `this.state.remove('x');`
