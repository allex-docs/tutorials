# .allexns.json

In order to obtain the needed functionality, AllexJS runtime loads the modules and instantiates them according to their group (`service`, `lib`, `parser` or `storage`).

Therefore, AllexJS runtime needs to know for each module name how to fetch the appropriate module and how to treat it.

This is where `.allexns.json` file comes in.

Here is a basic `.allexns.json` file:

```json
[
{"username": "allex", "namespace": null, "group": "services", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-services"},
{"username": "allex", "namespace": null, "group": "libs", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-libs"},
{"username": "allex", "namespace": null, "group": "storages", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-storages"},
{"username": "allex", "namespace": null, "group": "parsers", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-parsers"}
]
```

It is a JSON file that contains an array of objects.

Each object has to have the following properties:

| Property     | Meaning          |
| ----------   | -----------      |
| username     | AllexJS username |
| namespace    | namespace part of the module name. In the 3-part module name case, the value is `null`, just like in the example given above. |
| group        | name of the group that defines the groupsingular of the module name |
| type         | may be `npm` or `git`. `npm` is for modules that should be fetched via `npm install modulename`. `git` is for modules that should be fetched via `npm install git+ssh://giturl.git` |
| user         | if the type is `git`, this is the username that will access the git server |
| server       | if the type is `git`, this is the git server url |
| repogroup    | if the type is `git`, this is the repository group on the git server. On github.com, the term is _organization_. On gitlab instances, the term is _Group_. On plain git servers, the term is _directory_. |


Obviously, if the `type` is `npm`, the properties `user`, `server`, and `repogroup` are not needed and may be `null`. However, in the example file above their values are left as for the `git` type case.

# Location of .allexns.json

`.allexns.json` should be located in the directory where it is needed (the working directory) or in any parent directory up the filesystem.
This means that `.allexns.json` may be located in

- the working directory
- parent directory of the working directory
- parent directory of the parent directory of the working directory
- ...
- the filesystem root

The first `.allexns.json` file found in the filesystem path explained above will be used.

## Public modules and the `npm` type

If the type is `npm`, properties of the `.allexns.json` entry are combined to form the module name.
The modulename is first checked for AllexJS compatibility: the presence of underscores `_` is checked and the modulename is broken into appropriate 3 or 4 parts:

1. `username`
2. `groupsingular`
3. `modulename` and
4. (optionally) `namespace`.

Once the parts are known, an entry in the `.allexns.json` file is being searched for that will have the appropriate `username`, `group` (`groupsingular`+"s"), and (optionally, in the 4-part case) `namespace`.

Then the `type` is read from the .allexns.json entry, it is found to be `npm`, and a npm modulename is produced in the form of

`<username>_<modulename><groupsingular>` in the case of no namespace,

or `<username>__<namespace>_<modulename><groupsingular>` in the case of an existing namespace.

Obviously, the appropriate repository has to be `npm` `publish`-ed under the appropriate module name, as constructed above.

### No namespace example

If we take a look at the basic `.allexns.json`, and the entry within

`{"username": "allex", "namespace": null, "group": "services", "type": "npm", "user": "git", "server": "github.com", "repogroup": "allex-services"}`

then the `master` repository in `https://github.com/allex-services` github.com Organization maps to

`allex_masterservice`

because it has no namespace, and it will be installed with

`npm install allex_masterservice`

`user`, `server`, and `repogroup` properties of the `.allexns.json` are not taken into account in this case.

### Existing namespace example

If there were an entry in `.allexns.json` like

`{"username": "majordeveloper", "namespace": "projectabc", "group": "services", "type": "npm", "user": null, "server": null, "repogroup": null}`

then the `activator` repository in this repo group maps to

`majordeveloper__projectabc_activatorservice`

because it has the namespace `projectabc`, and it will be installed with

`npm install majordeveloper__projectabc_activatorservice`

## Private modules and the `git` type

The whole AllexJS framework was built on an idea that the developers might not want to expose their code to the public.

In order to develop private modules, the developer should have access to a git server where private repositories may be created. Possible cases may be

- Private repositories on [github.com](https://github.com)
- A private `gitlab` instance
- A private Unix-like server with ssh access, with `git` installed

Then, on the appropriate git server, the developer creates a repogroup (aka Organization/Group/directory).
That group will serve a set of modules of the same purpose.
This group will then be represented by a corresponding entry in the `.allexns.json`

Example:

- Git server address: git.supercompany.com
- Name of the repogroup: bestprojectservices
- Git server is a `gitlab` instance- which means that the type will be `git`
- User that accesses the git server is `git` (that's how `gitlab` works)
- AllexJS username: "superdev"
- Namespace: "bestproject"
- Group: "services"

Finally, the `.allexns.json` entry for the above parameters is

`{"username": "superdev", "namespace": "bestproject", "group": "services", "type": "git", "user": "git", "server": "git.supercompany.com", "repogroup": "bestprojectservices"}`

### How does privacy work for private modules?

Security is based on `ssh` keys.
When repogroups (and repositories within these repogroups) are being created, they should be created as "Private".
In the case of a "plain" git server (Unix-like server with `git` installed), plain-password-based ssh access should be disabled.
In any of the above cases, the repositories will be accessible only if the appropriate ssh key is [deployed](ssh_key_deployment.md) on the machine.

### What does module recognition do with the git type?

The modulename is first checked for AllexJS compatibility: the presence of underscores `_` is checked and the modulename is broken into appropriate 3 or 4 parts:

1. `username`
2. `groupsingular`
3. `modulename` and
4. (optionally) `namespace`.

Once the parts are known, an entry in the `.allexns.json` file is being searched for that will have the appropriate `username`, `group` (`groupsingular`+"s"), and (optionally, in the 4-part case) `namespace`.

Then the `user`, `server` and `repogroup` are read from the .allexns.json entry and a git url is produced in the form of git+ssh://user@server/repogroup/modulename.git

Then the module is installed as
`npm install git+ssh://user@server/repogroup/modulename.git`

As a continuation of the above example, if there is a `monitor` repository in the `bestprojectservices` repo group, it will be represented by the following module name:

`superdev__bestproject_monitorservice`

and will be installed with

 `npm install git+ssh://git@git.supercompany.com/bestprojectservices/monitor.git`


