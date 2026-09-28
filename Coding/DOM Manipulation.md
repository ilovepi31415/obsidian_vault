---
tags:
  - JS
---
The Document Object Model (DOM) is the tree of HTML elements in any webpage, and it is possible to use [[JavaScript]] to create and edit elements without excessive HTML code

```JS
// creates a new div referenced by the variable
const div = document.createElement('div');
```

>[!note]
>To actually [[#Adding and Removing Elements|add an element]] to the DOM, you must place it relative to an existing element}
## Styling Elements with JavaScript
It is possible to alter existing elements using [[CSS]] properties with
```JS
_element_.style._property_
```

Adding multiple properties at once can be done with
```JS
// These two options both work
_element_.style.cssText('CSS goes here');
_element_.setAtttribute('style', 'CSS goes here');
```

For example:
```JS
div.style.color = '#fff';
div.setAttribute('style', 'margin-top: 8px; margin-bottom: 4px;');

// kebab-cased properties must be replaced by camelCased ones or enclosed in brackets
div.style.fontSize = '2rem';
div.style['background-color'] = blue;
```

## Working with Classes
```JS
// adds class "new" to your new div
div.classList.add("new");

// removes "new" class from div
div.classList.remove("new");

// if div doesn't have class "active" then add it, or if it does, then remove it
div.classList.toggle("active");
```


## Parents, Children, and Siblings
The elements in a DOM are related to each other in a variety of ways, similar to a family tree
```HTML
<div class='parent'>
	<div class='child'></div>
	<div class='child'></div>
</div>
<div class='sibling'></div>
```

### Querying with Relationships
It is possible to use these relationships when querying elements in the DOM, just like you would in [[CSS]]
- `>`: children
- `+`: siblings 
```JS
const parent = document.querySelector('.parent');
// Selects all children of .parent
const child = document.querySelectorAll('.parent > .child');
const sibling = document.querySelector('parent + .sibling');
```

#### Adding and Removing Elements
You can add elements to the DOM by adding a child to any parent
```JS
parent.appendChild(child); // adds a child element to the end of the parent
parent.insertBefore(newChild, reference); // adds a `newChild` right before the `reference` element
```

Removing elements works in a similar way
```JS
parent.removeChild(child); // remove the child element
parent.removeChildren(); // removes ALL child elements
```


### Putting it All Together
```HTML
<!-- your HTML file: -->
<body>
  <h1>THE TITLE OF YOUR WEBPAGE</h1>
  <div id="container"></div>
</body>
```

```JS
// your JavaScript file
const container = document.querySelector("#container");

const content = document.createElement("div");
content.classList.add("content");
content.textContent = "This is the glorious text-content!";

container.appendChild(content);
```

# Links
[TOP Page](https://www.theodinproject.com/lessons/foundations-dom-manipulation-and-events) about DOM Manipulation