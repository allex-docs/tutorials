# Typical Service module directory structure

This is the structure of a typical Service module directory:

```
+-- browserify.js
+-- clientside.js
+-- index.js
+-- methoddescriptors
│   +-- serviceuser.js
│   \-- user.js
+-- package.json
+-- README.md
+-- servicecreator.js
+-- sinkmapcreator.js
+-- sinks
│   +-- servicesinkcreator.js
│   \-- usersinkcreator.js
+-- users
│   +-- serviceusercreator.js
│   \-- usercreator.js
\-- web_component
    \-- protoboard.json
```

It is produced by the `allex-service-createrepo` scaffolding command of the `allexsdk`.

Here are the descriptions of all the files/directories in the directory root:

| Name | Type | Description |
| --- | --- | --- |
| browserify.js | File | Used to produce the `web_component` directory - if needed. You will need to edit this file only in very rare/extreme development use cases. |
| clientside.js | File | Used to produce the "client side" of the Service. |
| index.js    | File | The "root" file of the whole Service repo. Used to define the _dependencies_ of your Service repo. |
| methoddescriptors | Directory | Contains the method descriptor files, one descriptor file per _user role_. In standard (vast majority) cases, there will be `serviceuser.js` and `user.js` within it. |
| package.json | File | Well known npm stuff. |
| README.md | File | Explains what your Service does (if you're tidy and careful). |
| servicecreator.js | File | The implementation of the "server side" of your Service. In more complex cases, the root file of your Service's implementation. |
| sinkmapcreator.js | File | Used to produce the _user role_ => _sink implementation_ `Map`. `clientside.js` uses it. |
| sinks | Directory | Contains the Sink implementation files, one implementation file per _user role_. In standard (vast majority) cases, there will be `servicesinkcreator.js` and `usersinkcreator.js` within it. |
| users | Directory | Contains the User implementation files, one implementation file per _user role_. In standard (vast majority) cases, there will be `serviceusercreator.js` and `usercreator.js` within it. |
| web_component | Directory | Initially holds just the `protoboard.json` file that contains the "vanilla" recipe for producing the "web browser side" of the Service. After running `allex-component-build`, this directory will contain files ready to be included in your _html_ files. You will need to edit the `protoboard.json` file only in very rare/extreme development use cases. |
