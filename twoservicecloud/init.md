# Initialize the project

Starting a project has been covered in detail in [Developing a Plain Service](../basic_development/plainservice.md).
We'll just list the steps needed here:

#### 1. Make a repo group for the Project.

In our case the repo group is a github Organization available at [https://github.com/allex-twoservicecloud-tutorial](https://github.com/allex-twoservicecloud-tutorial). It will contain just 2 repositories:
- the Project repository
- the Multiplier repository

#### 2. Create the `twoservicemultiplierproject` repository in your repo group.

In our case it is available at [https://github.com/allex-twoservicecloud-tutorial/twoservicemultiplierproject](https://github.com/allex-twoservicecloud-tutorial/twoservicemultiplierproject).

#### 3. Clone the `twoservicemultiplierproject` to your development directory.

In our case we do it with
```
git clone git@github.com:allex-twoservicecloud-tutorial/twoservicemultiplierproject.git
```
In any case, you should end up with the `twoservicemultiplierproject` directory in your development directory.

#### 4. Initialize the contents of the project directory

Enter the `twoservicemultiplierproject` directory and issue `allexlanmanager` and `allexmaster` simultaneously (in two separate terminals).
After they have successfully started and connected, stop them both.
Now you have all the initial files of the project.

#### 5. Edit the `.allexlanmanager/needs` file.

In our case it looks like this:
```json
[
  {
    "modulename": "allex_leveldbconfigservice",
    "instancename": "Config",
    "propertyhash": {
      "path": ["dbs", "config.db"],
      "fields": ["multiplier"]
    }
  },
  {
    "modulename": "allex__twoservicecloud_multiplierservice",
    "instancename": "Multiplier",
    "propertyhash": {
      "path": "config.db",
      "fields": ["multiplier"]
    }
  }
]
```
#### 6. Edit the `.allexns.json` file.

In our case it looks like this:
```json
[
{"username": "allex", "namespace": null, "group": "services", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-services"},
{"username": "allex", "namespace": null, "group": "libs", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-libs"},
{"username": "allex", "namespace": null, "group": "storages", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-storages"},
{"username": "allex", "namespace": null, "group": "parsers", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-parsers"},
{"username": "allex", "namespace": "twoservicecloud", "group": "services", "type": "git", "user": "git", "server": "github.com", "repogroup": "allex-twoservicecloud-tutorial"}
]
```

#### 7. Create the `multiplier` repository in your repo group.

#### 8. Create the `~/lib/allexjs_modules_dev` directory, if you do not already have one.

#### 9. Clone the `mutlitplier` repository

Enter the `~/lib/allexjs_modules_dev` directory, and clone the `multiplier` repository.
Have in mind that this repository will contain the AllexJS Service module, so take care about AllexJS [Module Name recognition](../development_basics/module_recognition.md).
In our case we
```
git clone git@github.com:allex-twoservicecloud-tutorial/multiplier.git allex__twoservicecloud_multiplierservice
```
You should end up with a Service module directory named
```
[your username]__twoservicecloud_multiplierservice
```

#### 10. Scaffold the Service

Enter the `[your username]__twoservicecloud_multiplierservice` and scaffold a plain service:
```
allex-service-createrepo -a [your author name] -u [your username] -r service -r user -n TwoServiceCloud -p Multiplier
```
In our case it is
```
allex-service-createrepo -a allex -r service -r user -n TwoServiceCloud -p Multiplier
```

#### 11. Restart the Cloud 

In the `twoservicemultiplierproject` project directory, run `allexlanmanager` and `allexmaster` simultaneously (in two separate terminals).
Now you will have the initial 2-service cloud up and running, but doing nothing.

## Wrapping it all up

1. You have created a repo group that will hold your project repository and your Multiplier service repository.
2. You have created initial repositories for the project and for the Multiplier service and cloned them - the project repository to your run-time directory, the service repository to your module development directory.
3. You initialized the project directory by initially running `allexlanmanager` and `allexmaster`. You edited the `.allexns.json` file accordingly as well.
4. You edited the initial `.allexlanmanager/needs` file so that it specifies 2 services to be ran in the cloud.
5. You initialized the Multiplier service directory by running `allex-service-createrepo` with appropriate parameters.

## What's next?

The time is right to [Develop the Service](develop_service.md)
