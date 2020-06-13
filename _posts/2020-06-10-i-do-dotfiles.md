---
layout: post
title: I do Dotfiles!
description: A detailed post about dotfiles in general and my dotfiles
comments: true
keywords: dotfiles, git, zsh, version control, vcs, open source, bash, vim, iterm2, macos, applescript, bashrc, zshrc, xcode
---
One of the main advantages/beauty of Unix-like systems is that configurations of everything are very customizable. Dotfiles are simply amazing. They are tiny-little configurations files but they customize/decide a lot of your system. Well-organized and understandable dotfiles basically allow you to automatize complex tasks that you repeat everyday. With dotfiles, your machine is more organized, advanced, and customized in the way you like your system to be. Also with dotfiles, you can mess around with new packages and settings quite easily, without ever having to worry about really breaking something important. If you would break something, your dotfiles are always right there to back you up.

> **TL;DR**: Invest time learning to configure your machine and automate processes, you'll get that time back tenfold.

> Your experience, knowledge and talent are key to success, but some kick-ass dotfiles speed you up.

#### :rocket: :sparkles: _My dotfiles can be found at [**jogendra/dotfiles**](https://github.com/jogendra/dotfiles)_

### What are dotfiles?

Dotfiles are files and folders on Unix-like systems starting with `.` (dot) that control the configuration of applications and shells on your system. Dotfiles are shell scripts that are executed to change the environment of your machine. The **“dotfiles”** name is derived from the configuration files in Unix-like systems that start with a dot (e.g. .zshrc and .gitconfig). For normal users, this indicates these are not regular documents, and by default are hidden in directory listings. You can display dotfiles inside any directory by running `ls -a` command.  [GitHub does dotfiles](https://dotfiles.github.io/) is definitely great place to explore and know more about dotfiles.

Why dotfiles start with `.` (dot) is an interesting example of **a bug that has become a feature**. Checkout interesting reads on [Why do hidden files in Unix begin with a dot?](https://www.reddit.com/r/linux/comments/at05xh/why_do_hidden_files_in_unix_begin_with_a_dot/egyj6lr/).

<img class="fullimg" alt="dotfiles" src="https://user-images.githubusercontent.com/20956124/84434654-14866500-ac4e-11ea-8de3-413870e37893.png">

### GitHub Codespaces and Dotfiles

Recently GitHub announced Codespaces. According to GitHub, Codespaces uses your dotfiles repository on GitHub to personalize every new codespace that you create. Anyone can create a dotfiles repository to personalize Codespaces for their user account. If your user account on GitHub owns a public repository named dotfiles, GitHub automatically uses this repository to personalize your codespace environment. Read more about this [here](https://help.github.com/en/github/developing-online-with-codespaces/personalizing-codespaces-for-your-account).

### Sharing your dotfiles

The main reason behind creating dotfiles is customizing your machine the way you like. Imagine the situation when your machine got broken or some reason you have to change your machine or you have changed your workplace so the machine, in that case, if you haven’t put your dotfiles on some remote machine, you will have to create the dotfile again which was was your long time iterations. With dotfiles, you can have a new machine ready in hours, providing the exact same user experience as the previous one. You can publicly put them on GitHub/Dropbox/etc or keep them private. It is worth keeping your dotfiles under version control for several reasons. Keeping a history of your changes allows you to safely experiment with new configurations, revert bad changes, and review the details of past changes. It is recommended to use different branch for different systems eg. you can put macOS focused dotfiles in one branch and linux system focused dotfiles on another branch so that it become easier for you when you shift between systems. I have made [my own dotfiles](https://github.com/jogendra/dotfiles) open sourced, and parts of my configuration are inspired by other people’s dotfiles. Afterall, dotfiles are meant for sharing.

### Do not blindly copy dotfiles

Mostly, dotfiles are very specific to the individual developer. What works for someone else isn’t necessarily optimal for you. Rather than using someone else's dotfiles, it is recommended to create your own dotfiles. But it is always great to look into other’s dotfiles to get better ideas, also you can use a bit and pieces of their profiles if you think that will be helpful to you. Going through other’s dotfiles is a great way to improve your own dotfiles. Before creating mine, I went through a lot of other's dotfiles on GitHub. I took this idea of organizing (structure) dotfiles from [Holman dotfiles](https://github.com/holman/dotfiles). You can get ideas from different dotfiles and cherry pick some of their part into yours, so for the same reason you can make your dotfiles public on GitHub/GitLab etc. and let the other developers pick and choose from it. I do not agree with [“dotfiles are meant to be forked”](http://zachholman.com/2010/08/dotfiles-are-meant-to-be-forked/).

### Securing the dotfiles

Dotfiles often contain some private data like plain text passwords and some pieces of information you don’t wanna share publicly. Anything that is a security risk, like files in your `.ssh/` folder, is not a good choice to share using this method. Be sure to double-check your configuration files before publishing them online and triple-check that no API tokens are in your files. You can use [gitattributes](https://git-scm.com/docs/gitattributes) for git related sensitive information. Version controlled folder’s files that contain sensitive information and should not be published are kept secret using package-specific `.gitignore` files.

Mistakenly, if you commit sensitive data, such as a password or SSH key into dotfiles Git repository, you can remove it from the history. To entirely remove unwanted files from a repository's history you can use either the [`git filter-branch`](https://git-scm.com/docs/git-filter-branch) command or the [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) (an alternative to `git filter-branch`) open source tool. You can check out GitHub's guide on [Removing sensitive data from a repository](https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository).

I would recommend you to use [Passwordstore](https://www.passwordstore.org/) (pass), the standard Unix password manager. It provides a secure way to encrypt, share, manage, and use private data or passwords. **pass** makes managing these individual password files extremely easy. All passwords live in `~/.password-store`, and `pass` provides some nice commands for adding, editing, generating, and retrieving passwords. It is a very short and simple shell script. It's capable of temporarily putting passwords on your clipboard and tracking password changes using `git`. You can [read more about pass here](https://git.zx2c4.com/password-store/about/).

Do checkout [Keep sensitive data encrypted in dotfiles](https://www.outcoldman.com/en/archive/2015/09/17/keep-sensitive-data-encrypted-in-dotfiles/). Also, check out [how to secure your dotfiles](https://abdullah.today/encrypted-dotfiles/).

### Building my own dotfiles

I came across a dotfiles repository of someone on GitHub sometime ago, I didn’t know about them before. I have always been a CLI (Command Line Interface) fanatic. I keep changing CLI configs, themes, etc time to time. I have been using _iTerm2_ with _ZSH_ and _oh-my-zsh_ from long ago. Above everything, I love scripting in general and automating stuff. For the same reason, I love shell scripting. I went through a lot of repositories from various people, but every single one of them seemed to be totally different because again dotfiles are very specific to the individual developer.

My dotfiles contain freshly brewed configuration files for various CLI applications based on macOS. With plenty of useful shell/Git aliases, shell scripts, AppleScripts, and command defaults, etc, it features beautifully customized ZSH and iTerm2 environments and a lot more stuff.

#### [jogendra/dotfiles](https://github.com/jogendra/dotfiles)

When I was looking for a way to organize my dotfiles, I found [Zach Holman’s dotfiles](https://github.com/holman/dotfiles) way of organizing the dotfiles is the best which is called **Topical organization** which is organizing the different parts of dotfiles in directories, each entitled to a specific subject. By topical organization, all AppleScripts go inside the `AppleScript/` directory, git settings are in a `git/` directory, and so on. Everything's built around topic areas. If you're adding a new area to your forked dotfiles — say, _"vscode"_ — you can simply add a vscode directory and put files in there. Anything with an extension of `.zsh` will get automatically included in your shell. Anything with an extension of _`.symlink`_ will get symlinked without extension into `$HOME` when you run `installers/bootstrap`.

<img class="sideimg" align="right" alt="" src="https://user-images.githubusercontent.com/20956124/84314457-b55d1d80-ab85-11ea-9bed-6009dcb8be18.png">

There are a few special files in the hierarchy:

- **bin/**: Anything in `bin/` will get added to your `$PATH` and be made available everywhere.
- **topic/*.zsh**: Any files ending in `.zsh` get loaded into your environment.
- **topic/path.zsh**: Any file named `path.zsh` is loaded first and is expected to set up `$PATH` or similar.
- **topic/completion.zsh**: Any file named _`completion.zsh`_ is loaded last and is expected to set up autocomplete.
- **topic/install.sh**: Any file named `install.sh` is executed when you run `installers/install`. To avoid being loaded automatically, its extension is `.sh`, not `.zsh`.
- **topic/*.symlink**: Any file ending in `*.symlink` gets symlinked into your `$HOME`. This is so you can keep all of those versions in your dotfiles but still keep those autoloaded files in your home directory. These get symlinked in when you run `installers/bootstrap`.

### The Components

#### AppleScripts

AppleScript is a scripting language developed by Apple to help people automate their work processes on the MacOS. AppleScript is an extremely simple, almost English-like language, but automation it does is pure gold. I sometimes write AppleScripts to automate stuff on my mac where it involves a lot of clicks or similar and I use them using shell aliases.
Here is a simple AppleScript that I wrote to open _iTerm2_ tabs with right directory for all work-related projects.

```AppleScript
property examplePath1 : "~/Projects/pathto/directory1"
property examplePath2 : "~/Projects/pathto/directory2"
property examplePath3 : "~/Projects/pathto/directory3"
property pathList : {examplePath1, examplePath2, examplePath3}
 
on run_iterm()
   tell application "iTerm"
       set newWindow to current window
       -- if you want to open repositories in new window
       -- uncomment the line below and comment linne above
       -- set newWindow to (create window with default profile)
       tell current session of newWindow
           repeat with path in pathList
               tell newWindow
                   create tab with default profile
               end tell
               write text "cd " & path
           end repeat
           delay 0.6
           select first tab of newWindow
       end tell
   end tell
end run_iterm
 
if application "iTerm" is running then
   run_iterm()
else
   activate application "iTerm"
   run_iterm()
end if
```

I simply use it with a shell alias:

```bash
alias workinit="osascript ~/dotfiles/AppleScripts/work_init.scpt"
```

I discovered AppleScript very recently. I will be writing a lot more of them and automate the stuff.

#### Homebrew and Homebrew Cask

Homebrew is very essential for machines running macOS. It helps you install packages and tools in an easy way through the CLI. Homebrew can also help install your apps. You won't need to manually download and install packages and tools anymore. [Homebrew Cask](https://github.com/Homebrew/homebrew-cask) has the power to install GUI applications in macOS from the command line.

```bash
brew cask install iterm2
brew cask install charles
brew cask install firefox
brew cask install google-chrome
brew cask install spotify
```

Homebrew does not support downloading applications from the App Store. To install App Store apps using CLI, you can use a tool called [mas](https://github.com/mas-cli/mas).

My [`brew/`](https://github.com/jogendra/dotfiles/tree/master/brew) contains an [install script](https://raw.githubusercontent.com/jogendra/dotfiles/master/brew/install.sh) that firstly checks whether homebrew is already installed or not. If not, download it otherwise get to next part. Next, I have listed some essential brews and casks that I use in my day to day dev life, which will be installed. And at last, it runs update and up-gradation following by cleanup.

```bash
#!/usr/bin/env bash

# Abort on error
set -e

echo "Checking if Homebrew is already installed..."; 

# Checks if Homebrew is installed
if test ! $(which brew); then
  echo "Installing Homebrew...";
  yes | /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
else
  echo "Homebrew is already installed...";
fi

# Install the essential brews
brew install carthage
brew install cocoapods
brew install gettext
brew install gh
brew install git
brew install node
brew install lazygit
brew install lsd
brew install tree
brew install yarn
brew install zsh
brew install zsh-completions
brew install neovim
brew install pass
brew install vim

# Install essential casks
brew cask install iterm2
brew cask install charles
brew cask install firefox

# Update and Upgrade
echo "Updating and upgrading Homebrew..."; echo;
yes | brew update
yes | brew upgrade

# Remove outdated versions from the cellar
brew cleanup
```

#### Git Configurations

My [`git/`](https://github.com/jogendra/dotfiles/tree/master/git) contains [`.gitconfig`](https://github.com/jogendra/dotfiles/blob/master/git/gitconfig) and some bash scripts related to git. I hate the idea of creating short aliases for every single basic git commands like `ga` for `git add .`, `gc` for `git commit` etc. They create confusion instead of saving time. I use git aliases for little longer git commands or git processes that involve more than two series of commands. I simply put them in a bash file and use them as aliases. You can simply use them according to your convenience.
I have **hardlinked** my dotfiles `.gitconfig` with my system’s global `.gitconfig`. They are **hardlinked** so that I can use the same in my other systems.

A **symbolic (symlink)** or **soft link** is an actual link to the original file, whereas a **hard link** is a mirror copy of the original file. If you delete the original file, the soft link has no value, because it points to a non-existent file, symlinked file will be useless. But in the case of hard link, it is entirely opposite. Even if you delete the original file, the hard link will still has the data of the original file. Because hard link acts as a mirror copy of the original file. In other words, indode number for soft linked files are different (point to different inode) but on the other hand inode number for hard linked files are same (point to same inode). In unix-like systems, **Inode** is a data-structure that represents a file or a directory. An **Inode number** is a unique number given to an inode.

You can simply create a hard link by running:

```bash
ln ~/.gitconfig gitconfig
```

OR if you want to create a soft link instead of a hard link, just use `-s` option.

```bash
ln -s ~/.gitconfig gitconfig
```

You can do the same for other git configuration files like `.gitignore`, `.gitattributes` etc.

Please be careful before putting any sensitive data into git configs files that you are going to put public on GitHub. You can refer to _**Securing the dotfiles**_ section of this blog.

My `.gitconfig` look like this:

```bash
[user]
    name = jogendra
    email = jogendrafx@gmail.com

[mergetool]
    keepBackup = false

[alias]
    parent = rev-parse --abbrev-ref --symbolic-full-name @{u}
    last = log -1 HEAD
    unstage = reset HEAD --
    pr = !sh ~/dotfiles/git/pull_request.sh
    remotes = remote -v
    syncu = !sh ~/dotfiles/git/sync_with_upstream.sh
    diffall = !sh ~/dotfiles/git/git_unpushed.sh
    plog = !sh ~/dotfiles/git/plog.sh
    lazy = !lazygit
    contributors = shortlog --summary --numbered

[color]
    diff = auto
    status = auto
    branch = auto
    ui = true

[commit]
    gpgsign = false

[core]
    editor = vim
    excludesfile = ~/.gitignore
```

### Interesting/Helpful Reads

- [Configuring your login sessions with dotfiles](http://mywiki.wooledge.org/DotFiles)
- [Zsh/Bash startup files loading order](https://shreevatsa.wordpress.com/2008/03/30/zshbash-startup-files-loading-order-bashrc-zshrc-etc/)
- [Why do hidden files in Unix begin with a dot?](https://www.reddit.com/r/linux/comments/at05xh/why_do_hidden_files_in_unix_begin_with_a_dot/egyj6lr/)
- [Dotfiles madness](https://0x46.net/thoughts/2019/02/01/dotfile-madness/)
- [What a shell dotfile can do for you](https://opensource.com/article/18/9/shell-dotfile)
- [Keep sensitive data encrypted in dotfiles](https://www.outcoldman.com/en/archive/2015/09/17/keep-sensitive-data-encrypted-in-dotfiles/)
- [Securing Information in dotfiles with Password-Store (pass)](https://chrisschuld.com/2019/06/securing-information-in-dotfiles-and-aliases-with-pass/)

I hope this post will give you enough push to start with your own dotfiles. Looking forword to your freshly brewed dotfiles. You can reach out to me on [Twitter](https://twitter.com/jogendrafx) in case of anything. Thanks for the read :)
