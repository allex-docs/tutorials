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

# What's next?

1. [.allexns.json](allexnsjson.md) will explain the lookup table for Module name recognition
