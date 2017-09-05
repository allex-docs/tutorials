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

In the `allexlanmanager` termnal run `allexlanmanager`.
This will result in the default contents of the `.allexlanmanager` directory.

## Edit the needs

Edit the `.allexlanmanager/needs` file.

It originally looks like this:

```json
```

## Let .allexns.json know about your new repo group

From the standpoint of AllexJS, `allex-plainservice-tutorial` will be the repo group for Services.
Therefore, 

1. 
