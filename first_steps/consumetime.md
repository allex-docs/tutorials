# Consume the Time service

In AllexJS terms, _consuming_ the service may mean several things. Two essential forms of service consumption are:

- listening to the changes of the Service state
- issuing commands to the Service

__Note__ that a Service may be built so that it does nothing with its state (nothing to listen for), and exposes no callable commands to the outer world.
Such services serve other purposes that will be covered in separate tutorials.

However you are about to consume a Service (or make it useful by any means), the first and fundamental task is to get hold of a __sink__ to the Service.

[Service Sink](../development_basics/sink.md) is the interface of the Service to the outer world.
In server/client terms, 

- the Service instance is a _server_, and the Sink is the interface of the _server_ on the _client_ side.
- _Client_ holds a _sink_ to the _Service_.
- _Sink_ enables the _Client_ to act against the _Service_.
- _Sink_ exposes a particular API to the _Service_.
- There are different APIs to the same _Service_, depending on the _role_ you are using to access the _Service_.

## allexruntask

This is a rather simple way of 

1. obtaining a Sink to the Service
2. consuming the Service via the obtained Sink


In order to make use of `allexruntask`, you will need to write some JavaScript.

Therefore, in the directory where you run your `allexmaster` (the directory where your `.allexmasterconfig.json` file resides)

1. make a directory named `userspace` (this name is not mandatory, but it is an established convention in AllexJS)
2. enter the directory `userspace`

Now that we're in the `userspace` directory, let's script some Time Service consumption.


### before we start

In order to have the `allexruntask` functionality working - and your `allexruntask` scripts producing desired results, make sure you have

1. `allexlanmanager` running from its directory
2. `allexmaster` running in the directory that is the parent directory of the `userspace` directory where you're scripting

This point 2. you can easily verify: if `allexmaster` is running in a particular directory, then there is a `allexmaster.pid` file in that directory.
The only contents of `allexmaster.pid` file is the pid of the allexmaster running in the directory.

So, the directory structure of your software should look like this:

```
allexlanmanager_directory/
+- .allexlanmanager/
   |- boot
   |- nat
   |- needs
   +- subnets
   
allexmaster_directory/
|- .allexmasterconfig.json
|- allexmaster.pid
+- userspace\
   +- <you are here, making and running your `allexruntask` scripts>
```

Now that you've got here, you're ready to do some AllexJS scripting:

- [Read the state of the `Time` service](readtimeservicestate.md)
- [Issue a Remote Method Invocation to the `Time` service](calltimeservice.md)