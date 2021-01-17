---
layout: post
title: I do Dotfiles!
description: A detailed post about dotfiles in general and my dotfiles
comments: true
keywords: dotfiles, git, zsh, version control, vcs, open source, bash, vim, iterm2, macos, applescript, bashrc, zshrc, xcode
scover: i_do_dotfiles.png
---
One of the main advantages/beauty of Unix-like systems is that configurations of everything are very customizable. Dotfiles are simply amazing. They are tiny-little configurations files but they customize/decide a lot of your system. Well-organized and understandable dotfiles basically allow you to automatize complex tasks that you repeat everyday. With dotfiles, your machine is more organized, advanced, and customized in the way you like your system to be. Also with dotfiles, you can mess around with new packages and settings quite easily, without ever having to worry about really breaking something important. If you would break something, your dotfiles are always right there to back you up.

> **TL;DR**: Invest time learning to configure your machine and automate processes, you'll get that time back tenfold.

> Your experience, knowledge and talent are key to success, but some kick-ass dotfiles speed you up.

#### :rocket: :sparkles: _My dotfiles can be found at [**jogendra/dotfiles**](https://github.com/jogendra/dotfiles)_

### What are dotfiles?

Dotfiles are files and folders on Unix-like systems starting with `.` (dot) that control the configuration of applications and shells on your system. Dotfiles are shell scripts that are executed to change the environment of your machine. The **“dotfiles”** name is derived from the configuration files in Unix-like systems that start with a dot (e.g. _.zshrc_ and _.gitconfig_). For normal users, this indicates these are not regular documents, and by default are hidden in directory listings. You can display dotfiles inside any directory by running `ls -a` command.  [GitHub does dotfiles](https://dotfiles.github.io/) is definitely great place to explore and know more about dotfiles.

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

`git config credential.helper store` is not a very secure way to store your git server passwords. According to [git credential store documentations](https://git-scm.com/docs/git-credential-store), _store_ helper will store your passwords unencrypted on disk, protected only by filesystem permissions. The `~/.git-credentials` file will have its filesystem permissions set to prevent other users on the system from reading it, but will not be encrypted or otherwise protected. So it stores your password as is. So if you are running _macOS_ on your machine, Git allows to use your _keychain_, which is way more secure. To set `osxkeychain` as your git credential helper, run:

```bash
git config --global credential.helper osxkeychain
```

If you are runnning _Linux_ on your machine, you can use [`git config credential.helper cache`](https://git-scm.com/docs/git-credential-cache), which stores passwords in your memory. This way stored credentials never touch the disk, and are forgotten after a configurable timeout. To set `cache` as your git credential helper, run:

```bash
git config --global credential.helper cache
```

Mistakenly, if you commit sensitive data, such as a password or SSH key into dotfiles Git repository, you can remove it from the history. To entirely remove unwanted files from a repository's history you can use either the [`git filter-branch`](https://git-scm.com/docs/git-filter-branch) command or the [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) (an alternative to `git filter-branch`) open source tool. You can check out GitHub's guide on [Removing sensitive data from a repository](https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository).

I would recommend you to use [Passwordstore](https://www.passwordstore.org/) (pass), the standard Unix password manager. It provides a secure way to encrypt, share, manage, and use private data or passwords. **pass** makes managing these individual password files extremely easy. All passwords live in `~/.password-store`, and `pass` provides some nice commands for adding, editing, generating, and retrieving passwords. It is a very short and simple shell script. It's capable of temporarily putting passwords on your clipboard and tracking password changes using `git`. You can [read more about pass here](https://git.zx2c4.com/password-store/about/).

Do checkout [Keep sensitive data encrypted in dotfiles](https://www.outcoldman.com/en/archive/2015/09/17/keep-sensitive-data-encrypted-in-dotfiles/). Also, check out [how to secure your dotfiles](https://abdullah.today/encrypted-dotfiles/).

### Using dotfiles through SSH

This section is for those who login often on remote servers using SSH. When you enter into the remote server, your ZSH, plugins, all of your aliases, completions, color schemes, _**NOTHING** is there_. Everything set to their default values because your remote shell does not have access back to your local configurations. Terrible?. The solution to this, what I would recommend is to write a script that contains everything you want to put on your remote machine and install there whenever you log in. Keep this script separate from your dotfile installation script because you may not want/need to install everything that is inside your dotfiles repository. Also, because you may not want to slow down your remote server login process. You can also use this SSH wrapper tool called [**kyrat**](https://github.com/fsquillace/kyrat) that allows you to source local dotfiles on an SSH session to a remote host. It works either from/to a _Linux_ or _OSX_ machine.

<hr>

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

<hr>

### The Components

#### AppleScripts

AppleScript is a scripting language developed by Apple to help people automate their work processes on the MacOS. AppleScript is an extremely simple, almost English-like language, but automation it does is pure gold. I sometimes write AppleScripts to automate stuff on my mac where it involves a lot of clicks or similar and I use them using shell aliases.
Here is a simple AppleScript that I wrote to open _iTerm2_ tabs with right directory for all work-related projects.

```applescript
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
	
[credential]
	helper = osxkeychain
```

#### ZSH and OH-MY-ZSH

Command-line is arguably one of the most important tools for any power user, so configuring it in the right way is really important. I prefer Z Shell over any other shell. I am using Z Shell from last almost 3 years now and did not found a reason to use another shell. I used to use Bash before that. Zsh is a powerful shell that features some great improvements over Bash like autocompletion, shared command history, themeable prompts, and a lot more. From the customizations perspective, no one can replace ZSH. There are many ZSH configuration frameworks available, [oh-my-zsh](https://ohmyz.sh/) is the most used/popular one. [ZSH is also been made default shell for macOS from Catalina](https://support.apple.com/en-ca/HT208050). For writing scripts, I prefer Bash over Zsh. You can get more insight into [practical differences between Bash and Zsh](https://apple.stackexchange.com/a/361957/266414).
Some people also prefer using [Fish](https://fishshell.com/) shell over Bash and Zsh.

[**oh-my-zsh**](https://ohmyz.sh/): Oh-my-zsh is simply amazing. Without it, it is difficult to customize/configure  ZSH. _Oh-My-Zsh_ is wildly adopted, has a strong community, and a great range of features. I use _Oh-My-Zsh_ with [**Powerlevel10k**](https://github.com/romkatv/powerlevel10k) theme. My favorite thing about _Powerlevel10k_ is its [Powerlevel9k](https://github.com/Powerlevel9k/powerlevel9k) compatibility. I use a lot of _Powerlevel9k_ customizations on _Powerlevel10k_ and _source_ them in `zshrc`. The [minimal theme](https://github.com/subnixr/minimal) is also nice to use.

My [`zsh/`](https://github.com/jogendra/dotfiles/tree/master/zsh) contains `zshrc` which I kept very clean by putting similar thighs into different files eg. all aliases into `aliases` file, configurations in `config` file, powerlevel9k customizations in `powerlevel9k` file and so on, and just **sourced** them into `zshrc`. Neat. Dotfiles `zshrc` is _hardlinked_ with system's `zshrc`.

```bash
# Load: Configurations
source $HOME/dotfiles/zsh/config

# Load: Plugins
source $HOME/dotfiles/zsh/plugins

# Load: OH-MY-ZSH
source $ZSH/oh-my-zsh.sh

# Load: POWERLINE
source $HOME/dotfiles/zsh/powerlevel9k

# Load: ALIASES
source $HOME/dotfiles/zsh/aliases
```

Looks very simple and clean? But with **`source`** command, it imports and executes a lot of stuff in it and they are loaded into your shell environment before you use them. `zshrc` is loaded and executed before you start your terminal. **`source`** is a shell built-in command which is used to read and execute the content of a file, passed as an argument in the current shell script.

#### macOS Preferences

A lot of macOS settings can directly be set from the command line. Preference and configuration files in macOS use property lists (_plists_) to specify the attributes, or properties, of an app or process. macOS comes with a [`defaults` command-line interface](https://support.apple.com/en-in/guide/terminal/apda49a1bb2-577e-4721-8f25-ffc0836f6997/mac) that lets you read, write, and delete macOS user defaults.

Mathias Bynens [dotfiles repository](https://github.com/mathiasbynens/dotfiles/blob/master/.macos) has a lot of macOS defaults listed, I just picked some of the defaults that I found useful. Here’s how it look like some of them:

```bash
# Free the Dock
defaults write com.apple.Dock size-immutable -bool no; killall Dock

# Remove the auto-hiding Dock delay
defaults write com.apple.Dock autohide-delay -float 0

# Remove the animation when hiding/showing the Dock
defaults write com.apple.Dock autohide-time-modifier -float 0

# Set the icon size of Dock items
defaults write com.apple.Dock tilesize -int 28

# Disable press-and-hold for keys in favor of key repeat
defaults write NSGlobalDomain ApplePressAndHoldEnabled -bool false
```

To apply the macOS defaults, you can simply run:

```bash
Source ~/.dotfiles/macos/settings
```

Similarly, I have some of the [defaults](https://github.com/jogendra/dotfiles/blob/master/xcode/defaults) specific to Xcode only are inside [`xcode/`](https://github.com/jogendra/dotfiles/tree/master/xcode).

#### iTerm Configurations

> iTerm2 + Zsh + Oh-my-zsh = :sparkling_heart:

My [`iTerm2/`](https://github.com/jogendra/dotfiles/tree/master/iTerm2) contains [`Default.json`](https://github.com/jogendra/dotfiles/blob/master/iTerm2/Default.json) which is configurations for _iTerm2's_ `Default` profile that includes _color schemes_, _Normal Font_ (I use _JetBrainsMono_), _Non Ascii Font_ (I use _MesloLGSNer_) and lot more. A profile can be imported or can be saved as JSON from **_iTerm2 > Preferences > Selecting Profile name > Clicking Other Actions_**.

#### Installation Script

To configure another system of your with your remote dotfiles, you need an _installation script_ that does all the installations and syncing process for you.

Make sure to check out other people’s scripts for more ideas and inspiration. You can refer to these [general-purpose dotfiles utilities](https://dotfiles.github.io/utilities/) that will help you with managing, syncing, and/or installing your dotfiles. Be careful about your installations script, there may already be configurations file like `.zshrc`, `.gitconfig` etc. and you may not want to lose/replace.

My dotfiles follow topological structure. Any file ending in `*.symlink` gets symlinked into `$HOME`. These get _symlinked_ on running `installers/bootstrap`. Any file named `install.sh` is executed on running `installers/install`. To avoid being loaded automatically, its extension is `.sh`, not `.zsh`. [`dot`](https://github.com/jogendra/dotfiles/blob/master/installers/dot) is a simple script that installs some dependencies, sets macOS defaults, sets Xcode defaults and so on. Tweak this script, and occasionally run dot from time to time to keep the environment fresh and up-to-date.

I will soon be adding **vscode** and **vim** configurations.

### Some Interesting/Helpful Reads

- [GitHub ❤ ~/](https://dotfiles.github.io/)
- [Configuring your login sessions with dotfiles](http://mywiki.wooledge.org/DotFiles)
- [Zsh/Bash startup files loading order](https://shreevatsa.wordpress.com/2008/03/30/zshbash-startup-files-loading-order-bashrc-zshrc-etc/)
- [Why do hidden files in Unix begin with a dot?](https://www.reddit.com/r/linux/comments/at05xh/why_do_hidden_files_in_unix_begin_with_a_dot/egyj6lr/)
- [Dotfiles madness](https://0x46.net/thoughts/2019/02/01/dotfile-madness/)
- [What a shell dotfile can do for you](https://opensource.com/article/18/9/shell-dotfile)
- [Keep sensitive data encrypted in dotfiles](https://www.outcoldman.com/en/archive/2015/09/17/keep-sensitive-data-encrypted-in-dotfiles/)
- [Securing Information in dotfiles with Password-Store (pass)](https://chrisschuld.com/2019/06/securing-information-in-dotfiles-and-aliases-with-pass/)

I hope this post will give you enough push to start with your own dotfiles. Looking forword to your freshly brewed dotfiles. You can reach out to me on [LinkedIn](https://www.linkedin.com/in/jogendrasingh24/) in case of anything. Thanks for the read :) Please take care of security of your system (or remote system) and your mental health :sparkles: :sparkles:
