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

### Shell prompt

Displaying some basic information about the git repository you are on your
prompt will help you know the state of you repository without running commands.

#### Activation

In `~/.bash_profile` add:

```sh
source $HOME/.bash_prompt
```

#### Code

Store the code bellow in `~/.bash_prompt`

```sh
# Shamelessly copied from https://github.com/gf3/dotfiles
# Screenshot: http://i.imgur.com/s0Blh.png

# -*- mode: sh -*-
# vi: set ft=sh :

BOLD="\033[1m"
RESET="\033[m"

BLACK="\033[0;30"
RED="\033[0;31m"
GREEN="\033[0;32m"
YELLOW="\033[0;33m"
BLUE="\033[0;34m"
MAGENTA="\033[0;35m"
CYAN="\033[0;36m"
WHITE="\033[0;37m"

BRIGHT_BLACK="\033[0;90"
BRIGHT_RED="\033[0;91m"
BRIGHT_GREEN="\033[0;92m"
BRIGHT_YELLOW="\033[0;93m"
BRIGHT_BLUE="\033[0;94m"
BRIGHT_MAGENTA="\033[0;95m"
BRIGHT_CYAN="\033[0;96m"
BRIGHT_WHITE="\033[0;97m"

BOLD_BLACK="\033[1;30"
BOLD_RED="\033[1;31m"
BOLD_GREEN="\033[1;32m"
BOLD_YELLOW="\033[1;33m"
BOLD_BLUE="\033[1;34m"
BOLD_MAGENTA="\033[1;35m"
BOLD_CYAN="\033[1;36m"
BOLD_WHITE="\033[1;37m"

export BLACK
export RED
export GREEN
export YELLOW
export BLUE
export MAGENTA
export CYAN
export WHITE
export BOLD
export RESET

export BOLD_BLACK
export BOLD_RED
export BOLD_GREEN
export BOLD_YELLOW
export BOLD_BLUE
export BOLD_MAGENTA
export BOLD_CYAN
export BOLD_WHITE

function parse_git_branch {
  branch_prefix=""
  branch=$(git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/')
  git_status_clean=$(git status --porcelain 2> /dev/null)
  indexed_modified=$(echo -e "$git_status_clean" | grep "^M" | wc -l | awk '{print $1}')
  not_indexed_modified=$(echo -e "$git_status_clean" | grep "^.M" | wc -l | awk '{print $1}')
  indexed_new=$(echo -e "$git_status_clean" | grep "^A" | wc -l | awk '{print $1}')
  not_indexed_new=$(echo -e "$git_status_clean" | grep "^.A" | wc -l | awk '{print $1}')
  indexed_deleted=$(echo -e "$git_status_clean" | grep "^D" | wc -l | awk '{print $1}')
  not_indexed_deleted=$(echo -e "$git_status_clean" | grep "^.D" | wc -l | awk '{print $1}')
  untracked=$(echo -e "$git_status_clean" | grep "^??" | wc -l |awk '{print $1}')

  remote=""
  curr_remote=$(git config branch.${branch}.remote 2> /dev/null)
  if [[ -n $curr_remote ]]; then
    curr_merge_branch=$(git config branch.${branch}.merge | sed -e "s/refs\/heads\///g");
    ahead=$(git rev-list --left-only --count ${branch}...origin/${curr_merge_branch} 2> /dev/null)
    behind=$(git rev-list --left-only  --count origin/${curr_merge_branch}...${branch} 2> /dev/null)
    if [[ -n $ahead && $ahead != 0 ]]; then
      remote="$remote ${YELLOW}↑$ahead${RESET}"
    fi
    if [[ -n $behind && $behind != 0 ]]; then
      remote="$remote ${YELLOW}↓$behind${RESET}"
    fi
  fi

  local workspace=""
  if [[ $untracked != 0 || $not_indexed_modified != 0 || $not_indexed_deleted != 0 ]]; then
    workspace="  ${RED}+$untracked ~$not_indexed_modified -$not_indexed_deleted${RESET}"
  fi

  local staged=""
  if [[ $indexed_new != 0 || $indexed_modified != 0 || $indexed_deleted != 0 ]]; then
    staged="  ${GREEN}+$indexed_new ~$indexed_modified -$indexed_deleted${RESET}"
  fi

  if [ $branch ]; then
    echo -e " on $MAGENTA$branch_prefix $branch$RESET$workspace$staged$remote"
  fi
}

PREFIX=""
if [[ -n "$SSH_CLIENT$SSH2_CLIENT$SSH_TTY" ]] ; then
  PREFIX="\[$RED\]\u\[$RESET\] at \[$YELLOW\]\h\[$RESET\] in "
fi

export PS1="${PREFIX}\[$BLUE\]\w\[$RESET\]\$([[ -n \$(git branch 2> /dev/null) ]])\$(parse_git_branch)\n\[$YELLOW\]❯ \[$RESET\]"
export PS2="\[$YELLOW\]→ \[$RESET\]"
```


[first-time-git-setup]: https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup
[git-aliases]: https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases
[git-getting-started]: https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control
[git-rebase]: https://git-scm.com/docs/git-rebase
[git-rewriting-history]: https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History
[good-commit]: https://chris.beams.io/posts/git-commit/
[pro-git-book]: https://git-scm.com/book/en