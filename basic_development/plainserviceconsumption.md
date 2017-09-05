# Consuming the Multiplier Service

We will make an `allexruntask` script to send a number to the Multiplier Service and prove that we will get back our original number multiplied with 2.

## Create the `userspace` subdirectory

```
mkdir userspace
cd userspace
```

## Create the script

Edit the file `doMultiply.js` so that it looks like this:

```javascript
function go (taskobj) {
  /* do the sanity checks and exit accordingly */
  if (!(taskobj && taskobj.sink)) {
    process.exit(0);
    return;
  }

  taskobj.sink.call('multiply', 2).then(
    console.log.bind(console, 'result'),
    console.error.bind(console, 'error')
  );
}

module.exports = {
  sinkname: 'Multiplier',
  identity: {
    role: 'user',
    name: 'user'
  },
  task: {
    name: go
  }
};
```

## Run the script

`allexruntask doMultiply.js`

and obtain the script output similar to

```
$ allexruntask doMultiply.js
parseProgram 87437
result 4
```
