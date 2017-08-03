# Module naming 

In AllexJS, the name of the module consists of 3 or 4 parts.

These are the possible parts:

### username
This is the api.allex.io username of the module author.

### namespace (optional)
This is the optional name of the repository group where the module resides. Default namespace is no namespace.

### repository
This is the name of the repository within its namespace. Default namespace is no namespace.

### group - singular
Standard AllexJS module groups are

- services
- libs
- parsers
- storages

Every group name has to end in letter `s`, because it denotes a plural (a group is a set of modules that behave the same way). Different groups are loaded by the AllexJS runtime differently.

Module name ends with the group name without the ending `s`, making it a singular form of the group name.

Examples:

- Name of a module that is in the `services` group ends in `service`
- Name of a module that is in the `libs` group ends in `lib`
- Name of a module that is in the `parsers` group ends in `parser`
- Name of a module that is in the `storages` group ends in `storage`

## 3-part module names

These modules have no namespace part.
Basic form is:

`<username>_<repository><groupsingular>`

Examples:

| Modulename           | Username        | Repository      | Group    |
| -------------------  | -----           | ------          | -------- |
| allex_masterservice  | allex           | master          | services |
| allex_directorylib   | allex           | directory       | libs     |
| allex_jsonparser     | allex           | json            | parsers  |
| allex_mongostorage   | allex           | mongo           | storages |

## 4-part module names

These modules have a namespace part as well.
Basic form is:

`<username>__<namespace>_<repository><groupsingular>`

Examples:

| Modulename                    | Username | Namespace | Repository | Group    |
| -------------------           | -----    | --------- | ------     | -------- |
| allex__test1_resolverservice  | allex    | test1     | resolver   | services |
| allex__gaming_mathlib         | allex    | gaming    | math       | libs     |
| allex__payment_requestparser  | allex    | payment   | request    | parsers  |
| allex__banking_ldapstorage    | allex    | banking   | ldap       | storages |

So, in the 4-part module names, the namespace comes in surrounded with underscores `_` .


# The "colon notation"

Any AllexJS compatible module name (in its 3-part or 4-part form) may be represented in the "colon notation".

Examples:

| Modulename                    | Colon notation               |
| ---------                     | --------------               |
| allex_masterservice           | allex:master                 |
| allex_directorylib            | allex:directory:lib          |
| allex_jsonparser              | allex:json:parser            |
| allex_mongostorage            | allex:mongo:storage          |
| allex__test1_resolverservice  | allex:test1_resolver         |
| allex__gaming_mathlib         | allex:gaming_math:lib        |
| allex__payment_requestparser  | allex:payment_request:parser |
| allex__banking_ldapstorage    | allex:banking_ldap:storage   |

So, the colon notation for 3-part module names is `<username>:<repository>:<groupsingular>`, and for 4-part module names it is `<username>:<namespace>_<repository>:<groupsingular>`

The exception to the rule is the `service` groupsingular case, that can (but _does not need to_) be omitted. (`allex:master` equals `allex:master:service`).

# .allexns.json

In order to obtain the needed functionality, AllexJS runtime loads the modules and instantiates them according to their type (`service`, `lib`, `parser` or `storage`).

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

