# The Testing Sequence for the Multiplying Cloud

We will here present a method that will actually _observe_ the behavior of the multiplying cloud.
The real meaning of the word `testing` would be

> Bring the cloud to a predefined state and expect a predefined result from it

In our case we shall be using the [`setMultiplier.js`](set_multiplier.md#setmutliplier_js), [`removeMultiplier`](set_multiplier.md#removemutliplier_js), and [`doMultiply.js`](consume_multiplier.md) scripts in the `userspace` subdirectory of the project directory.

Here is how we shall observe the Cloud:

1. Run the `removeMultiplier.js` task so that we're sure that the `Config` service has no `multiplier` config parameter.
2. Start the `doMultiply.js` task. It will be sending random numbers in the `[1, 10]` range each second to the `Multiplier` service.
3. We shall observe the result obtained from the `Multiplier` service when there is no `multiplier` config parameter available from the `Config` service.
4. We shall run the `setMultiplier.js` in order to set the `multiplier` config parameter to 5 on the `Config` service.
5. We shall observe the result obtained from the `Multiplier` service when there is a `multiplier` config parameter available from the `Config` service.
6. We shall run the `removeMultiplier.js` again in order to remove the `multiplier` config parameter from the `Config` service.
7. We shall observe the result obtained from the `Multiplier` service when there is no `multiplier` config parameter available from the `Config` service.


We will run this sequence for all 3 flavors of the `Multiplier` service:

1. When `null` is accepted as a valid value for the remote `multiplier` config parameter.
2. When `null` is not accepted as a valid value for the remote `multiplier` config parameter.
3. When `Multiplier` service waits for a valid value for the remote `multiplier` config parameter in order to resolve the `multiply` call.


## `null` is a valid value for the `multiplier`

1. Stop the `allexmaster` in the project directory.
2. `git checkout git checkout this_multiplier_accepts_null` in the development directory for your implementation of the Multiplier Service.
3. Run `allexmaster` in the project directory.
4. Run `allexruntask removeMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask removeMultiplier
parseProgram 11370
del succeeded multiplier
  ```
5. Run `allexruntask doMultiply` in the `userspace` subdirectory of the project directory (in a separate terminal, because this script will not return until `allexmaster`/Multiplier service is stopped).
  ```
$ allexruntask doMultiply
parseProgram 11370
multiply succeeded for number 1 yielding 0
will retry the multiplication in 1 seconds
multiply succeeded for number 7 yielding 0
will retry the multiplication in 1 seconds
multiply succeeded for number 2 yielding 0
will retry the multiplication in 1 seconds
multiply succeeded for number 10 yielding 0
will retry the multiplication in 1 seconds
multiply succeeded for number 5 yielding 0
will retry the multiplication in 1 seconds
  ```
  You can see that the service returns 0 as a result of the multiplication when `mutliplier` is not available.
6. Run `allexruntask setMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask setMultiplier
parseProgram 11370
put succeeded [ 'multiplier', 5 ]
  ```
7. Observe the output of the `doMultiply`
  ```
multiply succeeded for number 6 yielding 30
will retry the multiplication in 1 seconds
multiply succeeded for number 6 yielding 30
will retry the multiplication in 1 seconds
multiply succeeded for number 10 yielding 50
will retry the multiplication in 1 seconds
multiply succeeded for number 6 yielding 30
will retry the multiplication in 1 seconds
multiply succeeded for number 1 yielding 5
will retry the multiplication in 1 seconds
multiply succeeded for number 3 yielding 15
will retry the multiplication in 1 seconds
multiply succeeded for number 9 yielding 45
will retry the multiplication in 1 seconds
  ```
  Now the provided random numbers are multiplied by 5.
8. Run `allexruntask removeMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask removeMultiplier
parseProgram 11370
del succeeded multiplier
  ```
9. Observe the output of the `doMultiply`
  ```
multiply succeeded for number 1 yielding 0
will retry the multiplication in 1 seconds
multiply succeeded for number 7 yielding 0
will retry the multiplication in 1 seconds
  ```
  so the Multiplier has no `multiplier` config parameter, and gets back to returning 0 for any number provided to `multiply`.

## `null` value for the `multiplier` results in `reject`

1. Stop the `allexmaster` in the project directory.
2. `git checkout git checkout this_multiplier_rejects_on_null` in the development directory for your implementation of the Multiplier Service.
3. Run `allexmaster` in the project directory.
4. Run `allexruntask removeMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask removeMultiplier
parseProgram 13915
del succeeded multiplier
  ```
5. Run `allexruntask doMultiply` in the `userspace` subdirectory of the project directory (in a separate terminal, because this script will not return until `allexmaster`/Multiplier service is stopped).
  ```
$ allexruntask doMultiply
parseProgram 13915
multiply failed for number 9 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
multiply failed for number 2 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
multiply failed for number 10 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
multiply failed for number 9 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
multiply failed for number 5 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
  ```
  You can see that the service rejects as a result of the multiplication when `mutliplier` is not available.
6. Run `allexruntask setMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask setMultiplier
parseProgram 13915
put succeeded [ 'multiplier', 5 ]
  ```
7. Observe the output of the `doMultiply`
  ```
multiply succeeded for number 6 yielding 30
will retry the multiplication in 1 seconds
multiply succeeded for number 4 yielding 20
will retry the multiplication in 1 seconds
multiply succeeded for number 7 yielding 35
will retry the multiplication in 1 seconds
multiply succeeded for number 5 yielding 25
will retry the multiplication in 1 seconds
multiply succeeded for number 1 yielding 5
will retry the multiplication in 1 seconds
  ```
  Now the provided random numbers are multiplied by 5.
8. Run `allexruntask removeMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask removeMultiplier
parseProgram 13915
del succeeded multiplier
  ```
9. Observe the output of the `doMultiply`
  ```
multiply failed for number 4 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
multiply failed for number 9 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
multiply failed for number 5 because { code: 'NO_MULTIPLIER',
  message: 'MultiplierService currently has no multiplier' }
will retry the multiplication in 1 seconds
  ```
  so the Multiplier has no `multiplier` config parameter, and gets back to rejecting for any number provided to `multiply`.

## `dependentServiceMethod` waits for the `multiplier` to be acquired

1. Stop the `allexmaster` in the project directory.
2. `git checkout dependent_service_method` in the development directory for your implementation of the Multiplier Service.
3. Run `allexmaster` in the project directory.
4. Run `allexruntask removeMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask removeMultiplier
parseProgram 14719
del succeeded multiplier
  ```
5. Run `allexruntask doMultiply` in the `userspace` subdirectory of the project directory (in a separate terminal, because this script will not return until `allexmaster`/Multiplier service is stopped).
  ```
$ allexruntask doMultiply
parseProgram 14719

  ```
  You can see that the service waits for the `mutliplier` to become available.
6. Run `allexruntask setMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask setMultiplier
parseProgram 14719
put succeeded [ 'multiplier', 5 ]
  ```
7. Observe the output of the `doMultiply`
  ```
multiply succeeded for number 5 yielding 25
will retry the multiplication in 1 seconds
multiply succeeded for number 3 yielding 15
will retry the multiplication in 1 seconds
multiply succeeded for number 10 yielding 50
will retry the multiplication in 1 seconds
multiply succeeded for number 1 yielding 5
will retry the multiplication in 1 seconds
multiply succeeded for number 3 yielding 15
will retry the multiplication in 1 seconds
multiply succeeded for number 9 yielding 45
will retry the multiplication in 1 seconds
  ```
  Now the provided random numbers are multiplied by 5.
8. Run `allexruntask removeMultiplier` in the `userspace` subdirectory of the project directory.
  ```
$ allexruntask removeMultiplier
parseProgram 13915
del succeeded multiplier
  ```
9. Observe the output of the `doMultiply`
  ```
  ```
  so the Multiplier has no `multiplier` config parameter, and gets back to waiting for the `multiplier` config param value to be acquired again.

