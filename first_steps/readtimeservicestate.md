# read time from the Service on the client side

Make sure you went through the [Consume the Time service](consumetime.md) tutorial and that you are in the `userspace` directory.

Now, edit the file named `readTime.js`

Every `allexruntask` JavaScript script has to export an object with the following properties:

| Property     |  Meaning        |
| --------     | --------------- |
| sinkname     | Name of the Service within the AllexJS cloud that the script needs a Sink to |
| identity     | For now, just use the trivial (and most commonly used) object structure: `{name: 'user', role: 'user'}` |
| task         | This has to be an object with the `name` property. In our case, `name` will point to our main function  |

Let our main script function in `readTime.js` be named `main`.

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

Just in order to show that something is going on, let's make `main` print out the `taskobj` and exit:

```javascript
function main (taskobj) {
    console.log(taskobj);
    process.exit(0);
}
```

## run the script

In your terminal, execute

```
allexruntask readTime.js
```

or just

```
allexruntask readTime
```

(the extension `.js` is not needed)

and you will get a pretty massive output

```
parseProgram 3775
{ sink:
   UserSink {
     destroyed:
      HookCollection {
        head: [Object],
        tail: [Object],
        length: 1,
        controller: [Object] },
     __dyingException: null,
     clientuser:
      SinkClientUser {
        destroyed: [Object],
        __dyingException: null,
        client: [Object],
        oobsink: null,
        channels: [Object] },
     state: null,
     oobsink: null },
  taskRegistry:
   TaskRegistry {
     modulesDone:
      DIContainer {
        _instanceMap: [Object],
        _deferMap: [Object],
        _listeners_map: [Object] },
     ctors: Map { root: [Object], count: 20, controller: [Object] },
     rtExceptions: MapOfArrays { root: null, count: 0, controller: [Object] } },
  execlib:
   { lib:
      { jsonschema: [Object],
        Error: [Function: AllexError],
        JSONizingError: [Function: AllexJSONizingError],
        NotAnAllexErrorError: [Function: NotAnAllexErrorError],
        dummyFunc: [Function: dummyFunc],
        functionCopy: [Function: functionCopy],
        traverseShallow: [Function: traverseShallow],
        traverseShallowConditionally: [Function: traverseShallowConditionally],
        traverse: [Function: traverse],
        traverseConditionally: [Function: traverseConditionally],
        extend: [Function: extend],
        extendShallow: [Function: extendShallow],
        pick: [Function: pick],
        pickExcept: [Function: pickExcept],
        inherit: [Function: inherit],
        inheritMethods: [Function: inheritMethods],
        isFunction: [Function: isFunction],
        isArray: [Function: isArray],
        isUndef: [Function: isUndef],
        defined: [Function: defined],
        isString: [Function: isString],
        isNumber: [Function: isNumber],
        isNull: [Function: isNull],
        isNotNull: [Function: isNotNull],
        isDefinedAndNotNull: [Function: isDefinedAndNotNull],
        isBoolean: [Function: isBoolean],
        isVal: [Function: isVal],
        isEqual: [Function: isEqual],
        has: [Function: has],
        isInteger: [Function: isInteger],
        Destroyable: [Function: Destroyable],
        SimpleDestroyable: [Object],
        ComplexDestroyable: [Function: ComplexDestroyable],
        Gettable: [Object],
        Settable: [Object],
        Changeable: [Object],
        Listenable: [Object],
        ChangeableListenable: [Object],
        CLDestroyable: [Function: CLDestroyable],
        Configurable: [Object],
        CBMapable: [Object],
        prependToString: [Function: prependToString],
        thousandSeparate: [Function: thousandSeparate],
        readPropertyFromDotDelimitedString: [Function: readPropertyFromDotDelimitedString],
        toIndentedJson: [Function: toIndentedJson],
        querystring: [Object],
        getMac: [Function: getMac],
        isMac: [Function: isMac],
        initUid: [Function: init],
        uid: [Function: uniqueSessionId],
        setImmediate: [Function],
        clearImmediate: [Function],
        runNext: [Function: runNext],
        destroyASAP: [Function],
        clearTimeout: [Function],
        intervals: [Object],
        DList: [Function: DoubleLinkedList],
        Fifo: [Function: Fifo],
        StringBuffer: [Object],
        DeferFifo: [Function: DeferFifo],
        DeferMap: [Function: DeferMap],
        Map: [Function: Map],
        SortedList: [Function: List],
        arryDestroyEl: [Function: arryDestroyEl],
        arryDestroyAll: [Function: arryDestroyAll],
        arryNullEl: [Function: arryNullEl],
        arryNullAll: [Function: arryNullAll],
        objNullAll: [Function: objNullAll],
        objDestroyAll: [Function: objDestroyAll],
        containerDestroyAll: [Function: containerDestroyAll],
        containerDestroyDeep: [Function: containerDestroyDeep],
        HookCollection: [Function: HookCollection],
        q: [Object],
        qlib: [Object],
        isIdentical: [Function: equals],
        ListenableMap: [Object],
        request: [Function: request],
        DIContainer: [Function: DIContainer],
        moduleRecognition: [Function: bound ],
        capitalize: [Function: capitalize],
        ws: [Object],
        DestinationError: [Function: DestinationError],
        UnsupportedProtocolError: [Function: UnsupportedProtocolError],
        UnconnectableError: [Function: UnconnectableError],
        NoServerError: [Function: NoServerError] },
     loadDependencies: [Function: loadDependencies],
     execSuite:
      { RegistryBase: [Function: RegistryBase],
        additionalRegistries: [Object],
        streamSourceCreator: [Function: createStreamSource],
        StreamSink: [Function: StreamSink],
        StreamBlackHole: [Function: BlackHole],
        StreamSinkBunch: [Function: StreamSinkBunch],
        StreamDistributor: [Function: StreamDistributor],
        StateCoder: [Function: StateCoder],
        StateSource: [Object],
        State2Array: [Function: Stream2Array],
        State2Defer: [Function: Stream2Defer],
        StateDecoder: [Function: StreamDecoder],
        State2Map: [Function: Stream2Map],
        StatePathModifier: [Function: StreamPathModifier],
        StatePathListener: [Function: StreamPathListener],
        StateCollectionListener: [Function: CollectionListener],
        StateScalarListener: [Function: ScalarListener],
        StateSubServiceExtractor: [Function: SubServiceExtractor],
        ADS: [Object],
        Collection: [Function: Collection],
        LimitedCollection: [Function: LimitedCollection],
        libRegistry: [Object],
        talkerSpawner: [Function: factory],
        clientFactory: [Function: produceClient],
        dependentMethod: [Function: dependentMethod],
        taskRegistry: [Object],
        ResolvableTaskError: [Function: ResolvableError],
        Task: [Function: Task],
        DestroyableTask: [Function: DestroyableTask],
        MultiDestroyableTask: [Function: MultiDestroyableTask],
        SinkTask: [Function: SinkTask],
        registry: [Object],
        Callable: [Object],
        dependentServiceMethod: [Function],
        userSessionFactoryCreator: [Function: createFactory],
        RemoteServiceListenerServiceMixin: [Object],
        installFromError: [Function: install],
        firstFreePortStartingWith: [Function: reserve],
        checkPort: [Function: check],
        tmpPipeDir: [Function: tempPipeDir],
        acquireAuthSink: [Function: acquireAuthSink],
        start: [Function],
        lanManagerPorts: [Object],
        machineManagerPorts: [Object],
        tcpTransmissionStartingPort: 24000,
        sinkNameFromString: [Function: sinkNameFromString] },
     serverLoggingSetup: [Function: serverLoggingSetup],
     dataSuite:
      { storageRegistry: [Object],
        recordSuite: [Object],
        ObjectHive: [Object],
        inherit: [Function: inherit],
        filterFactory: [Object],
        QueryBase: [Function: QueryBase],
        QueryClone: [Function: QueryClone],
        DataSource: [Object],
        DataDecoder: [Function: Decoder],
        StreamDistributor: [Function: DataStreamDistributor],
        DataManager: [Function: DataManager],
        DistributedDataManager: [Function: DistributedDataManager],
        SpawningDataManager: [Function: SpawningDataManager],
        StorageBase: [Function: StorageBase],
        NullStorage: [Function: NullStorage],
        CloneStorage: [Function: CloneStorage],
        MemoryStorageBase: [Function: MemoryStorageBase],
        AsyncMemoryStorageBase: [Function: AsyncMemoryStorageBase],
        MemoryStorage: [Function: MemoryStorage],
        MemoryListStorage: [Function: MemoryListStorage] } } }
```

A fully detailed explanation of what is displayed here exceeds the scope of this tutorial.
For now, let's just focus on the following facts:

1. taskobj has the following properties: `sink`, `taskRegistry` and `execlib`
2. for simple `allexruntask` scripts, `sink` is all that matters
3. `sink` is not null, but rather an instance of the `UserSink` class (exactly what it should be)
4. for slightly more complicated `allexruntask` scripts, we will make use of `taskRegistry` as well

So, after a simple check, let's use the sink

## use the taskRegistry to materialize the state of the Sink and start listening on the state

Now we shall modify the `main` function:

```javascript
function main (taskobj) {
  /* prepare the vars in advance (good ol' ES5) */
  var state, taskRegistry;

  /* do the sanity checks and exit accordingly */
  if (!(taskobj && taskobj.sink)) {
    process.exit(0);
    return;
  }

  /* Just for less clutter in the code */
  taskRegistry = taskobj.taskRegistry;

  /* Run the 'materializeState' task */
  state = taskRegistry.run('materializeState', {
    sink: taskobj.sink
  });

  /* Run the 'readState' task */
  taskRegistry.run('readState', {
    state: state,
    name: 'time',
    cb: console.log.bind(console, 'time')
  });
}
```

Now, when you run `allexruntask readTime`, (provided that `allexmaster` is running, with its `Time` child process) you will read out something like this in the console:

```
$ allexruntask readTime
parseProgram 3786
time 1502459401468 undefined
time 1502459402469 1502459401468
time 1502459403473 1502459402469
time 1502459404471 1502459403473
time 1502459405472 1502459404471
time 1502459406476 1502459405472
```

`parseProgram 88704` comes from `allexruntask` itself. Its `programParser` is reporting the pid it is running under.

All subsequent lines have 3 elements:

1. The `time` word. It is there because it was bound to `console.log` as the `cb` of `readState`
2. The newly acquired value of the `time` property of the remote `Time` service.
3. The previous value of the `time` property of the remote `Time` service. Note the `undefined` in the first report line. Initially, the previous value of the `time` property was `undefined`.



