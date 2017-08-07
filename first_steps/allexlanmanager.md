# Start the allexlanmanager

1. Make sure you have followed the steps described in [Setup your environment](../first_steps/setup.md) (You have `allex`, `allexsdk` and `allextesting` installed, and `.allexns.json` deployed in your user's root directory)
2. Make a directory where you will start the allexlanmanager. Name it whatever you want, but make sure it is a fresh and empty directory.
3. Start your terminal program.
4. Enter the directory that you created in step 1.
5. Run the command `allexlanmanager`

## If you never ran any AllexJS command before

In your terminal you will get lines similar to these:

```
$ allexlanmanager
Fri Aug 04 2017 12:58:33 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_directoryservice
Fri Aug 04 2017 12:58:37 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_directorylib
Fri Aug 04 2017 12:58:40 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_jsonparser
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_lanmanagerservice
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager starting with conf { boot: { portcorrection: 0 },
  nat:
   [ { iaddress: '192.168.1.1',
       iport: 12345,
       eaddress: '1.2.3.4',
       eport: 80 } ],
  subnets: [ '192.168.1.0/8' ] }
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager correcting { protocol: { name: 'ws' }, port: 23451 } by 0
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager correcting { protocol: { name: 'socket' }, port: 23551 } by 0
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager ipstrategies [ { ip: '127.0.0.1/32', role: 'user' },
  { ip: '192.168.1.0/8', role: 'user' } ]
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager running in /home/developer/test/allexlanmanager
Fri Aug 04 2017 12:58:48 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_remoteserviceneedingservice
Fri Aug 04 2017 12:58:51 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_availablelanservicesservice
Fri Aug 04 2017 12:58:54 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_engagedmodulesservice
Fri Aug 04 2017 12:58:58 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_natservice
Fri Aug 04 2017 12:59:01 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_needingservice
Fri Aug 04 2017 12:59:07 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_dataservice
Fri Aug 04 2017 12:59:11 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_servicecollectionservice
Fri Aug 04 2017 12:59:15 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_datafilterslib
Fri Aug 04 2017 12:59:18 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_servicecontainerservice
Fri Aug 04 2017 12:59:18 GMT+0200 (CEST) DEBUG lanmanager 8641 registering supersink LanManager (allex_lanmanagerservice:service)
Fri Aug 04 2017 12:59:21 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_serviceneedservice
Fri Aug 04 2017 12:59:25 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_needservice
```

Now let's go through the lines, and see what's going on here:

### You ran the command allexlanmanager

```
$ allexlanmanager
```

This command first needs to read the current working directory in order to find a directory named `.allexlanmanager`.
In order to do so, it needs the service `allex_directoryservice`.

However, if you never ran any AllexJS command before, the module `allex_directoryservice` is not installed.
AllexJS runtime finds out that `require ('allex_directoryservice')` fails because such a module does not exist.

So, the [Module recognition](../development_basics/module_recognition.md) layer is activated.
It recognizes the module name `allex_lanmanagerservice` (because you have the basic `.allexns.json` deployed in your user root directory),
and finally `npm install allex_directoryservice` is ran.

### allex_directoryservice gets installed

```
Fri Aug 04 2017 12:58:33 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_directoryservice
```

Once you see this line, there is an existing module directory `~/lib/node_modules/allex_directoryservice`.

However, `allex_directoryservice` depends on `allex_directorylib` that actually does the work of reading/writing/traversing the file system,
so, the [Module recognition](../development_basics/module_recognition.md) layer is activated again.

### allex_directorylib gets installed

```
Fri Aug 04 2017 12:58:37 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_directorylib
```

Now, `allex_directorylib` has to find a file `.allexlanmanager/needs` and retrieve its contents.
If such a file is not found, `allex_directorylib` will create the file `.allexlanmanager/needs` with default contents, and retrieve that contents.

The same type of task has to be done for the following files:

- `.allexlanmanager/boot`
- `.allexlanmanager/nat`
- `.allexlanmanager/subnets`

In order to manipulate the `.allexlanmanager/needs` file (which is a JSON file), it needs the `allex_jsonparser`.
So, the [Module recognition](../development_basics/module_recognition.md) layer is activated.


### allex_jsonparser gets installed

```
Fri Aug 04 2017 12:58:40 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_jsonparser
```

Now, the task of reading/initial creation of the JSON file `.allexlanmanager/needs` is done, 
and (since you're running `allexlanmanager` for the first time) 

`.allexlanmanager/needs` has the following contents:

```json
[
  {
    "modulename": "allex_timeservice",
    "instancename": "Time",
    "propertyhash": {}
  }
]
```

`.allexlanmanager/boot` has the following contents:

```json
{
  "portcorrection": 0
}
```

`.allexlanmanager/nat` has the following contents:

```json
[
  {
    "iaddress": "192.168.1.1",
    "iport": 12345,
    "eaddress": "1.2.3.4",
    "eport": 80
  }
]
```

`.allexlanmanager/subnets` has the following contents:

```json
[
  "192.168.1.0/8"
]
```

### An instance of allex_lanmanagerservice get started

Once the `.allexlanmanager/needs` file is read, AllexJS runtime has to start an instance of `allex_lanmanagerservice` that will do the job of `allexlanmanager`.
So, the [Module recognition](../development_basics/module_recognition.md) layer is activated.

### allex_lanmanagerservice gets installed

```
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_lanmanagerservice
```

### Instance of allex_lanmanagerservice starts doing its job

```
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager starting with conf { boot: { portcorrection: 0 },
  nat:
   [ { iaddress: '192.168.1.1',
       iport: 12345,
       eaddress: '1.2.3.4',
       eport: 80 } ],
  subnets: [ '192.168.1.0/8' ] }
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager correcting { protocol: { name: 'ws' }, port: 23451 } by 0
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager correcting { protocol: { name: 'socket' }, port: 23551 } by 0
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager ipstrategies [ { ip: '127.0.0.1/32', role: 'user' },
  { ip: '192.168.1.0/8', role: 'user' } ]
Fri Aug 04 2017 12:58:44 GMT+0200 (CEST) DEBUG lanmanager running in /home/developer/test/allexlanmanager
```

The `allex_lanmanagerinstance` reports the initial state of `boot`, `nat` and `subnets` found in the `.allexlanmanager` directory.

The meaning/purpose of these files will be explained later in this tutorial.

However, in order to start serving the contents of the `needs` file, it has to start subservices.
There will be one subservice per need found in the `needs` file.
Each subservice will be an instance of `allex_needservice`.
All these subservices will be controlled by an instance of `allex_remoteserviceneedingservice`.
But

1. `allex_remoteserviceneedingservice` inherits from `allex_needingservice`. 
2. `allex_needingservice` inherits from `allex_servicecollectionservice`.
3. `allex_servicecollectionservice` inherits from `allex_servicecontainerservice`.
4. `allex_servicecontainerservice` inherits from `allex_dataservice`.
5. `allex_dataservice` depends on `allex_datafilterslib`

In addition, it has to start serving the contents of the `nat` file. Therefore it needs to start a subservice that will be an instance of `allex_natservice`.
But

1. `allex_natservice` inherits from `allex_dataservice`.

In addition, it has to start a subservice that will be an instance of `allex_availablelanservicesservice`. 
But

1. `allex_availablelanservicesservice` inherits from `allex_dataservice`.

This seems to be a pretty clumsy mess of mutual dependencies, but having `.allexns.json` in place,

1. all the appropriate dependency module names will be resolved, 
2. the needed modules will be installed
3. whenever all the dependencies for a particular module are met, the module gets registered by the AllexJS runtime, and the boot process of allexlanmanager continues

### all the modules needed for allexlanmanager to start are installed and the instance of allex_lanmanagerservice is registered

```
Fri Aug 04 2017 12:58:48 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_remoteserviceneedingservice
Fri Aug 04 2017 12:58:51 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_availablelanservicesservice
Fri Aug 04 2017 12:58:54 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_engagedmodulesservice
Fri Aug 04 2017 12:58:58 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_natservice
Fri Aug 04 2017 12:59:01 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_needingservice
Fri Aug 04 2017 12:59:07 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_dataservice
Fri Aug 04 2017 12:59:11 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_servicecollectionservice
Fri Aug 04 2017 12:59:15 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_datafilterslib
Fri Aug 04 2017 12:59:18 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_servicecontainerservice
Fri Aug 04 2017 12:59:18 GMT+0200 (CEST) DEBUG lanmanager 8641 registering supersink LanManager (allex_lanmanagerservice:service)
```

At this moment, the instance of `allex_lanmanagerservice`, named `LanManager`, is registered and accessible from the LAN.
LAN accessibility will be explained further in this tutorial.


### the remaining modules needed to serve the needs are installed

```
Fri Aug 04 2017 12:59:21 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_serviceneedservice
Fri Aug 04 2017 12:59:25 GMT+0200 (CEST) DEBUG lanmanager 8641 npm installed the missing module allex_needservice
```

### mind the pid of the LanManager process

In all the terminal output lines above, you will see that they all 

1. begin with the timestamp 
2. followed by the word DEBUG. This word describes that the lines were produced from JavaScript using `console.log`
3. followed by the word `lanmanager`. This is the AllexJS name of the process.
4. followed by the number `8641`. This is the pid of the `lanmanager` process.

## If you run allexlanmanager with all the needed modules installed

```
$ allexlanmanager
Fri Aug 04 2017 14:11:30 GMT+0200 (CEST) DEBUG lanmanager starting with conf { boot: { portcorrection: 0 },
  nat:
   [ { iaddress: '192.168.1.1',
       iport: 12345,
       eaddress: '1.2.3.4',
       eport: 80 } ],
  subnets: [ '192.168.1.0/8' ] }
Fri Aug 04 2017 14:11:30 GMT+0200 (CEST) DEBUG lanmanager correcting { protocol: { name: 'ws' }, port: 23451 } by 0
Fri Aug 04 2017 14:11:30 GMT+0200 (CEST) DEBUG lanmanager correcting { protocol: { name: 'socket' }, port: 23551 } by 0
Fri Aug 04 2017 14:11:30 GMT+0200 (CEST) DEBUG lanmanager ipstrategies [ { ip: '127.0.0.1/32', role: 'user' },
  { ip: '192.168.1.0/8', role: 'user' } ]
Fri Aug 04 2017 14:11:30 GMT+0200 (CEST) DEBUG lanmanager running in /Users/andra/trash/bla1
Fri Aug 04 2017 14:11:31 GMT+0200 (CEST) DEBUG lanmanager 9608 registering supersink LanManager (allex_lanmanagerservice:service)
```

Note that now there are no module installation notifications.
The pid of the `lanmanager` process is (naturally) different.

# How does LanManager do its job?

## Start listening on 2 ports for 2 distinct protocols

Out of all the [AllexJS communication protocols](development_basics/communication_protocols.md), `LanManager` exposes the following 2:

1. `ws` protocol, listening on port `23451 + boot.portcorrection`
2. `socket` protocol, listening on port `23551 + boot.portcorrection`

Therefore, the `portcorrection` property of the `boot` object found in the `boot` file allows for "project coexistence" on the same machine.
This will come in handy when you have to develop several protocols on the same machine.
Just like anything else, the `portcorrection` mechanism is limited, allowing for only 100 different values (in the `[0, 99]` range).
Also, the schema for assigning different `portcorrection` values for different protocols is left to the developer.

## Start serving the nat

As you already know, with the good old IPv4 there exists the "network address translation" of some kind
that may translate certain internal (LAN/internal address : port) pairs to (WAN/external address : port) pairs.

A similar problem exists in the life of LanManager. AllexJS processes may access the LanManager __only__ within the same LAN.
But, these processes may need to be exposed to the outer world. `nat` takes care of how this exposition will be made.

The default value of the `.allexlanmanager/nat` will very probably be useless in your particular LAN layout case,
but we will address the `nat` problem in full detail in more advanced tutorials (when `nat` really starts to matter).

## Start the LAN access filter

The LAN access filter is defined by the contents of the `.allexlanmanager/subnets` file.

This file is a JSON of an array of CIDR strings.

The final LAN access filter will let only processes coming from LAN addresses that match the CIDR strings defined in `.allexlanmanager/subnets` file.

__Note__ that the CIDR `127.0.0.1/32` is always active, meaning that the processes coming from the same machine are always accepted.

The default CIDR stated in the default `subnets` file is `192.168.1.0/8`. Check your LAN network setup and adjust this value accordingly.
Additionaly, you may add more CIDRs for processes coming from other LAN subnets.

## Serving the needs

Finally, but most importantly, LanManager handles the __needs__.

The needs are defined in the `.allexlanmanager/needs` file.
This file (and extensions/alterations to its contents) will be thoroughly discussed in more advanced tutorials.

For now, let's take a look at the default need put there during the initial run:

```
{
  "modulename": "allex_timeservice",
  "instancename": "Time",
  "propertyhash": {}
}
```

A need is an object. In its most basic form, it has 3 basic properties:

1. "modulename" - the module name of the service to be instantiated
2. "instancename" - the name of the instance of "modulename". This name will later be used by AllexJS to find the service within the LAN.
3. "propertyhash" - the configuration object passed over to the Service constructor. If there is nothing to pass, the default value is "{}".

The default need specifies that an instance of `allex_timeservice` named `Time` should be instantiated with no particular configuration parameters.

## Wrapping it all up

So, the LanManager does its job by

1. Exposing the needs for AllexJS services in its LAN
2. By listening on `ws 23451 + boot.portcorrection` and `socket 23551 + boot.portcorrection` ports
3. Accepting only processes from `127.0.0.1/32` and CIDRs listed in `subnets`
4. `nat`ting the [internal address:port] pairs to [extenal address:port] pairs


# Who will fulfill the needs?

[allexmaster](allexmaster.md) process is the workhorse of the AllexJS framework.

