A website and service used mainly to host [[Git]] repositories

## Deploying a Page
To deploy a repo as a page, go to the repo and then `Settings > Pages` and then choose a branch to deploy

## Cloning a Repo
Cloning is copying a remote repo to the local device. To clone a repo, use `git clone` followed by the HTTPS link for the repo. You can also use an SSH key, which is is what I do for [[The Odin Project]]. IDK exactly why, but it's what they suggested

## Template Repos
Often, a certain type of project (like a [[Webpack]] bundler) uses very similar files and code to get started each time. To save the time of rewriting it for each project, it is possible to make template repositories by checking a box under the repos settings page

## Creating a Repo from the CLI
Use github CLI, to make a new repo: `gh repo create [<name>] [flags]`. Flags include whether the repo is `--public` or `--private`, and if you want to `--clone` the repo locally