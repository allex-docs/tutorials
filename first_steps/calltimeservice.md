# Issue a Remote Method Invocation (RMI) call to the Time service


Make sure you went through the [Consume the Time service](consumetime.md) tutorial and that you are in the `userspace` directory.

Now, edit the file named `setTimeActive.js`

Every `allexruntask` JavaScript script has to export an object with the following properties:

| Property     |  Meaning        |
| --------     | --------------- |
| sinkname     | Name of the Service within the AllexJS cloud that the script needs a Sink to |
| identity     | For now, just use the trivial (and most commonly used) object structure: `{name: 'user', role: 'user'}` |
| task         | This has to be an object with the `name` property. In our case, `name` will point to our main function  |

Let our main script function in `setTimeActive.js` be named `main`.

Therefore, this is the initial script:

```javascript
function main (taskobj) {
}

module.exports = {
    sinkname: 'Time',
    identity: {name: 'user', role: 'user'},
    task: {
        name: main
    }
};
```

If you're interested in some trivial initial invocation of the script, you may take a look at [initial invocation of the readTime script](readtimeservicestate.md#run_the_script).

## Trivial invocation

In this tutorial, we will immediately write some useful code in the `main` function:

```javascript
function main (taskobj) {
  /* prepare the vars in advance (good ol' ES5) */
  var state, taskRegistry;

  /* do the sanity checks and exit accordingly */
  if (!(taskobj && taskobj.sink)) {
    process.exit(0);
    return;
  }

  taskobj.sink.call('setActive', false);
}
```

It is obvious that the one and only crucial line is

```javascript
  taskobj.sink.call('setActive', false);
```

`call` is the Sink's method that allows one to issue RMIs to remote Services.
Sink will take care of method name validation and parameter validation, and finally issue the call to the `Time` service.

When you run the script with `allexruntask setTimeActive`, you will notice nothing significant in your terminal:

```
$ allexruntask setActive
parseProgram 3803
```

But, take a look at the terminal that is running `allexmaster`.
You will notice that the "ticking" in the terminal has stopped.
That is because you have set the `active` flag on the `Time` service to `false`.
The `Time` service is still alive and running, but it is inactive and does not tick.


### What's "trivial" in this trivial invocation?

As you may have noticed, the invocation of `allexruntask setTimeActive` has never finished. The script has

1. Acquired a Sink to the `Time` Service
2. Issued a call for the `setActive` method with the `false` parameter

However, this script has no idea about the fate of the call it issued.
This is because it did not use the result of the `call` method.

So, use `<Ctrl>-C` to abort the execution of `allexruntask setTimeActive`, and make some use of the result of the `call` method.


## The call method returns a promise

If you're not into JavaScript Promises, this is the time you got out there and understood fundamentally how Promises work and why they exist at all, because the AllexJS is mostly all about Promises.
Since the `call` method, returns a Promise, we'll be using `then` to control its execution.

```javascript
function main (taskobj) {
  /* prepare the vars in advance (good ol' ES5) */
  var state, taskRegistry;

  /* do the sanity checks and exit accordingly */
  if (!(taskobj && taskobj.sink)) {
    process.exit(0);
    return;
  }

  taskobj.sink.call('setActive', false).then(
      (result) => {
        console.log('setActive succeeded', result);
        process.exit(0);
      },
      (reason) => {
        console.log('setActive failed', reason);
        process.exit(1);
      }
  );
}
```

We used the ES6 "arrow functions" for code clarity.
However, note that the whole AllexJS core was written in ES5, for compatibility with older browser versions.

The promise fulfill and reject handlers are as simple as possible, but they provide you with sufficient information about the fate of the `call` execution.

Now, run `allexruntask setTimeActive`, and get something like

```
$ allexruntask setActive
parseProgram 3819
setActive succeeded true
```

So, your script has recognized the moment of receiving the result of successful invocation of the Remote Method `setActive`.
`Time` Service has invoked the `setActive` method with the `false` parameter, and returned `true` as the invocation call result.
After receiving the `true` result back from `Time` service, the script has logged the notification to the console, and exited the process with 0 result.

### Unsuccessful invocation examples

Ok, everything nice and neat, but what would happen if the invocation failed?

#### Wrong method name

Instead of having the `call` method call the `setActive` Remote Method, let's try to invoke the `setActive1` method:

```javascript
function main (taskobj) {
  /* prepare the vars in advance (good ol' ES5) */
  var state, taskRegistry;

  /* do the sanity checks and exit accordingly */
  if (!(taskobj && taskobj.sink)) {
    process.exit(0);
    return;
  }

  taskobj.sink.call('setActive1', false).then(
      (result) => {
        console.log('setActive succeeded', result);
        process.exit(0);
      },
      (reason) => {
        console.log('setActive failed', reason);
        process.exit(1);
      }
  );
}
```

Now run `allexruntask setTimeActive`, and get something like

```
$ allexruntask setActive
parseProgram 3833
Trace
    at SinkClientUser.ClientUser.genericCall (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/servicesink/clientusercreator.js:108:15)
    at SinkClientUser.ClientUser.call (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/servicesink/clientusercreator.js:121:29)
    at UserSink.ServiceSink.call (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/servicesink/index.js:114:33)
    at Object.main (/Users/andra/trash/bla2/userspace/setActive.js:11:16)
    at FindAndRunTask.onSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findAndRun.js:60:25)
    at FindSinkTask.callbackTheSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findSink.js:221:26)
    at FindSinkTask.acceptSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findSink.js:196:12)
    at FindSinkTask.reportSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findSink.js:164:12)
    at SingleMachineRecordSinkHunter.RemoteSinkHunter.reportSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/sinkhunterscreator.js:317:15)
    at AcquireSinkTask.onSpawnSuccess (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/base/tasks/acquireSink.js:77:12)
Did not yield a methodname, subservice name: undefined has methods:
{ die: true,
  introduceUser: [ { title: 'User property hash', type: 'object' } ],
  subConnect:
   [ { title: 'SubService name', type: 'string' },
     { title: 'Identity', type: 'object' },
     { title: 'SubClientUser constructor', type: 'function' } ],
  requestTcpTransmission: [ { title: 'Options object', type: 'object' } ],
  setActive: [ { title: 'Active', type: 'boolean' } ] } 'got arguments' { '0': '.', '1': 'setActive1', '2': false }
setActive failed { Error: No descriptor for method: setActive1
    at AllexError.Error (native)
    at new AllexError (/Users/andra/lib/node_modules/allex/node_modules/allex_errorlowlevellib/index.js:3:21)
    at SinkClientUser.ClientUser.genericCall (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/servicesink/clientusercreator.js:111:23)
    at SinkClientUser.ClientUser.call (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/servicesink/clientusercreator.js:121:29)
    at UserSink.ServiceSink.call (/Users/andra/lib/node_modules/allex/node_modules/allex_servicepackservercorelib/servicesink/index.js:114:33)
    at Object.main (/Users/andra/trash/bla2/userspace/setActive.js:11:16)
    at FindAndRunTask.onSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findAndRun.js:60:25)
    at FindSinkTask.callbackTheSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findSink.js:221:26)
    at FindSinkTask.acceptSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findSink.js:196:12)
    at FindSinkTask.reportSink (/Users/andra/lib/node_modules/allex_masterservice/tasks/findSink.js:164:12) code: 'NO_METHOD_DESCRIPTOR' }
```

Scary, but get used to it. Watch out for the `code` property of the received Error.
Most of AllexJS Errors will have the `code` property, which is a string.
In this case, the `code` has the value of `'NO_METHOD_DESCRIPTOR'`.

Clearly, this is because there is no method descriptor for the method `setActive1`.
No method descriptor => no way to invoke such a method.

#### Wrong parameter type

Now, let's try to invoke the existing `setActive` method, but with a parameter that is not `boolean`.

```javascript
function main (taskobj) {
  /* prepare the vars in advance (good ol' ES5) */
  var state, taskRegistry;

  /* do the sanity checks and exit accordingly */
  if (!(taskobj && taskobj.sink)) {
    process.exit(0);
    return;
  }

  taskobj.sink.call('setActive', 5).then(
      (result) => {
        console.log('setActive succeeded', result);
        process.exit(0);
      },
      (reason) => {
        console.log('setActive failed', reason);
        process.exit(1);
      }
  );
}
```

This time the output in the terminal will be less messy:

```
$ allexruntask setActive
parseProgram 3847
setActive failed { code: 'Active: is not of a type(s) boolean', message: '' }
```

But the Error `code` is clear: the parameter, named `Active` on the Service side, is not of the `boolean` type.
