---
tags:
  - JS
---
We may not always want to write *all* of our own code. Many things have already been developed by other people and they've shared it to be used by others. It's possible to ease much of this process by using npm (which is always lowercase and doesn't stand for Node Package Manager!)

npm is a Package Manager for [[JavaScript]] - a gigantic repository of plugins, libraries, and other tools. It provides us with a command-line tool we can use to install these tools (that we call “packages”) in our applications. We will then have all our installed packages’ code locally, which we can import into our own files. We could even publish our own code to npm!

## package.json

`package.json` is the base of everything npm does. It contains all the information about a project: dependencies, version numbers, etc. Using `npm install` in a project with this file will automatically look through and download all of the necessary packages to keep the project running properly

For example, here is the `package.json` file for The Odin Project’s curriculum repo that houses all of the lesson files:

```json
{
  "name": "curriculum",
  "version": "1.0.0",
  "description": "[The Odin Project](https://www.theodinproject.com/) (TOP) is an open-source curriculum for learning full-stack web development. Our curriculum is divided into distinct courses, each covering the subject language in depth. Each course contains a listing of lessons interspersed with multiple projects. These projects give users the opportunity to practice what they are learning, thereby reinforcing and solidifying the theoretical knowledge learned in the lessons. Completed projects may then be included in the user's portfolio.",
  "scripts": {
    "lint:lesson": "markdownlint-cli2 --config lesson.markdownlint-cli2.jsonc",
    "lint:project": "markdownlint-cli2 --config project.markdownlint-cli2.jsonc",
    "fix:lesson": "markdownlint-cli2 --fix --config lesson.markdownlint-cli2.jsonc",
    "fix:project": "markdownlint-cli2 --fix --config project.markdownlint-cli2.jsonc"
  },
  "license": "CC BY-NC-SA 4.0",
  "devDependencies": {
    "markdownlint-cli2": "^0.12.1"
  }
}
```

If you were to clone the curriculum repo, if you ran `npm install`, npm would read this `package.json` file and see that it needs to install the `markdownlint-cli2` package. Once this package is installed, you’ll be able to run any of the four npm scripts that use that package. The curriculum repo itself does not actually contain the code for the `markdownlint-cli2` package, as anyone cloning the repo can just run `npm install` to let npm grab the code for them.

In our own projects, as we use npm to install new packages (or uninstall any!), it will automatically update our `package.json` with any new details. We will see this in action through module bundling using a package called [[Webpack]]