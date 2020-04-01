---
layout: post
title: How to use multiple GitHub accounts on single machine
description: A detailed blog about using multiple GitHub accounts on a single machine via HTTPS or SSH
comments: true
keywords: git, github, version control, vcs, open source, how to
---
Most of us have multiple (mostly two) GitHub accounts, personal and work account. This post is about how to use them on a single machine using HTTPS or SSH.

## Using HTTPS

### Step 1: Change remote URL
```
$ git remote set-url <remote-name> https://<username>@github.com/<username>/<repo-name>.git
```
If you are setting-up new remote:
```
$ git remote add <remote-name> https://<username>@github.com/<username>/<repo-name>.git
```

### Step 2: Update git config
By default, git use a system-wide configuration file (global config file) or the one stored at top of your home directory to pick your `username` and `email`. To ensure that the commits appear as performed by correct username, you have to setup the `user.name` and `user.email` for project, too:
```
$ git config user.name <username>
$ git cofig user.email <your-email@example.com>
```
Every git repository has a hidden `.git` folder (dotfile) which stores all of git related information. You can always check the username and email for a particular project.
```
$ open .git/config // check [user] section for email and username
```
You are good to go now!
```
$ git push <remote> <branch>
```
Entering this, terminal will ask your GitHub account password (once). If it is asking for password each time you push commit, you can cache them using [git credential store](https://git-scm.com/docs/git-credential-store):
```
$ git config credential.helper store
```
> Using this helper will store your passwords unencrypted on disk, protected only by filesystem permissions. This command stores credentials indefinitely on disk for use by future Git programs.
