---
tags:
  - JS
aliases:
  - ES6 Modules
---
## Why ESM?

When loading [[JavaScript]] files with [[HTML]], it used to be that all files were loaded into the same global scope, so any variable in one file could be accessed in another

Let’s say we have two scripts, `one.js` and `two.js`, and we link them in our HTML as separate scripts.

```html
<script src="one.js" defer></script>
<script src="two.js" defer></script>
```

```javascript
// one.js
const greeting = "Hello, Odinite!";
```

```javascript
// two.js
console.log(greeting);
```

`two.js` has access to the variable `greeting`, and so it logs without any errors

But the global way of doing things led to a lot of problems. Our global variables can unintentionally be modified by other files, which causes many issues if we aren't careful. With the introduction of ES6 Modules, we have the option for each file be self contained
## Usage

There are two different kinds of importing and exporting, `default` and `named`
### Named Exports

For `named` exports, we can use the `export` keyword on individual variables or use `{}` to select multiple things

```javascript
// one.js
export const greeting = "Hello, Odinite!"; // This exports them individually
export const farewell = "Bye bye, Odinite!";

const one = "I can count to one!";
const two = "I cannot count to two...";
export { one, two } // This allows us to export multiple variables at once
```

For importing, we must specify the names of the variables we wish to import in `{}` braces (even if only one variable is imported!), along with the file where they originated

```js
// two.js
import { greeting, farewell } from "./one.js";

console.log(greeting); // "Hello, Odinite!"
console.log(farewell); // "Bye bye, Odinite!"
```

>[!warning] Braces are not all the same
>The `{}` used in importing and exporting do not act the same way as when used for objects. An object is ***not*** exported, simply a list of variables

### Default Exports

Each file can default export a single thing. The default export does not need to be named, but is otherwise exported very similarly to a named export 

```javascript
// one.js
export default "Hello, Odinite!";

// or
const greeting = "Hello, Odinite!";
export default greeting;
```

When importing a default variable, braces are not needed, and any name can be chosen

```javascript
// two.js
import helloOdinite from "./one.js";

console.log(helloOdinite); // "Hello, Odinite!"
```

>[!note] A single variable
If only one variable needs to be exported, either default exporting or a single named export is permissible. Preference may vary from person to person

## Linking Modules to HTML

When using [[Import and Export#Why ESM?|ES6 Modules]] you don't have to list every [[JavaScript]] file. Instead, you create an entry point to your modules with `type="module"`

```html
<script src="two.js" type="module"></script>
```

### Dependencies

When figuring out which file should be the entry point, pay attention to which files depend on other ones. In our above examples, `two.js` imports variables from `one.js`, meaning `two.js` depends on `one.js`, so we have the following **dependency graph**:

```text
importer  depends on  exporter
two.js <-------------- one.js
```

When we load `two.js` as a module, the browser will see that it depends on `one.js` and load the code from that file as well. If we instead used `one.js` as our entry point, the browser would see that it does not depend on any other files, and so would do nothing else. Our code from `two.js` would not be used, and nothing would get logged!

If we had another file, `three.js`, that exported something and `two.js` imported from it, then `two.js` would still be our entry point, now depending on both `one.js` and `three.js`.

```text
two.js <-------------- one.js
              └------- three.js
```

Or perhaps instead of `two.js`, `one.js` imports from `three.js`. In which case, `two.js` would still be our entry point and depend on `three.js` indirectly through `one.js`.

```text
two.js <-------------- one.js <-------------- three.js
```

>[!note]
>We do not need to add the `defer` attribute, as `type="module"` will automatically defer script execution for us



