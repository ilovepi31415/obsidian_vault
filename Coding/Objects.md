---
tags:
  - JS
---

Objects allow you to store key-value pairs
```js
const MyObject = {
	key: 'value',
	'key with spaces': 'other value'
	objectFunction: function() {
		// function body here
	} 
}
```

## Constructors
An object constructor allows for the reuse of an object
```js
// Constructor
function Animal(name, sound) {
	this.name = name;
	this.sound = sound;
	this.talk = function() {
		console.log(this.sound);
	};
}

// Instances of the Animal object
const Dog = new Animal('dog', 'woof');
const Cat = new Animal('cat', 'meow');
```

>[!warning]
>It is often advised not to use constructors like this to create objects due to their insecurities. Instead, [[Factory Functions]] are recommended. These use JavaScript's [[Scope and Closure|closure]] to create and modify the features of an object
## Prototypes
1. All objects have prototypes
		`Object.getPrototypeOf(MyObject)` will return that prototype
2. All prototypes are another object (up to the 'Object' object)
3. All methods and properties on the prototype are inherited by the child object
