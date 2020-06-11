---
layout: post
title: I do Dotfiles!
description: A detailed post about dotfiles in general and my dotfiles
comments: true
keywords: dotfiles, git, zsh, version control, vcs, open source, bash, vim, iterm2, macos, applescript, bashrc, zshrc, xcode
---
One of the main advantages/beauty of Unix-like systems is that configurations of everything are very customizable. Dotfiles are simply amazing. They are tiny-little configurations files but they customize/decide a lot of your system. Putting them at the right place and using them right, will save a lot of time of your each day. With dotfiles, your machine is more organized, advanced, and customized in the way you like your system to be. Also with dotfiles, you can mess around with new packages and settings quite easily, without ever having to worry about really breaking something important. If you would break something, your dotfiles are always right there to back you up.

> **TL;DR**: Invest time learning to configure your machine and automate processes, you'll get that time back tenfold.

#### :rocket: :sparkles: _My dotfiles can be found at [**jogendra/dotfiles**](https://github.com/jogendra/dotfiles)_

## What are Dotfiles?

Dotfiles are files and folders on Unix-like systems starting with `.` (dot) that control the configuration of applications and shells on your system. Dotfiles are shell scripts that are executed to change the environment of your machine. The **“dotfiles”** name is derived from the configuration files in Unix-like systems that start with a dot (e.g. .zshrc and .gitconfig). For normal users, this indicates these are not regular documents, and by default are hidden in directory listings. You can display dotfiles inside any directory by running `ls -a` command.  [GitHub does dotfiles](https://dotfiles.github.io/) is definitely great place to explore and know more about dotfiles.

Why dotfiles start with `.` (dot) is an interesting example of **a bug that has become a feature**. Checkout interesting reads on [Why do hidden files in Unix begin with a dot?](https://www.reddit.com/r/linux/comments/at05xh/why_do_hidden_files_in_unix_begin_with_a_dot/egyj6lr/).

## GitHub Codespaces and Dotfiles

Recently GitHub announced Codespaces. According to GitHub, Codespaces uses your dotfiles repository on GitHub to personalize every new codespace that you create. Anyone can create a dotfiles repository to personalize Codespaces for their user account. If your user account on GitHub owns a public repository named dotfiles, GitHub automatically uses this repository to personalize your codespace environment. Read more about this [here](https://help.github.com/en/github/developing-online-with-codespaces/personalizing-codespaces-for-your-account).

## Sharing your Dotfiles

The main reason behind creating dotfiles is customizing your machine the way you like. Imagine the situation when your machine got broken or some reason you have to change your machine or you have changed your workplace so the machine, in that case, if you haven’t put your dotfiles on some remote machine, you will have to create the dotfile again which was was your long time iterations. With dotfiles, you can have a new machine ready in hours, providing the exact same user experience as the previous one. You can publicly put them on GitHub/Dropbox/etc or keep them private. It is worth keeping your dotfiles under version control for several reasons. Keeping a history of your changes allows you to safely experiment with new configurations, revert bad changes, and review the details of past changes. It is recommended to use different branch for different systems eg. you can put macOS focused dotfiles in one branch and linux system focused dotfiles on another branch so that it become easier for you when you shift between systems.

Mostly, dotfiles are very specific to the individual developer. What works for someone else isn’t necessarily optimal for you. Rather than using someone else's dotfiles, it is recommended to create your own dotfiles. But it is always great to look into other’s dotfiles to get better ideas, also you can use a bit and pieces of their profiles if you think that will be helpful to you. Going through other’s dotfiles is a great way to improve your own dotfiles. Before creating mine, I went through a lot of other's dotfiles on GitHub. I took this idea of organizing (structure) dotfiles from [Holman dotfiles](https://github.com/holman/dotfiles). You can get ideas from different dotfiles and cherry pick some of their part into yours, so for the same reason you can make your dotfiles public on GitHub/GitLab etc. and let the other developers pick and choose from it. Afterall, [Dotfiles are meant for sharing](http://zachholman.com/2010/08/dotfiles-are-meant-to-be-forked/).

## Securing the Dotfiles

Dotfiles often contain some private data like plain text passwords and some pieces of information you don’t wanna share publicly. Anything that is a security risk, like files in your `.ssh/` folder, is not a good choice to share using this method. Be sure to double-check your configuration files before publishing them online and triple-check that no API tokens are in your files. You can use [gitattributes](https://git-scm.com/docs/gitattributes) for git related sensitive information. Version controlled folder’s files that contain sensitive information and should not be published are kept secret using package-specific `.gitignore` files.

Mistakenly, if you commit sensitive data, such as a password or SSH key into dotfiles Git repository, you can remove it from the history. To entirely remove unwanted files from a repository's history you can use either the [`git filter-branch`](https://git-scm.com/docs/git-filter-branch) command or the [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) (an alternative to `git filter-branch`) open source tool. You can check out GitHub's guide on [Removing sensitive data from a repository](https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository).

I would recommend you to use [Passwordstore](https://www.passwordstore.org/) (pass), the standard Unix password manager. It provides a secure way to encrypt, share, manage, and use private data or passwords. **pass** makes managing these individual password files extremely easy. All passwords live in `~/.password-store`, and `pass` provides some nice commands for adding, editing, generating, and retrieving passwords. It is a very short and simple shell script. It's capable of temporarily putting passwords on your clipboard and tracking password changes using `git`. You can [read more about pass here](https://git.zx2c4.com/password-store/about/).

Do checkout [Keep sensitive data encrypted in dotfiles](https://www.outcoldman.com/en/archive/2015/09/17/keep-sensitive-data-encrypted-in-dotfiles/). Also, check out [how to secure your dotfiles](https://abdullah.today/encrypted-dotfiles/).
