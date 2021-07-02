---
layout: post
title:  "Setting up Powerline with Go in a bash terminal"
date:   2021-07-01 14:25:46 +0200
categories: work setup
---
Powerline is a statusline and presents status and prompt for different version control systems like Git. Particularly, this post presents a guide to install the Go implementation, [Powerline-Go](https://github.com/justjanne/powerline-go) for bash shell.

**Table of Contents**

- [Version Control](#version-control)
- [Installation](#installation)
- [Use](#use)

## Version Control

All the version control systems supported by powerline shell give you a
quick look into the state of your repo:

- The current branch is displayed and changes background color when the
  branch is dirty.
- When the local branch differs from the remote, the difference in number
  of commits is shown along with `⇡` or `⇣` indicating whether a git push
  or pull is pending.

In addition, git has a few extra symbols:

- `✎` -- a file has been modified, but not staged for commit
- `✔` -- a file is staged for commit
- `✼` -- a file has conflicts
- `+` -- untracked files are present
- `⚑` -- stash is present

Each of these will have a number next to it if more than one file matches.


## Installation

Requires Go 1.12+ and fonts for powerline.

```bash
sudo apt install golang-go fonts-powerline
```

For some shells (for example Visual Studio Code terminal) can be experimented some font troubles. By installing [Cascadia Code PL](https://github.com/microsoft/cascadia-code) (from the ttf file).

Then by executing next command PowerLineGo will be installed.

```bash
cd $HOME
go get -u github.com/justjanne/powerline-go
```

## Use

Add the following to your `.bashrc` to start to use Powerline-Go:

```bash
GOPATH=$HOME/go
function _update_ps1() {
    PS1="$($GOPATH/bin/powerline-go -error $? -jobs $(jobs -p | wc -l))"

    # Uncomment the following line to automatically clear errors after showing
    # them once. This not only clears the error for powerline-go, but also for
    # everything else you run in that shell. Don't enable this if you're not
    # sure this is what you want.

    #set "?"
}

if [ "$TERM" != "linux" ] && [ -f "$GOPATH/bin/powerline-go" ]; then
    PROMPT_COMMAND="_update_ps1; $PROMPT_COMMAND"
fi
```