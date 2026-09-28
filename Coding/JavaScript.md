---
tags:
  - JS
---

JavaScript is a programming language that handles the algorithm side of many webpages, and can also be used as a standalone language

## Things to Know
- **var** vs **let**: the `var` and `let` keywords are both used for declaring variables, but they have different [[Scope and Closure|scopes]]
	- `var` defines a variable within a *function*, even if nested in a loop
	- `let` and `const` define a variable with *block scope*, and is not defined outside of the block
- [[Objects|Objects]]
	- [[Objects#Constructors|Constructors]] vs [[Factory Functions]]
- **User Input**
	- `prompt(<question>)`: opens a window prompting the user for response
	- Prompts return strings, so use `parseInt(<string>, <base = 10>)` to get a number if needed
- [[DOM Manipulation|Document Object Mode (DOM) Manipulation]] allows for editing much of the [[HTML]] of a webpage through scripting instead