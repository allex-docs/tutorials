# Developing a plain Service

By the term "plain Service" a Service will be assumed that has all of its functionality coded in the Service module.

This term is opposed to "lib Service" that uses the functionality coded in a library (lib).

## Prepare a git repo group

For the purpose of this tutorial, we created an Organization on github.com.
The name of the organization is allex-plainservice-tutorial.
It is accessible via https://github.com/allex-plainservice-tutorial.

It will contain:

1. The Service repository. 
2. The Project repository.

This organization of the code/repositories is rather dirty, but we are affording ourselves such dirtiness because there will be no more repositories in this Organization.
More complex tutorials will ask for a cleaner layout of the Organizations and repositories within.

## Prepare the Project repository

The Project repository will contain the _runtime_ directory.
This is the directory that will have the `.allexlanmanager` subdirectory, `.allexmasterconfig.json` file and the `userspace` subdirectory.
For the purpose of this tutorial, we created a repository named `project` in the `allex-plainservice-tutorial` repo group, and initiated it with a MIT License.

## Make sure you have the github key installed on your machine

At this point, you should have had an ssh key created and deployed on github.com.
You can easily verify this with `ssh -T git@github.com`.

## Clone the Project repository to your development machine

`git clone git@github.com:allex-plainservice-tutorial/project.git`

This command will result in creating the directory named `project` in the directory where you ran the above command.

## Initiate the contents of the Project directory

Enter the `project` directory and prepare 3 terminals:

1. For the `allexlanmanager`
2. For the `allexmaster`
3. For the `userspace`, where you will develop the `allexruntask` scripts.

## Establish the naming convention for your new "service" group

Services (in the case of this tutorial, just one Service) specific for this tutorial Project will be in the `allex-plainservice-tutorial` repo group.

We will be using the `plainservicetutorial` AllexJS namespace for the repo group that will hold the Services.

Since we will be building the `multiplier` Service, the AllexJS name of the Service will be `allex__plainservicetutorial_multiplierservice`.


## Let .allexns.json know about your new repo group

### Initiate the .allexns.json file

Download the contents of the [basic .allexns.json](https://gist.github.com/andrija-hers/f80b7dd09558efbc1a59b5f445e47fbf)  and save it in your Project directory as `.allexns.json`

Now it has the following content:

```json
[
{"username": "allex", "namespace": null, "group": "services", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-services"},
{"username": "allex", "namespace": null, "group": "libs", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-libs"},
{"username": "allex", "namespace": null, "group": "storages", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-storages"},
{"username": "allex", "namespace": null, "group": "parsers", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-parsers"}
]
```

### Add the entry for your Service group

Because in our case we will have a Service repository in the `allex-plainservice-tutorial` Organization on the server `github.com` that we will be accessing as the `git` user,
we will make an entry as follows:

```json
{"username": "allex", "namespace": "plainservicetutorial", "group": "services", "type": "git", "user": "git", "server": "github.com", "repogroup": "allex-plainservice-tutorial"}
```

so that finally your `.allexns.json` file looks like this:

```json
[
{"username": "allex", "namespace": null, "group": "services", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-services"},
{"username": "allex", "namespace": null, "group": "libs", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-libs"},
{"username": "allex", "namespace": null, "group": "storages", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-storages"},
{"username": "allex", "namespace": null, "group": "parsers", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-parsers"},
{"username": "allex", "namespace": "plainservicetutorial", "group": "services", "type": "git", "user": "git", "server": "github.com", "repogroup": "allex-plainservice-tutorial"}
]
```

### Update the global .allexns.json as well

Make sure that you add the same entry 

```json
{"username": "allex", "namespace": "plainservicetutorial", "group": "services", "type": "git", "user": "git", "server": "github.com", "repogroup": "allex-plainservice-tutorial"}
```

to your global `.allexns.json` file (`~/.allexns.json` on Unix-like platforms), because it will be needed there for the Service development process.

## Prepare the needs

### Initiate the .allexlanmanager subdirectory

In the `allexlanmanager` termnal run `allexlanmanager`.
This will result in the default contents of the `.allexlanmanager` directory.

### Edit the needs

Edit the `.allexlanmanager/needs` file.

It was initiated by `allexlanmanager` to this content:

```json
[
  {
    "modulename": "allex_timeservice",
    "instancename": "Time",
    "propertyhash": {}
  }
]
```

But in this tutorial we are not interested in running an instance of `allex_timeservice`. We need an instance of `allex__plainservicetutorial_multiplierservice`.
Therefore, we will edit `.allexlanmanager/needs` so that it looks like this:


```json
[
  {
    "modulename": "allex__plainservicetutorial_multiplierservice",
    "instancename": "Multiplier",
    "propertyhash": {}
  }
]
```

At this moment of the Project development, if we run `allexmaster` we will get a pretty ugly output:

```
Sun Aug 27 2017 13:31:17 GMT+0200 (CEST) DEBUG allexmaster running acquireSink in order to find LAN manager ws://127.0.0.1:23451
Sun Aug 27 2017 13:31:22 GMT+0200 (CEST) ERROR allexmaster 4193 npm could not install the missing module allex__plainservicetutorial_multiplierservice because Error: Cannot find module 'allex__plainservicetutorial_multiplierservice'
```

Because we still haven't created the `multiplier` repository in the `allex-plainservice-tutorial` repo group.

## Create the Service repository

We create the `multiplier` repository in the `allex-plainservice-tutorial` repo group (github.com Organization).

It is now available at [https://github.com/allex-plainservice-tutorial/multiplier](https://github.com/allex-plainservice-tutorial/multiplier).

### allexmaster will fail again

If you run `allexmaster` now, you will get an error different to the previous one:

```
Sun Aug 27 2017 13:44:20 GMT+0200 (CEST) DEBUG allexmaster running acquireSink in order to find LAN manager ws://127.0.0.1:23451
Sun Aug 27 2017 13:44:26 GMT+0200 (CEST) ERROR allexmaster 5696 npm could not install the missing module allex__plainservicetutorial_multiplierservice because 235
```

235?! What? There needs to be an explanation to this.

So, what happened here?

1. allexmaster asked the internal `allex-npm-install` to install the `allex__plainservicetutorial_multiplierservice` module via npm's `git+ssh://` mechanism.
2. The working directory of `allex-npm-install` is `~/lib` for the Unix-like platforms (`` for the Windows platforms)
3. The log of `allex-npm-install` can be found in the working directory of `allex-npm-install`, in the file `.npminstaller.log`
4. Let's take a look at the last 6 lines of `.npminstaller.log`

```json
{"name":"npm-install","hostname":"AndroBook.local","pid":5701,"level":30,"msg":"invoke allex__plainservicetutorial_multiplierservice git+ssh://git@github.com/allex-plainservice-tutorial/multiplier.git from 5696","time":"2017-08-27T11:44:22.894Z","v":0}
{"name":"npm-install","hostname":"AndroBook.local","pid":5701,"level":30,"msg":"new SuperInstallRequest allex__plainservicetutorial_multiplierservice git+ssh://git@github.com/allex-plainservice-tutorial/multiplier.git","time":"2017-08-27T11:44:22.896Z","v":0}
{"name":"npm-install","hostname":"AndroBook.local","pid":5701,"level":30,"msg":"allex__plainservicetutorial_multiplierservice onInitialCheck 1","time":"2017-08-27T11:44:23.184Z","v":0}
{"name":"npm-install","hostname":"AndroBook.local","pid":5701,"level":30,"msg":"going to temp install git+ssh://git@github.com/allex-plainservice-tutorial/multiplier.git currently in /Users/andra/lib","time":"2017-08-27T11:44:23.184Z","v":0}
{"name":"npm-install","hostname":"AndroBook.local","pid":5701,"level":50,"msg":"install error is 235","time":"2017-08-27T11:44:26.019Z","v":0}
{"name":"npm-install","hostname":"AndroBook.local","pid":5701,"level":30,"msg":"done InstallRequest git+ssh://git@github.com/allex-plainservice-tutorial/multiplier.git allex__plainservicetutorial_multiplierservice","time":"2017-08-27T11:44:26.022Z","v":0}
```

We can see that

1. `allex-npm-install`, running under pid 5701, has received a request to install `allex__plainservicetutorial_multiplierservice`, together with the npm install url `git+ssh://git@github.com/allex-plainservice-tutorial/multiplier.git` from a process running under pid 5696 (that was our `allexmaster`, as you can see from the `allexmaster` output).
2. A new SuperInstallRequest was spawned to perform `npm install`.
3. An initial check was ran to see if the needed module is perhaps already installed. The check should have returned 0 if the module were installed. However, the check returned 1, meaning that the module is not installed yet.
4. `npm` was ran.
5. `npm` returned the result 235 (actually, -21, but the result was interpreted as an `unsigned char`).
6. InstallRequest finished its job - unsuccessfully.

In short, this means that:

1. We should `git clone` the initial repository to a development directory.
2. We should link the cloned development directory to the `node_modules` directory.
3. We should go to the cloned development directory and create the service.

## Create the development directory

On Unix-like platforms

```
mkdir ~/lib/allexjs_modules_dev
cd ~/lib/allexjs_modules_dev
```

## `git clone` the initial repository

```
git clone git@github.com:allex-plainservice-tutorial/multiplier allex__plainservicetutorial_multiplierservice
```

## Link the cloned development directory to the `node_modules` directory

```
cd ../node_modules
ln -s ../allexjs_modules_dev/allex__plainservicetutorial_multiplierservice
```

## Go to the cloned development directory and create the service

```
cd ../allexjs_modules_dev/allex__plainservicetutorial_multiplierservice
allex-service-createrepo -r service -r user -n PlainServiceTutorial -p Multiplier
```

And you've got your first AllexJS Service.
It does nothing, but the whole Service code is in its place.
Your working directory now has the [Standard Service Module Structure](../development_basics/servicemoduledirstructure.md).
It contains a working Service.

If you run `allexmaster` now in your allexmaster directory, you will get

```
Sun Aug 27 2017 13:49:49 GMT+0200 (CEST) DEBUG allexmaster running acquireSink in order to find LAN manager ws://127.0.0.1:23451
Sun Aug 27 2017 13:49:50 GMT+0200 (CEST) DEBUG Multiplier 9224 registering supersink Multiplier (allex__plainservicetutorial_multiplierservice:service)
```

### Possible problem

If you get 

```
allex__plainservicetutorial_multiplierservice was not recognized as an AllexJS module, cannot create a Service repository
```

that means that you did not update your __global__ `.allexns.json` file with the `allex-plainservice-tutorial` repogroup entry.

## Adding a method

We would like for the `user` role of the Service to have a `multiply` method. This method should:

1. receive a number as an input parameter
2. multiply the received number by 2
3. return back the result

### Add the corresponding method descriptor to the `methoddescriptors`

In the `methoddescriptors` subdirectory of the `allex__plainservicetutorial_multiplierservice` Service repo directory there are 2 files:

1. `serviceuser.js`
2. `user.js`

The first file is for methoddescriptors for the `service` User role.
The second file is for methoddescriptors for the `user` User role.

We will edit the second file, the `methoddescriptors/user.js` file.
It initially looks like this:

```javascript
module.exports = {
};
```

In other words, `methoddescriptors/user.js` exports an object.
Each property of this object has to be an array of method parameter descriptors.

Because

1. the method name is `multiply`
2. the method receives one parameter that is the Number to Multiply, and is a number by type

we will edit the file so that it looks like this:

```javascript
module.exports = {
  multiply: [{
    title: 'Number to Multiply',
    type: 'number'
  }]
};
```

Now both the remote side and the Service User will know the fingerprint of the method.

### Add the method implementation

In the `users` subdirectory of the `allex__plainservicetutorial_multiplierservice` Service repo directory there are 2 files:

1. `serviceusercreator.js`
2. `usercreator.js`

The first file is the implementation of the `service` User role.
The second file is the implementation of the `user` User role.

We are working on the `user` role, so we will edit the `users/usercreator.js` file.
It initially looks like this:

```javascript
function createUser(execlib, ParentUser) {
  'use strict';
  if (!ParentUser) {
    ParentUser = execlib.execSuite.ServicePack.Service.prototype.userFactory.get('user');
  }

  function User(prophash) {
    ParentUser.call(this, prophash);
  }

  ParentUser.inherit(User, require('../methoddescriptors/user'), [/*visible state fields here*/]/*or a ctor for StateStream filter*/);
  User.prototype.__cleanUp = function () {
    ParentUser.prototype.__cleanUp.call(this);
  };

  return User;
}

module.exports = createUser;
```

This boilerplate code simply introduces a new class that inherits from the ParentUser class.
We will add a method

1. named `multiply`
2. that takes the one parameter defined in the method descriptor (the Number to Multiply parameter).
3. the obligatory last parameter, named `defer` by convention.

User methods cannot `return`.
There is noone to return to.
AllexJS works by feeding the method with all the parameters described in the method descriptor + the last parameter, named `defer` by convention.

When the method has the result to report back to the caller, it will

```javascript
defer.resolve(result);
```

If the method should `throw`, it will

```javascript
defer.reject(error);
```

Having this in mind, we edit the `users/usercreator.js` file so that it looks like this:

```javascript
function createUser(execlib, ParentUser) {
  'use strict';
  if (!ParentUser) {
    ParentUser = execlib.execSuite.ServicePack.Service.prototype.userFactory.get('user');
  }

  function User(prophash) {
    ParentUser.call(this, prophash);
  }

  ParentUser.inherit(User, require('../methoddescriptors/user'), [/*visible state fields here*/]/*or a ctor for StateStream filter*/);
  User.prototype.__cleanUp = function () {
    ParentUser.prototype.__cleanUp.call(this);
  };

  User.prototype.multiply = function (numbertomultiply, defer) {
    defer.resolve(numbertomultiply*2);
  };

  return User;
}

module.exports = createUser;
```

# Wrapping it all up

1. Make sure you have the repo group for your Multiplier service, and that AllexJS knows about it in both the `allexmaster` directory for the run-time and globally for the development.
2. Create the `multiplier` repo in your repo group.
3. Clone the `multiplier` repo under the AllexJS-compliant name.
4. Link the cloned directory into the global `node_modules` directory.
5. Run the `allex-service-createrepo` scaffolding procedure.
6. Add the method descriptor to `methoddescriptors/user.js`.
7. Add the method implementation to `users/usercreator.js`.


# What's next?

[Consume your Multiplier service](plainserviceconsumption.md)
