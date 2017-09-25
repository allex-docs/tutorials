# Set the `multiplier` configuration parameter

In this tutorial, we will develop an `allexruntask` script named `setMultiplier.js` that will

1. Obtain a Sink to the `Config` service
2. On the Sink obtained, call `sink.call('put', 'multiplier', 5)`, where 5 is just a hardcoded value.

Then, in a similar fashion, we will develop an `allexruntask` script named `removeMultiplier.js` that will

1. Obtain a Sink to the `Config` service
2. On the Sink obtained, call `sink.call('del', 'multiplier')`.


Creation of `allexruntask` scripts has been thoroughly described in [Read time From the Service](../first_steps/readtimeservicestate.md), so we will just present the very scripts:

## `setMultiplier.js`

```javascript
function onPutSucceeded (result) {
  console.log('put succeeded', result);
  process.exit(0);
}

function go (taskobj) {
  if (!(taskobj && taskobj.sink)) {
    process.exit(1);
  }
  taskobj.sink.call('put', 'multiplier', 5).then(
    onPutSucceeded
  );
}

module.exports = {
  sinkname: 'Config',
  identity: {name: 'user', role: 'user'},
  task: {
    name: go
  }
};
```

Read the script bottom-up: 

1. Standard `allexruntask` export asks for the `Config` declaring `go` to be the callback function.
2. `go`, after sanity checks, calls the `put` method in order to set the value of `multiplier` to 5.
3. `onPutSucceeded` prints out the message and `process.exit`s.


## `removeMultiplier.js`

```javascript
function onRemoveSucceeded (result) {
  console.log('del succeeded', result);
  process.exit(0);
}

function go (taskobj) {
  if (!(taskobj && taskobj.sink)) {
    process.exit(1);
  }
  taskobj.sink.call('del', 'multiplier').then(
    onRemoveSucceeded
  );
}

module.exports = {
  sinkname: 'Config',
  identity: {name: 'user', role: 'user'},
  task: {
    name: go
  }
};
```

Read the script bottom-up: 

1. Standard `allexruntask` export asks for the `Config` declaring `go` to be the callback function.
2. `go`, after sanity checks, calls the `del` method in order to remove the `multiplier` from the Config.
3. `onRemoveSucceeded` prints out the message and `process.exit`s.

