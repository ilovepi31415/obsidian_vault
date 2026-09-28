---
tags:
  - JS
---
## Scope
Scope refers to the section of code that a variable is accessible from. In [[JavaScript]], the different variable creation methods reflect in the variable's scope.
### var vs. let vs. const
- `var` defines a variable as *function scoped*, or accessible from within the current function
- `const` and `let` define a variable as *block scoped*, or accessible from within the nearest set of `{ curly braces }`, like a function, if statement, etc.
When not defined within a function or set of braces, the variable is considered to be *global*, or accessible from anywhere in the code file

```JS
let globalAge = 23; // This is a global variable

// This is a function - and hey, a curly brace indicating a block
function printAge (age) {
  var varAge = 34; // This is a function scoped variable

  // This is yet another curly brace, and thus a block
  if (age > 0) {
    // This is a block-scoped variable that exists
    // within its nearest enclosing block, the if's block
    const constAge = age * 2;
    console.log(constAge);
  }

  // ERROR! We tried to access a block scoped variable
  // not within its scope
  console.log(constAge);
}

printAge(globalAge);

// ERROR! We tried to access a function scoped variable
// outside the function it's defined in
console.log(varAge);
```

## Closures
When a function is defined, it has access to:
- Its own local variables.
- The variables in the outer function (if any).
- Global variables.
Even if that function is returned within the scope of another function, it will retain access to the outer functions variables
```JS
function outerFunction() {
  let outerVariable = 'I am from the outer function!';
  
  function innerFunction() {
    console.log(outerVariable);
  }
  
  // This function keeps access to outerVariable, even though the outerFunction is closed after the function is returned
  return innerFunction;
}

const closureExample = outerFunction();
closureExample(); // Output: 'I am from the outer function!'
```

### Private Variables
Because of scope, it becomes possible to create variables that are only accessible via function methods. These are known as *private variables*
```JS
function createCounter() {
	let counter = 0;
	
	return {
		increment: function() {
			counter++;
		},
		decrement: function() {
			counter--;
		},
		display: function() {
			console.log(counter);
		}
	}
}

const myCounter = createCounter();
myCounter.increment(); // Counter now has value 1
myCounter.display(); // Ouput: '1'
```

Even though `counter` is not defined within the inner functions, they still have access to is, as it in contained inside the outer function. Note that it is not possible to modify the value of `counter` directly. Instead one must use the included methods