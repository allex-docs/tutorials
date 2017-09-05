# State

## What is the state?

AllexJS Service state is an implementation of the [Gang of Four Observer pattern](http://www.blackwasp.co.uk/Observer.aspx).

![observer pattern](http://i.imgur.com/meQkfvh.png)

In the state implementation, Subject is the `state` property of every Service instance (introduced by the base Service class),
and ObserverA, ObserverB, ObserverC are Sinks.

`state` is an instance of the `Collection` class.
The `Collection` class is a specialization of the `Map` class.
Therefore, the basic task of `state` is to maintain a _name_ => _value_ mapping.

However, the feature that distinguishes `Collection` from `Map` is that `Collection` also raises events during any change in its structure.
This feature allows for state change listeners.
The AllexJS infrastructure further introduces special listeners on the `state` that distribute the structure changes in a `Stream`-able fashion.

Therefore, any changes made on the `state` will travel through the AllexJS distributed infrastructure and reach all the clients.
Clients may express their interest in the changes of the `state` - the most convenient form to do this is to use the `materializeState` and `readState` AllexJS Tasks.

The concept of `state` allows for a family of client-server synchronization use cases.

## Who changes the state?

The most basic answer to this question is: _The Server side_.

However, _The Server side_ turns out to be layered:

1. The core of the Server side is the Service instance.
2. In order to handle client connections, Service spawns Users of appropriate name. Name is always related to client connection parameters.

Therefore, the Service may be visualised as a center of its "solar system":

1. Users gravitate to the Service because they are spawned by it.
2. Users may access the Service instance via a special property `__service`, introduced by the base `User` class.
3. Every User instance has its own `state`.
4. User's `state` reflects the state of its `__service`.
5. User is free to change its `state`. Thereby, User's `state` may diverge from the __service's state.
6. Remote client is connected _only_ to the User, _never_ directly to the User's __service.


So, there is a hierarchical nature in handling the `state`:

1. Changes on the `state` of the Service will be broadcast to all User instances.
2. Changes on the `state` of the Service will result in equal changes on the `state`s of all User instances.
3. User JavaScript code will define which properties of the `state` will be broadcast to the remote Clients. By default, __no__ `state` property will leave the User towards the Sink.



