# Using the remote `multiplier` value

At the beginning of this tutorial, it should be noted that you should have completed the [Listen to the Config Service](listentoconfig.md) by now.
On other words, your `MultiplierService` should have the skeleton `onMultiplierSet` and `onMultiplierRemoved` methods at this point.

The behavior of the handlers will affect the nature of the Multiplier service.
Let's analyze the possible scenarios, and have in mind that this type of decision applies to any Service that relies upon data of a remote Service.

## Save the value of the multiplier to a field of the MultiplierService

In this case, the `MultiplierService` constructor would account for a field named `multiplier`:

```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
    this.multiplier = null;
  }
```

The destructor would also account for the same field:

```javascript
  MultiplierService.prototype.__cleanUp = function() {
    this.multiplier = null;
    RemoteServiceListenerServiceMixin.prototype.destroy.call(this);
    ParentService.prototype.__cleanUp.call(this);
  };
```

Mind the AllexJS convention: whatever happens in the constructor, happens in the destructor (`__cleanUp`) in the exact reverse order.

Finally, the handlers would be:

```javascript
  MultiplierService.prototype.onMultiplierSet = function (keyvalarray) {
    var key = keyvalarray[0], val = keyvalarray[1];
    this.multiplier = val;
  };
```

```javascript
  MultiplierService.prototype.onMultiplierRemoved = function (key) {
    this.multiplier = null;
  };
```

In this case, `MultiplierService` might find itself in a situation that a number provided should be multplied by `null`, because the current value of `this.multiplier` might turn out to be `null`.

### Use any current value of `this.multiplier`

```javascript
  MultiplierService.prototype.multiply = function (number) {
    return number*this.multiplier;
  }
```

Moreover, due to the asynchronous nature of execution, it would be better to "promisify" this method:

```javascript
  MultiplierService.prototype.multiply = function (number) {
    return q(number*this.multiplier);
  }
```

where `q` stands for `execlib.lib.q`;

From the aspect of strictness of `MultiplierService`, this is not a problem.
Since, for example,

```javascript
5*null === 0
```

`MultiplierService` would return `0` as a result whenever it is not in possession of the `multiplier` value.

However, from a logical standpoint, this is not quite true.
"Not having a multiplier" is not equivalent to "having a multiplier equal to 0".

This implementation of the `Multiplier` Service is available on the `this_multiplier_accepts_null` branch of the [Provided Multiplier Service code](https://github.com/allex-twoservicecloud-tutorial/multiplier.git)

### Reject if `this.multiplier` is not a number

Logically, it would be more correct to reject if `this.multiplier` is not a number:

```javascript
  MultiplierService.prototype.multiply = function (number) {
    if (!lib.isNumber(this.multiplier)) {
      return q.reject(new lib.Error('NO_MULTIPLIER', 'MultiplierService currently has no multiplier'));
    }
    return q(number*this.multiplier);
  }
```

where `q` stands for `execlib.lib.q`;

This implementation of the `Multiplier` Service is available on the `this_multiplier_rejects_on_null` branch of the [Provided Multiplier Service code](https://github.com/allex-twoservicecloud-tutorial/multiplier.git)

## Saving the `multiplier` value to a `state` property and multiplying only when it exists

This approach guarantees that the very multiplication will occur only if the `MultiplierService` instance is in hold of the `multiplier` value.

No changes would be made to the original constructor and destructor:

```javascript
  function MultiplierService(prophash) {
    ParentService.call(this, prophash);
    RemoteServiceListenerServiceMixin.call(this);
  }
```

The destructor would also account for the same field:

```javascript
  MultiplierService.prototype.__cleanUp = function() {
    RemoteServiceListenerServiceMixin.prototype.destroy.call(this);
    ParentService.prototype.__cleanUp.call(this);
  };
```

The handlers would be:

```javascript
  MultiplierService.prototype.onMultiplierSet = function (keyvalarray) {
    var key = keyvalarray[0], val = keyvalarray[1];
    this.state.set(key, val);
    //just for fun, consider
    //this.state.set.apply(this.state, keyvalarray);
  };
```

```javascript
  MultiplierService.prototype.onMultiplierRemoved = function (key) {
    this.state.remove(key);
  };
```

But, the `multiply` method of `MultiplierService` would make use of the `dependentServiceMethod` mechanism:

```javascript
  MultiplierService.prototype.multiply = execSuite.dependentServiceMethod([], ['multiplier'], function (multiplier, number, defer) {
    defer.resolve(multiplier*number);
  });
```

`dependentServiceMethod` accepts 3 parameters:

| parameter | explanation |
| ---       | ---         |
| `subservices` | Array of subservice names. Only when all the subservices exist under the given names, the function will be called with sinks to all the subservices listed in the Array. In our case we have no subservices to wait for, so this Array is empty. |
| `states`    | Array of state names. Only when all the states exist under the given names, the function will be called with values of the state properties listed in the Array. In our case we need just the `multiplier` state property, so this parameter has the `['multiplier']` value. |
| `function`  | Function that will be the _workhorse_ of the method produced by `dependentServiceMethod`. This function will receive all the sinks listed in the `subservices` input parameter, then all the values listed in the `states` parameter, then all the input parameters of the final method produced, then the `defer`. In our case the method should receive a `number` to multiply with the `multiplier` value, so the input parameters of our `function` are `(multiplier, number, defer)`. |

`dependentServiceMethod` returns a function that will be the method.
This method will always return a promise.
The `function` provided to `dependentServiceMethod` should never `return`.

It should `resolve` the provided `defer` instead of `return`ing.

It should `reject` the provided `defer` instead of `throw`ing.

Moreover, the function is free to `notify` the provided `defer` in order to communicate intermediate results before it finishes its operation.

So, with this approach, the `function` will not be called until `multiplier` `state` property is set.
When it is set, the `function` will be called with the value of `multiplier` set on the `state`.

This approach is elegant:

> If the `Config` service is not available, or the `multiplier` config parameter is not set on it, the `Multiplier` service will be deferring all the calls to the `multiply` method. Once the value of the `multiplier` config parameter is finally obtained, all the waiting `multiply` calls will be resolved - with a result of multiplying the `mutliplier` value with the corresponding value of the provided `number`.

However, this elegant approach comes with a price:
if the `multiplier` config parameter is not obtained for a long time, the queue of waiting `multiply` calls may grow too much, and the node.js process may crash due to `ENOMEM: Cannot allocate memory` Error.

This implementation of the `Multiplier` Service is available on the `dependent_service_method` branch of the [Provided Multiplier Service code](https://github.com/allex-twoservicecloud-tutorial/multiplier.git)

