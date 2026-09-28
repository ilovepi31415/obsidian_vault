Git is a version control program (I think that's what it's called) used by [[GitHub]] and programmers to keep track of their work.

## Useful Git Commands
Make sure to put "git" before these commands; e.g., `$git commit`

- `add`: Stages files to prepare them for commit *(I don't really like this one, so I use my [[#Aliases|aliases]])*
	- `-A` adds all available files
- `clone <url>`: Copies a remote to the local machine
- `commit`: Saves changes locally
	- `-m <message>` allows you to commit without needing to open a text editor
- `log`: Lists the most recent commits, along with their authors
- `merge`: Allows multiple people/features to develop in parallel, then be connected back together. [[Git Merge|details]]
- `push: Stores local changes to the origin
	- `$git push <remote> <branch>` can be used for more specificity, but without parameters it will default to `$git push origin main`
- `rebase -i HEAD~<commits>`: An interactive rebase that retrieves the latest commits and allows you to merge and rename them
- `status`: Compares the local files to the local and remote repos


## Aliases
I like to use aliases (at time of writing, at least). They're very helpful to speed up some of the more repetitive things:

- `addall = add -A`: Adds all modified files
- `ll = log --oneline --pretty=format: '%C(auto)%h%C(red)%as%C(blue)%aN%C(auto)%d%C(green) %s' --graph`: Formats log statements to be more concise and aesthetic
- `qc = "!git addall && git commit"`  Saves and commits
- `qp = "!git addall && git commit && git push"` Saves, commits, and pushes
- `ri = "!git rebase -i HEAD~$1 #"`: I'm really bad at remembering how to rebase the HEAD, so i just do `ri N` to rebase the last *N* commits
- `st = status -sb`: Formats status to be more concise
