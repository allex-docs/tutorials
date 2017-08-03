# Setup your environment

1. Install nodejs version 6.x, x >= 10. Major release number has to be 6, minor release number 10 or higher.
2. Install `allex` globally: `npm install -g allex`
3. Install `allexsdk` globally: `npm install -g allexsdk`
4. Install `allextesting` globally: `npm install -g allextesting`
5. Initiate the base .allexns.json namespace file: `wget -O ~/.allexns.json https://gist.githubusercontent.com/andrija-hers/f80b7dd09558efbc1a59b5f445e47fbf/raw`

## Notes
1. nodejs __has__ to support the nodejs LTS. At the time of writing this tutorial, it is 6.11.1
2. Installing `allex` globally will bring you the essential commands:
3. Install `allexsdk` globally will bring you a family of software development commands that we shall deal with in later tutorials.
4. Install `allextesting` globally will bring you extensions to the `mocha` JavaScript testing framework, which will be covered in detail in later tutorials.
5. Tutorial [Module naming](../development_basics/module_recognition.md) will show you what `.allexns.json` file is for, and much more on the module naming convention.