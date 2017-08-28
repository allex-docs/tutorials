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

## Private modules and the git type

The whole AllexJS framework was built on an idea that the developers might not want to expose their code to the public.

In order to develop private modules, the developer should have access to a git server where private repositories may be created. Possible cases may be

- Private repositories on [github.com](https://github.com)
- A private `gitlab` instance
- A private U*ix-like server with ssh access with `git` installed

Then, on the appropriate git server, the developer creates a repogroup (aka Organization/Group/directory).
That group will serve a set of modules of the same purpose.
This group will then be represented by a corresponding entry in the `.allexns.json`

Example:

- Git server address: git.supercompany.org
- Name of the repogroup: bestprojectservices
- Type of the git server: gitlab - which means that the git user that accesses the git server is `git`

- AllexJS username: "superdev"
- Namespace: "bestproject"
- Group: "services"

Finally, the `.allexns.json` entry for the above parameters is

`{"username": "superdev", "namespace": "bestproject", "group": "services", "type": "git", "user": "git", "server": "git.supercompany.com", "repogroup": "bestprojectservices"}`

### How does privacy work for private modules?

Security is based on `ssh` keys.
When repogroups (and repositories within these repogroups) are being created, they should be created as "Private".
In the case of a "plain" git server (U*ix-like server with `git` installed), plain-password-based ssh access should be disabled.
In any of the above cases, the repositories will be accessible only if the appropriate ssh key is [deployed](ssh_key_deployment.md) on the machine.

### What does module recognition do with the git type?

The modulename is first checked for AllexJS compatibility: the presence of underscores `_` is checked and the modulename is broken into appropriate 3 or 4 parts `username` `groupsingular`, `modulename` and (optionally) `namespace`.

Once the parts are known, an entry in the `.allexns.json` file is being searched for that will have the appropriate username, group, and (optionally, in the 4-part case) namespace.

Then the `user`, `server` and `repogroup` are read from the .allexns.json entry and a git url is produced in the form of git+ssh://user@server:repogroup/modulename.git

Then the module is installed as
`npm install git+ssh://user@server:repogroup/modulename.git`

# Location of .allexns.json

`.allexns.json` should be located in the directory where it is needed (the working directory) or in any parent directory up the filesystem.
This means that `.allexns.json` may be located in

- the working directory
- parent directory of the working directory
- parent directory of the parent directory of the working directory
- ...
- the filesystem root

The first `.allexns.json` file found in the filesystem path explained above will be used.


