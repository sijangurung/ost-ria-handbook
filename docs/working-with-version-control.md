# Working With Version Control

## Git

First read: [Getting Started - Git Official Documentation][git-getting-started]

After installing `git` using your preferred method then follow: [First Time Git
Setup - Git Official Documentation][first-time-git-setup]

This will help you set up your identity and editor.

Get familiar with the [Pro Git Book][pro-git-book] it will function
as a great reference in the future and explains both simple and advanced
topics.

Read: [How to Write a Git Commit Message][good-commit]

### Tricks and other useful stuff

Some of there are short versions from the [Pro Git Book][pro-git-book]

#### Rebase

*Never* use `git rebase` when standing in the `master` branch.

##### Common use cases

Update the current branch with the latest changes from the base branch.

```sh
git rebase <base-branch>
```

[Rebase - Git Official Documentation][git-rebase]

Combine multiple commits into one commit. This is useful when creating a pull
request and before merging a pull request after a review.

```sh
git rebase --interactive <base-branch>
```

This will bring up your configured editor with content similar to this.

```gitrebase
pick f7f3f6d changed my name a bit
pick 310154e updated README formatting and added blame
pick a5f4a0d added cat-file

# Rebase 710f0f8..a5f4a0d onto 710f0f8
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

Replace `pick` with `squash` for all the commits except the first one and save
the file. This will combine all the changes from the commits into a new commit.

```gitrebase
pick f7f3f6d changed my name a bit
squash 310154e updated README formatting and added blame
squash a5f4a0d added cat-file

# Rebase 710f0f8..a5f4a0d onto 710f0f8
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

Now run `git push --force` to update the remote version of the branch

[Rewriting-History - Git Official Documentation][git-rewriting-history]

### Useful aliases

[Git Aliases][git-aliases] used to create new `git` subcommands. They are
stored in your `~/.gitconfig` file under the `[alias]` entry.

A compact and pretty log:

```gitconfig
[alias]
	lg = log --date-order --graph --decorate --pretty=format:'%C(yellow)%h%Creset %C(magenta)%d%Creset %s %C(blue)(%an)%Creset %C(green)%cr%Creset'
```

Example:

```sh
git lg
```

Generate a `.gitignore` file:

```gitconfig
[alias]
	ignore = "!gi() { curl -L -s https://www.gitignore.io/api/$@ ;}; gi"
```

Example:

```sh
git ignore vim,linux,macos,python > .gitignore
```


### Useful scripts

Here are some useful scripts to automate some of our workflows when using Git.
Store these scripts in a directory that you have in your `$PATH` and use them
as subcommands in `git` e.g. `git merge-pull-request`.

#### `git-create-pull-request`

Creates a new branch for your pull request and pushes that branch to the remote
repository.

##### Command example

```sh
git create-pull-request my-branch
```

##### Script code

```sh
#!/usr/bin/env bash

set -e

branch=${1}
base="master"

echo "Creating pull request ${branch} from ${base}"

git checkout $base
git pull --rebase
git checkout -b $branch
git push --set-upstream origin $branch
```

#### `git-update-pull-request`

Updates the local and remote version of your branch with changes from the
remote base branch.

##### Command example

```sh
# With the branch checked out
git update-pull-request

# Without the branch checked out
git update-pull-request my-branch
```

##### Script code

```sh
#!/usr/bin/env bash

set -e

current_branch=$(git rev-parse --abbrev-ref HEAD)
branch=${1:-$current_branch}
base="master"

echo "Updating pull request ${branch} from ${base}"

git checkout $base
git pull --rebase
git checkout $branch
git rebase $base
git push --force
```

#### `git-merge-pull-request`

Merges a branch into base branch and updates the remote base branch.
Deletes the local and remote pull request branch if merging was uploaded. This
makes sure you merge pull request correctly without using the GitHub merge
button (that button often creates a merge commit).

##### Command example

```sh
# With the branch checked out
git merge-pull-request

# Without the branch checked out
git merge-pull-request my-branch
```

##### Script code

```sh
#!/usr/bin/env bash

set -e

current_branch=$(git rev-parse --abbrev-ref HEAD)
branch=${1:-$current_branch}
base="master"

echo "Merging pull request ${branch} into ${base}"

git checkout $base
git pull --rebase
git merge --ff-only $branch
git push
git branch -d $branch
git push origin :$branch
```

[first-time-git-setup]: https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup
[git-aliases]: https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases
[git-getting-started]: https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control
[git-rebase]: https://git-scm.com/docs/git-rebase
[git-rewriting-history]: https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History
[good-commit]: https://chris.beams.io/posts/git-commit/
[pro-git-book]: https://git-scm.com/book/en