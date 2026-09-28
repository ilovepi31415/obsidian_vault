---
tags:
  - JS
---
Factory Functions present an alternative to [[Objects#Constructors|Constructors]] as a method for creating [[Objects]]. Their main benefits include the ability to have [[Scope and Closure#Private Variables|Private Variables]] and not needing the `new` keyword to use

## Creating a Factory Function
Factory functions are written much like a regular function, but they return an object when called
```JS
function createAnimal(name, sound) {
	const makeNoise = () => console.log(`I say ${sound}`);
	return {name, sound, makeNoise};
}
```

>[!note] Object Shorthand Notation
>When returning an object, instead of writing
>```JS
>const thatObject = { name: name, age: age, color: color };
>```
>it can be shortened to
>```javascript
>const nowFancyObject = { name, age, color };
>```
### Converting a Constructor to a Factory Function
Standard Constructors can be converted to Factory Functions without too much hassle, as shown here:

```JS
const User = function (name) {
  this.name = name;
  this.discordName = "@" + name;
}
// This can be refactored into a factory!

function createUser (name) {
  const discordName = "@" + name;
  return { name, discordName };
}
// and that's very similar, except since it's just a function,
// we don't need a new keyword
```

Simply removing `this.` keywords and returning the relevant information, your code's objects can become proper factories