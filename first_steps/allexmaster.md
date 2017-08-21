# Start the allexmaster

1. Make sure you have followed the steps described in [Setup your environment](../first_steps/setup.md) (You have `allex`, `allexsdk` and `allextesting` installed, and `.allexns.json` deployed in your user's root directory)
2. Make sure you have started [allexlanmanager](allexlanmanager.md)
2. Make a directory where you will start the allexmaster. Name it whatever you want, but make sure it is a fresh and empty directory.
3. Start your terminal program.
4. Enter the directory that you created in step 3.
5. Run the command `allexmaster`

In your terminal you will get an output similar to this:

```
$ allexmaster
Mon Aug 07 2017 11:49:23 GMT+0200 (CEST) DEBUG allexmaster 1884 npm installed the missing module allex_masterservice
Mon Aug 07 2017 11:49:24 GMT+0200 (CEST) DEBUG allexmaster running acquireSink in order to find LAN manager ws://127.0.0.1:23451
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) DEBUG allexmaster 1884 npm installed the missing module allex_timeservice
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) DEBUG Time tick! next in 1000
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) DEBUG Time 1990 registering supersink Time (allex_timeservice:service)
Mon Aug 07 2017 11:49:34 GMT+0200 (CEST) DEBUG Time tick! next in 996
Mon Aug 07 2017 11:49:35 GMT+0200 (CEST) DEBUG Time tick! next in 997
Mon Aug 07 2017 11:49:36 GMT+0200 (CEST) DEBUG Time tick! next in 996
Mon Aug 07 2017 11:49:37 GMT+0200 (CEST) DEBUG Time tick! next in 998
```

Let's break down what's happening in these lines.

## You ran the command allexmaster

```
$ allexmaster
```

This command first needs to read the current working directory in order to find a file named `.allexmasterconfig.json`.
In order to do so, it needs the service `allex_directoryservice`.
However, this module is already installed in `~/lib/node_modules/allex_directoryservice` during the initial run of `allexlanmanager`.
All the modules needed to read a JSON file (`allex_directorylib`, `allex_jsonparser`) are installed as well.

Finally, the `.allexmasterconfig.json` file is not found, so it is created with a default contents:

```json
{
  "lanmanageraddress": "127.0.0.1",
  "lanmanagerportcorrection": 0,
  "runtimedirectory": ".",
  "portrangestart": {
    "tcp": 15000,
    "http": 16000,
    "ws": 17000
  }
}
```

### lanmanageraddress

This is the IP address (single exact IP address, no CIDR notation allowed) of the LanManager instance that `allexmaster` will be looking for.

### lanmanagerportcorrection

This number has to match the `portcorrection` property in the `boot` file of the LanManager's `.allexlanmanager` directory.

### runtimedirectory

For most cases, this parameter should be left at its initial value `"."`.
Changing this parameter makes sense only in cases of embedded AllexJS deployments, that will be covered in a separate tutorial.

### portrangestart

Out of all the [AllexJS communication protocols](development_basics/communication_protocols.md), three recognize the concept of the _port number_:

1. `tcp` (sub-case of the `socket` protocol)
2. `http` (this protocol has been abandoned during the AllexJS development due to its inefficiency, but - perhaps - not for good)
3. `ws`

For these protocols, the starting port number is defined in `.allexmasterconfig.json`.
`allexmaster` will be spawning children processes that will start listening on `tcp` and `ws` port numbers.
In order to avoid servers clashing on ports, a special AllexJS service [portoffice](../development_basics/portoffice) will be running on each machine running `allexmaster`.

Again, during the development of the [portoffice](../development_basics/portoffice) service, the starting numbers for the port range were hardcoded within the service.
Therefore, `portrangestart` part of the `.allexmasterconfig.json` is obsolete at the moment.


## allex_masterservice gets installed

`allexmaster` process will be running an instance of `allex_masterservice`.

If you read about [automatic module installation](allexlanmanager.md#if-you-never-ran-any-allexjs-command-before), there's nothing special here.
[Module recognition](../development_basics/module_recognition.md) layer triggers the installation of the `allex_masterservice` module.

```
Mon Aug 07 2017 11:49:23 GMT+0200 (CEST) DEBUG allexmaster 1884 npm installed the missing module allex_masterservice
```

## An instance of allex_masterservice gets started

```
Mon Aug 07 2017 11:49:24 GMT+0200 (CEST) DEBUG allexmaster running acquireSink in order to find LAN manager ws://127.0.0.1:23451
```

The first thing `allexmaster` has to do is to find the LAN manager in its LAN.
The url `ws://127.0.0.1:23451` shows the protocol, IP address and port where the LAN manager is expected.

If you have `allexlanmanager` running on the same machine (`127.0.0.1`), with `portcorrection` 0, (port `23451`), `allexmaster` will find it.

## need is being satisfied

The initial need of your `allexlanmanager` is 

```json
{
  "modulename": "allex_timeservice",
  "instancename": "Time",
  "propertyhash": {}
}
```

(you can read more about it in the [allexlanmanager tutorial](allexlanmanager.md#serving-the-needs))

### allex_timeservice gets installed

[Module recognition](../development_basics/module_recognition.md) layer triggers the installation of the `allex_timeservice` module.

```
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) DEBUG allexmaster 1884 npm installed the missing module allex_timeservice
```

### a process is started that creates an instance of allex_timeservice

`allex_timeservice` is a trivial service that ticks once in a second, see [here](https://github.com/allex-services/time/blob/master/servicecreator.js#L19).
Whenever it ticks, it reports the tick in the console, by announcing the milliseconds in which the next tick will be done.

```
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) DEBUG Time tick! next in 1000
```

This was the initial tick.

### Instance of allex_timeservice named Time is registered

After the initial tick, the instance of `allex_timeservice` is registered

```
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) DEBUG Time 1990 registering supersink Time (allex_timeservice:service)
```

Pay attention to the process pids:

1. `allexmaster` in this case is running in process pid `1884`. You can see that in the report lines for `allex_masterservice` and `allex_timeservice` modules being installed.
2. `allex_timeservice` is instantiated in process pid `1990`.

Also, pay attention to the name of the instance: `Time`.
This name is dictated by the need - served by `allexlanmanager`.

## What's going on at the allexlanmanager side?

If you take a look at the terminal output of the `allexlanmanager`, you will find the following lines:

```
Mon Aug 07 2017 11:49:33 GMT+0200 (CEST) INFO lanmanager need Time (allex_timeservice) with config
 {}
  satisfied by 127.0.0.1:17000 on pid 1990
```

- `allexlanmanager` was contacted to find out the need.
- `allexmaster` started a child process pid 1990.
- That process instantiated the Time instance of `allex_timeservice`.
- In order to get connected with the AllexJS run-time infrastructure, Time started listening on the first available `ws` port (that is `17000`).
- `allexmaster` reports the successful creation to `allexlanmanager`.
- `allexlanmanager` checks for successful need satisfaction, and brings the need down.

## What happens if allexmaster goes down?

In your `allexmaster` directory, hit `<Ctrl>-C` to kill `allexmaster` and all of its child processes (in this tutorial case, it is just one child process, the `Time` process pid 1990).
    

### allexmaster output

Take a look at the `allexmaster` terminal output:

```
^CMon Aug 07 2017 11:51:16 GMT+0200 (CEST) ERROR Time Trace
    at process.exit (/Users/andra/lib/node_modules/allex_masterservice/users/spawn.js:34:11)
    at emitNone (events.js:86:13)
    at process.emit (events.js:185:7)
    at Signal.wrap.onsignal (internal/process.js:199:44)
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) ERROR allexmaster Trace
    at process.exit (/Users/andra/lib/node_modules/allex/master.js:16:11)
    at emitNone (events.js:86:13)
    at process.emit (events.js:185:7)
    at Signal.wrap.onsignal (internal/process.js:199:44)
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) ERROR Time undefined
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) DEBUG Time 1990 cleaning pipes because of 0
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) ERROR allexmaster undefined
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) DEBUG allexmaster 1884 cleaning pipes because of 0
```

Note that now for the fist time there is a new word in the terminal output:
`ERROR` is the word that denotes a line produced with `console.error()`

Since the processes `Time` and `allexmaster` were killed, that is treated as an error.
Stack trace shows that the error was caught by the signal handler installed in every AllexJS process (`allex_masterservice` takes care of that).
But, is there an actual error?

```
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) ERROR Time undefined
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) DEBUG Time 1990 cleaning pipes because of 0
```

Time process had an error `undefined`, meaning that there was no run-time error in the execution of code in the `Time`.
To put it simply, "the time came to become dead", which is exceptional.
The exit code of `Time` process pid 1990 is 0.

```
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) ERROR allexmaster undefined
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) DEBUG allexmaster 1884 cleaning pipes because of 0
```

Time process had an error `undefined`, meaning that there was no run-time error in the execution of code in the `allexmaster`.
The exit code of `allexmaster` process pid 1990 is 0.

### allexlanmanager output
    
Now, take a look at the `allexlanmanager` terminal output.

```
Mon Aug 07 2017 11:51:16 GMT+0200 (CEST) DEBUG lanmanager User 127.0.0.1 down, deleting the record
```

`allexlanmanager` detects that `allexmaster` on 127.0.0.1 is down, so it is deleting all the records created by this `allexmaster`.

__Note__ that there can logically be just one `allexmaster` accessing the `allexlanmanager` from one IP address.
If - for any reason - two or more `allexmaster` processes access `allexlanmanager` from the same IP address, they will be treated as the same __user__.

Finally, the need(s) served by `allexmaster` from 127.0.0.1 are proclaimed unsatisfied.
These needs will be exposed again for eventual satisfaction.
Whenever an `allexmaster` accesses the `allexlanmanager` so that it can satisfy the `Time` need, the need will be satisfied again, and the `Time` need will be brought down by `allexlanmanager`.

## Run allexmaster again

Make sure you're in the test directory for `allexmaster`.

Run `allexmaster`, and the output will look similar to this:

```
$ allexmaster
Mon Aug 07 2017 14:18:08 GMT+0200 (CEST) DEBUG allexmaster running acquireSink in order to find LAN manager ws://127.0.0.1:23451
Mon Aug 07 2017 14:18:09 GMT+0200 (CEST) DEBUG Time tick! next in 1000
Mon Aug 07 2017 14:18:09 GMT+0200 (CEST) DEBUG Time 2672 registering supersink Time (allex_timeservice:service)
Mon Aug 07 2017 14:18:10 GMT+0200 (CEST) DEBUG Time tick! next in 997
Mon Aug 07 2017 14:18:11 GMT+0200 (CEST) DEBUG Time tick! next in 994
Mon Aug 07 2017 14:18:12 GMT+0200 (CEST) DEBUG Time tick! next in 994
```

This time, pid of the Time process is 2672.


### What does allexlanmanager say?

```
Mon Aug 07 2017 14:18:09 GMT+0200 (CEST) INFO lanmanager need Time (allex_timeservice) with config
 {}
  satisfied by 127.0.0.1:17000 on pid 2672
```

So, everything is back to normal. `allexlanmanager` has got all of its one needs satisfied.


## What happens if a single Service process goes down?

Now that we know the pid of the particular `Time` process, let's kill just that process:

```
kill 2672
```

### allexmaster detects its child going down

`allexmaster` reports to `allexlanmanager` that a child is dead

### allexlanmanager puts the need up again

```
Mon Aug 07 2017 14:18:16 GMT+0200 (CEST) DEBUG lanmanager Time is dead
Mon Aug 07 2017 14:18:16 GMT+0200 (CEST) INFO lanmanager Service Time is down, will spawn a new need
```

### allexmaster reads out the new need and satisfies it

```
Mon Aug 07 2017 14:18:16 GMT+0200 (CEST) DEBUG Time 2678 registering supersink Time (allex_timeservice:service)
```

The `Time` service process is up again, this time with pid 2678

### allexlanmanager finds out that the need is satisfied and brings it down

```
Mon Aug 07 2017 14:18:16 GMT+0200 (CEST) INFO lanmanager need Time (allex_timeservice) with config
 {}
  satisfied by 127.0.0.1:17001 on pid 2678
```

# Wrapping it all up

- `allexlanmanager` and `allexmaster` are the basic building blocks for the AllexJS cloud management.
- There will be one `allexlanmanager` per LAN.
- There will be one `allexmaster` per computer (instance) in the cloud.
- The AllexJS architecture allows for a robust operation of the software dispersed in the cloud.
- The functionality of the AllexJS cloud is obtained on a _service process_ basis. Each AllexJS process implements one microservice (although the term _network protocol-based component_ might be more appropriate, see [here](https://apigee.com/about/blog/technology/microservices-bad-name-good-idea))
- The AllexJS cloud is built on a premise that microservices may occasionally go down. This makes it robust and flexible in terms of run-time functionality maintenance.

# What's next?

- [Start `allexmaster` from another machine in the LAN](allexmasterlan.md).
- [Consume the Time service](consumetime.md)