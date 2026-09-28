---
tags:
---
Webpack is one of the most popular [[JavaScript]] bundlers, meaning it will combine all the files of a webpage into a single one when provided with an [[Import and Export#Linking Modules to HTML|entry point]].
## Installation

To add Webpack to a project, run the following commands to install the required packages:
```bash
npm install --save-dev webpack webpack-cli
```

Using the `--save-dev` flag means the packages will be included as dependencies in the project files (You can also use `-D`)
```json
// package.json
{
	devDependencies: {
		"webpack": "^5.99.7", // Whatever version is used
		"webpack-cli": "^6.0.1", 
	}
}
```

A `node_modules` directory and a `package-lock.json` got auto-generated. `node_modules` holds Webpack's actual code, and `package-lock.json` is just another file npm uses to track more specific package information.
## src and dist

When dealing with Webpack (and often with any other bundler or build tool), we have two very important directories: `src` (short for “source”) and `dist` (short for “distribution”). We could technically call these directories whatever we want, but these names are conventions.

`src` is where we keep all of our website’s source code, essentially where all of our work will be done (with an exception being altering any configuration files in the root of the project). When we run Webpack to bundle our code, it will output the bundled files into the `dist` directory.

The idea is that if someone were to fork or clone the project, they would not need the `dist` directory, as they’d just be able to run Webpack to build from `src` into their own `dist`. Similarly, to deploy our website, we would only need the `dist` code and nothing else. Work inside `src`, build into `dist`, then deploy from there
## Adding files

Create a [[#src and dist|src]] directory and add all needed files to it. Different file types require slightly different approaches

### JavaScript

JavaScript is the simplest language for Webpack to bundle. Say we have two .js files:
```javascript
// script.js
import { greeting } from "./greeting.js";

console.log(greeting);
```

```javascript
// greeting.js
export const greeting = "Hello, Odinite!";
```

Our `script.js` file is dependent upon `greeting.js`, so our entry point is `script.js`. Outside of the `src` directory, create a file called `webpack.config.js`. This will hold all of the config data we need for bundling
```js
// webpack.config.js
const path = require("path");

module.exports = {
  mode: "development",
  entry: "./src/script.js",
  output: {
    filename: "main.js",
    path: path.resolve(__dirname, "dist"),
    clean: true,
  },
};

```

There are a few important sections of this code:

- `mode`: Different [[Webpack#Modes|modes]] create different optimizations when the code is bundled. 
- `entry`: A file path from the config file to whichever file is our entry point, which in this case is `src/script.js`.
- `output`: An object containing information about the output bundle.
    - `filename`: The name of the output bundle - it can be anything you want
    - `path`: The path to the output directory, in this case, `dist`. If this directory doesn’t already exist when we run Webpack, it will automatically create it for us as well. Don’t worry too much about why we have the `path.resolve` part - this is just the way Webpack recommends we specify the output directory
    - `clean`: If we set this to `true`, webpack will empty out the `dist` folder every time we run it so it only contains the newest version

### Modes

Different modes in webpack make the bundler optimize things in various ways. Two main modes are `development` and `production`. To save the difficulty of editing the [[Webpack#Potential config file|config file]] ever time you want to change modes, you can create two separate files and then choose between them via [[Webpack#npm Scripts|scripts]].

```json
"build": "webpack --config webpack.prod.js",
"dev": "webpack serve --config webpack.dev.js"
```

### HTML

We need both [[HTML]] and [[CSS]] if we want to properly bundle a full website. Webpack doesn't natively support other languages, but we can solve this by installing another package
```bash
npm install --save-dev html-webpack-plugin
```

Now if we make an `template.html` file inside of `src`, we can write HTML as normal. However, we do NOT need to include a `<script>` tag, since Webpack will handle that for us

In the config file, there are a couple extra things that you need to add:
```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");
```

Then under `module.exports`, include:
```javascript
 plugins: [
    new HtmlWebpackPlugin({
      template: "./src/template.html",
    }),
  ],
```
To make sure the plugin is used correctly
### CSS

Packages to install:
```bash
npm install --save-dev style-loader css-loader
```

Config additions (under `module.exports`):
```javascript
 module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"], // Array order matters!!
      },
    ],
  },
```

This checks all files for the `.css` extension and then applies our plugins to them

>[!warning] Loader Order Matters!
Notice how we put `css-loader` **at the end** of the array. We **must** set this order and not the reverse.

To import an added CSS file, add it as a side effect import, meaning the code will be run even though no variables are actually imported
```javascript
// script.js
import "./styles.css";
import { greeting } from "./greeting.js";

console.log(greeting);
```

>[!important] DO NOT FORGET TO IMPORT
>You **have to** import the CSS into your JS, otherwise it will not be included at all
### Images and Other Sources
Images are loaded differently based on which file they are referenced from
#### CSS
Sources loaded using `url()` in CSS do not need any extra resources
#### HTML
When using `<img src="">`, we need an extra package
```bash
npm install --save-dev html-loader
```

Then add the following to the config file under the `module.rules` array:
```javascript
{
  test: /\.html$/i,
  loader: "html-loader",
}
```
#### JS
When we manipulate image sources through the [[DOM Manipulation|DOM]], we need to add some code to the `module.rules` array (The regex can be modified if necessary for other file types):
```javascript
{
  test: /\.(png|svg|jpg|jpeg|gif)$/i,
  type: "asset/resource",
}
```

When using the image, we need to import it to the relevant .js file:
```javascript
import odinImage from "./odin.png";
   
const image = document.createElement("img");
image.src = odinImage;
   
document.body.appendChild(image);
```

This makes sure Webpack understands that our file is not simply plain text, but refers to a local source image

### Potential config file
With all of these additions, the config file might look something like this:
```js
// webpack.config.js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development",
  entry: "./src/script.js",
  output: {
    filename: "main.js",
    path: path.resolve(__dirname, "dist"),
    clean: true,
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./src/template.html",
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.html$/i,
        loader: "html-loader",
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: "asset/resource",
      },
    ],
  },
};
```

Many of these things are not necessary for every project, but most of them will at least be used some of the time. There are also some cases where more packages may be needed

## Webpack Dev Server

It's possible to run a local web server, similar to VSC's Live Preview, so that changes are updated live on the website. It will bundle the code every time a file is saved

Install:
```bash
npm install --save-dev webpack-dev-server
```

Add the following to the config file under `module.exports`:
```javascript
devtool: "eval-source-map",
  devServer: {
    watchFiles: ["./src/index.html"],
  },
```

The `eval-source-map` makes sure that all errors are properly reported along with their files and line number. The `watchFiles` keep all files updated in the server; otherwise the HTML files would not work properly with the update

Start a server:
```bash
npx webpack serve
```
The site will then be available at [http://localhost:8080/](http://localhost:8080/) by default

## npm Scripts

Using [[npm]] scripts we are able to do complicated operations more quickly (similar to using aliases in [[Git]]). To create a script, add a section to the `package.json` file like so:

```json
{
  // ... other package.json stuff
  "scripts": {
    "build": "webpack",
    "dev": "webpack serve",
    "deploy": "git subtree push --prefix dist origin gh-pages"
  },
  // ... other package.json stuff
}
```

>[!note] npx commands in scripts
>You don't need to include the `npx` when writing a script. It will be added automatically

To execute a script, run `npm run <program-name>` in the terminal