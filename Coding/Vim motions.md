---
tags:
  - VIM
---
Vim motions are the methods of interacting with the [[Vim]] code editor, but they are usable in many other places such as IDEs like VSCode or even [[Obsidian]] itself. The hands rarely need to leave the home row in a hope to ensure the most efficient possible workflow. A tutorial by YouTuber [The Primeagen](https://www.youtube.com/watch?v=X6AR2RMB5tE) has been very helpful in creating this note

## Movement
Using the arrow keys for movement requires moving the right hand considerably far, so an alternative was created. While in [[#Normal Mode]], movement is:
- `h` -> left
- `j` -> down
- `k` -> up
- `l` -> right
Additionally, prefixing the direction with a number will act the same as pushing the key that many times. For example, `5j` will move the cursor 5 lines downward. This is mostly used for vertical movement with [[#Relative Line Numbering]] enabled, but a similar method can be used for most commands
### Relative Line Numbering
When jumping in Vim, it often helps to see how far away the other lines are rather than just have them all numbered sequentially
```
  3  Code here
  2  More lovely code is going on this line
  1
45   This is the line I am currently on
  1
  2
  3  The numbers start going back up again
  4  I can get down here using '4j'
```
## Modes
There are four main modes that Vim can be in at any point
### Normal Mode
This is the default mode, used for [[#Movement]] and access to the other modes. To return to normal mode, use the `esc` key

>[!note]
>On my keyboard I have remapped `esc` to `capslock` and vice versa
### Insert Mode
Allows for text insertion and regular typing without commands. To reach insert mode, press `i`
### Visual Mode
Used for selecting groups of text, just like clicking and dragging. Best for things like yanking, pasting, and deleting specific groups of text. Enter visual mode with `v`
### Command Mode
For commands like file saving and quitting, etc. Use `:` to enter command mode. 

## List of Motions
There are a large number of motions for Vim motions. This is a shortened version of the rest of this document. For a full cheat sheet, go [here](https://vim.rtorr.com/)
### Basics
- `i`: enter [[#Insert Mode]] (inserts *before* the selected character)
- `v`: enter [[#Visual Mode]]
- `:`: enter [[#Command Mode]]

- `h`: move left\*
- `j`: move down\*
- `k`: move up\*
- `l`: move right\*

 - `w`: move forward one word at a time (moves to the *first* letter of the word)\*
 - `b`: move backward one word at a time (moves to the *first* letter of the word)\*

- `u`: undoes the most recent action
- `ctrl + r`: redoes the most recent action

- `x`: deletes the current character

Some commands can be formatted as `command count motion`, such as `y3k` to yank the current line and the 3 lines above it
- `d`: delete the current selection
	- `dw`: deletes current word\*
	- `dd`: deletes current line

- `y`: yank (copy) the current selection
	- `yy`: yanks current line (including return)

- `p`: paste onto current selection
  
>[!note]
>Both delete and yank go to the same paste buffer, so you delete and paste instead of cutting. This also applies when pasting over selected text: the replaced text will still go to paste buffer

### Commands
These are for when in [[#Command Mode]]. Compatible commands can be listed one after the other, such as `:wq` to save and quit
- `:w`: writes, or saves, the file
- `:q`: attempts to quit Vim