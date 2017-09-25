# Consume the `Multiplier` service

The consumption will again be done via an `allexruntask` script.
After the standard steps

1. Acquire a Sink to the `Multiplier` service
2. Perform sanity checks on the `taskobj`

We will enter a loop that will last until

- The sink to `Multiplier` exists
- The call to the `multiply` method resolves

The loop will call `multiply` on the sink acquired, sending a random number in the `[1, 10]` range.
We will intentionally avoid sending a `0` value to be multiplied.
Once the call is resolved or rejected, `multiply` will be called again in a second.

Here is the code for the `doMultiply.js` in the `userspace` subdirectory of the project directory:

```javascript
function randomNumber () {
  return Math.floor(10*Math.random()+1);
}

function onMultiplySucceeded (sink, mynumber, result) {
  console.log('multiply succeeded for number', mynumber, 'yielding', result);
  multiplyAgain(sink);
}

function onMultiplyFailed (sink, mynumber, reason) {
  console.log('multiply failed for number', mynumber, 'because', reason);
  multiplyAgain(sink);
}

function multiplyAgain (sink) {
  var seconds = 1;
  console.log('will retry the multiplication in', seconds, 'seconds');
  setTimeout(doMultiply.bind(null, sink), seconds*1000);
}

function doMultiply (sink) {
  var rn;
  if (!sink.destroyed) {
    console.error('sink is destroyed, ending the process');
    process.end(1);
    return;
  }
  rn = randomNumber();
  sink.call('multiply', rn).then(
    onMultiplySucceeded.bind(null, sink, rn),
    onMultiplyFailed.bind(null, sink, rn)
  );
}

function go (taskobj) {
  if (!(taskobj && taskobj.sink)) {
    process.exit(1);
  }
  doMultiply(taskobj.sink);
}

module.exports = {
  sinkname: 'Multiplier',
  identity: {name: 'user', role: 'user'},
  task: {
    name: go
  }
};
```

Naturally, because the very consumption of the `Multiplier` service depends on the behavior of the `Multiplier` service itself, this script will behave differently when the `Multiplier` service

- Accepts the `null` value for the remote `multiplier` config parameter
- Does not accept the `null` value for the remote `multiplier` config parameter and rejects the call in that case
- Waits for the remote `multiplier` config parameter to be acquired and then resolves the `multiply` call
