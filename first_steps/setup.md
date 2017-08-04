# Setup your environment

1. Install nodejs version 6.x, x >= 10. Major release number has to be 6, minor release number 10 or higher.
2. Set the `npm prefix` to your users's root directory: `npm config set prefix ~`
3. Update your PATH directory so that it also contains the `$HOME/bin` path
4. Install `allex` globally: `npm install -g allex`
5. Install `allexsdk` globally: `npm install -g allexsdk`
6. Install `allextesting` globally: `npm install -g allextesting`
7. Initiate the base .allexns.json namespace file: `wget -O ~/.allexns.json https://gist.githubusercontent.com/andrija-hers/f80b7dd09558efbc1a59b5f445e47fbf/raw`

## Notes
1. AllexJS __has__ to support the nodejs LTS. At the time of writing this tutorial, it is 6.11.1
2. Because you are in the "development mode" (as opposed to "deployment mode", that will be discussed in a separate tutorial), you should be capable of running `npm install -g` without `sudo` privileges. Setting the default `npm` directory (the term is `prefix`) to your user's root directory (aliases are `~` or `$HOME`) means that all the globally installed modules will appear in the `~/lib/node_modules` directory, and all the binary executables will appear in the `~/bin` directory.
3. Because all the binary executables will appear in the `~/bin` directory, you need to be capable of running them without calling them explicitly (so that you can, for example, run `allexlanmanager` in your terminal, without having to explicitly call `~/bin/allexlanmanager`). This is most easily done by editing your `.bashrc file`, found in the `~` directory.
4. Installing `allex` globally will bring you the essential commands: `allexlanmanager`, `allexmaster`, `allexrun`, `allexruntask`, `allexenterandruntask`, and several less significant commands. We shall deal with each of them in later tutorials. As already noted, the `allex` module directory will be `~/lib/node_modules/allex`, and all the binaries that `allex` exports will be in `~/bin`.
5. Install `allexsdk` globally will bring you a family of software development commands that we shall deal with in later tutorials. As already noted, the `allexsdk` module directory will be `~/lib/node_modules/allexsdk`, and all the binaries that `allexsdk` exports will be in `~/bin`.
6. Install `allextesting` globally will bring you extensions to the `mocha` JavaScript testing framework, which will be covered in detail in later tutorials. As already noted, the `allextesting` module directory will be `~/lib/node_modules/allextesting`, and all the binaries that `allextesting` exports will be in `~/bin`.
7. Tutorial [Module naming](../development_basics/module_recognition.md) will show you what `.allexns.json` file is for, and much more on the module naming convention.
