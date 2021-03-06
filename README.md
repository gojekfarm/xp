# Introduction

`xp` is a tool created to make practising extreme programming easier.

[![Build Status](https://travis-ci.org/gojek/xp.svg?branch=master)](https://travis-ci.org/gojek/xp)
[![Coverage Status](https://coveralls.io/repos/github/gojek/xp/badge.svg?v=2)](https://coveralls.io/github/gojek/xp)

## Reference

Full list of options supported:

```
➜  ~ xp
NAME:
   xp - extreme programming made simple

USAGE:
   xp [global options] command [command options] [arguments...]

VERSION:
   (version)

COMMANDS:
     show-config, sc  Print the current config
     add-dev          Add a new developer
     init, i          Initialize a repo. Setup prepare-commit-msg hook
     set-devs         Set list of devs working on the repo
     help, h          Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --config value  set the default configuration file (default: "/Users/kidoman/.xp")
   --help, -h      show help
   --version, -v   print the versio
```

## Features

- Manage the co-authorship of commits by automatically writing appropriate* `Co-authored-by` trailers (see https://help.github.com/articles/creating-a-commit-with-multiple-authors/ for details on this standard)
- Take co-authorship information written in the first line of the commit message and convert that into appropriate `Co-authored-by` trailers (overrides all other sources)
- Ensure that the author drafting the commit is not duplicated as a `Co-authored-by` trailer
- Preserve co-authorship information when ammending commits

## Installation

The simplest way to install `xp` in your dev environment is:

```
go get -u github.com/gojek/xp
```

If you do not have `go` installed, or prefer to install a different way, you can always:

- Download a binary from the [releases](https://github.com/gojek/xp/releases) page
- `brew install gojek/tap/xp`

## Usage

`xp` stores its global configuration at `~/.xp` (can be overriden via global flag `--config`)

Most of the `xp` functionality are exposted via various subcommands:

- `show-config`: Print the current stored configuration
- `add-dev`: Add/remove developers in xp
- `init`: Add/remove repos managed by xp

A separate command `add-info` is made available for use from within `git` hooks:

- prepare-commit-msg
- commit-msg

## Example

Suppose we have a repo at `~/work/lambda` which we want to now manage using `xp` (this assumes you have already installed `xp` using the instructions above):


Add Karan Misra &lt;kidoman@gmail.com&gt; as a tracked author in the system with shortcode "km" to allow for easy referencing in future command line invocations or the first line of commit messages. Same for "akshat":

```
$ xp add-dev km "Karan Misra" kidoman@beef.com
$ xp add-dev ak "akshat" akshat@beef.com
```

Switch to the directory with the `git` repo:

```
$ cd ~/work/lambda
```

Initialize the git hooks and register the repo with `xp`:

```
$ xp init
```

Indicate that `akshat` is pairing with you by adding him using his shortcode:

```
$ xp set-devs ak
```

Commit as normal:

```
$ touch CHANGE
$ git add .
$ git commit -m"Added CHANGE"
```

Rejoice at a well formed commit message:

```
$ git log -1
commit sha (HEAD -> master)
Author: Karan Misra <kidoman@beef.com>
Date:   date

    Added CHANGE

    Co-authored-by: akshat <akshat@beef.com>
```

When working on a story (issue, etc.), `xp` makes it really easy to embed the issue id in the commit in a well formed manner:

```
$ git add .
$ git commit -m"[BEEF-123|ak] This is a nice story"
```

```
$ git log -1
commit sha (HEAD -> master)
Author: Karan Misra <kidoman@beef.com>
Date:   date

    Make world better

    Issue-id: BEEF-123

    Co-authored-by: akshat <akshat@beef.com>
```

## Bonus

If you quickly want to author a commit with someone you typically don't pair with:

```
$ xp add-dev anand "Anand Shankar" anand@beef.com
```

After making the required changes:

```
$ git add .
$ git commit -m"[anand] Make world better"
```

The commit message becomes:

```
$ git log -1
commit sha (HEAD -> master)
Author: Karan Misra <kidoman@beef.com>
Date:   date

    Make world better

    Co-authored-by: Anand Shankar <anand@beef.com>
```

Note: See how the `[anand]` from the start of the commit message has now resulted in `Anand Shankar` being added as a co-author, overriding the repo level setting (thus `akshat` is not in the list anymore.) Multiple co-authors can be similarly added by separating their aliases by `,` or `|` like so:


```
$ git commit -m"[anand,akshat] Make world better"
$ git commit -m"[anand|akshat] Make world better"
```
