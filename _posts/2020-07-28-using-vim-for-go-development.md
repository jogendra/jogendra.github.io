---
layout: post
title: Using Vim for Go Development
description: Detailed post about using vim for all Go related development with vim-go and other plugins
comments: true
keywords: vim, go, golang, vim-go, nerdtree, coc.nvim, plugins
scover: vim_for_go.png
---

As a programmer, you spend a lot of time using text editors, IDEs, etc to code. So it is important what and how you use it. I recently moved to Vim for Go development and I am totally loving it. Initially, I was using VSCode with Go extension and after that, I used GoLand for a while. [Visual Studio Code](https://github.com/microsoft/vscode) and [GoLand](https://www.jetbrains.com/go/) both are totally awesome, I loved using them too. It definitely takes some time to learn using vim but it is worth the learning. It is totally up to you what works better for you. Do not use vim just because others are using it. I am very much a beginner to vim and things I am going to explain in this is what I learned after I started using it. I would be really glad if you are a long time Vim user and have some good advice/suggestions. You can put them in the comment section of this post. If you are a beginner, I will put some good resources for learning vim at the bottom of this post.

## Why do people use Vim?

- Vim is guaranteed to exist on all Unix systems (basically, it’s available on every major platform). It’s everywhere, and it’s the same on all platforms. Vim supports all file formats and programming languages, no need to download different IDEs for different programming languages.
- Mouse free navigation. Your fingers very rarely have to leave the home row, which means you'll be able to edit text very, very quickly. You can do key mapping according to your convenience. Not having to switch between keyboard and mouse is definitely nice with Vim.
- Vim is very light-weight and fast. Vim can easily handle large files but Visual Studio or other IDE couldn’t handle them smoothly. Try opening a large file (~20MB) with your favorite non-vim text editor and vim, you will definitely get to know the difference. There is great power in the way Vim handles files (buffers), splits  (windows), and layouts (tabs), the power that you can't get in other editors.
- Vim and vim like text editors (emacs, nano, etc) feel and look so native, no distraction. I don't have to switch between the terminal and editor/IDE. It's just simply everything in one place. It's pretty easy to be productive in vim with little effort.
- Vim is very handy when you have to make quick configurational or other changes to the remote machine/server. Even on your own machine, you won’t open a heavy text editor for small change.
- Vim is highly configurable. You can simply use `~/.vimrc` to override default configurations and personalize yours. Vim plugins provide everything to vim that you need in a text editor. Autocomplete, syntax highlighting, or any other feature you name it and it is available, you just need the right plug-in.
- Using vim is fun. It's little challenging to use initially, but it become fun once you get used to it.

## Let's GO!

In this post, we will explore setting up Vim as a full-fledged IDE for Go development. Plugins we are going to use are:

- [vim-go](https://github.com/fatih/vim-go): Go development plugin for Vim. It has everything you need for Go development.
- [NERDTree](https://github.com/preservim/nerdtree) - File system explorer for the Vim. It enables you to visually browse complex directory hierarchies, quickly open files for reading or editing, and perform basic file system operations.

### :electric_plug: [Vim-Go](https://github.com/fatih/vim-go)
Vim-Go is most popular and most used vim plugin for Go development. It offer almost all the features you may need. Some of them are:
- Compile your package with `:GoBuild`, install it with `:GoInstall` or test it with `:GoTest`.
- Quickly execute your current file(s) with `:GoRun`.
- Syntax highlighting and folding.
- Autocompletion support via `gopls`.
- Run `gofmt` and `goimports` on save. Means, you don’t have to worry about importing packages and code formatting, vim-go will do that for you on saving the file.
- Go to symbol/declaration with `:GoDef` and look up documentation with `:GoDoc` or `:GoDocBrowser`.
- Check code coverage, do code linting etc.

You can check all the feature offered by vim-go [here](https://github.com/fatih/vim-go#features).

#### Installing vim-go

Vim 8 added native support for packages which mean you can load plugins by just cloning their contents into your `~/.vim` directory. To install vim-go, you can simple run command:

```bash
git clone https://github.com/fatih/vim-go.git ~/.vim/pack/plugins/start/vim-go
```

You will also need to install all the necessary binaries. vim-go makes it easy to install all of them by providing a command, `:GoInstallBinaries`, which will `go get` all the required binaries.

Or you can put plugin in your `~/.vimrc`.

```bash
call plug#begin()
Plug 'fatih/vim-go', { 'do': ':GoUpdateBinaries' }
call plug#end()
```

Now save the `.vimrc` and install the plugin by running `vim +PlugInstall`. It will install vim-go plugin with all the necessary binaries.

I have added following vim-go related directives to my `.vimrc`, to make it more useful:

```viml
filetype plugin indent on

set autowrite

“ Go syntax highlighting
let g:go_highlight_fields = 1
let g:go_highlight_functions = 1
let g:go_highlight_function_calls = 1
let g:go_highlight_extra_types = 1
let g:go_highlight_operators = 1

“ Auto formatting and importing
let g:go_fmt_autosave = 1
let g:go_fmt_command = "goimports"

" Status line types/signatures
let g:go_auto_type_info = 1

" Run :GoBuild or :GoTestCompile based on the go file
function! s:build_go_files()
  let l:file = expand('%')
  if l:file =~# '^\f\+_test\.go$'
    call go#test#Test(0, 1)
  elseif l:file =~# '^\f\+\.go$'
    call go#cmd#Build(0)
  endif
endfunction

“ Map keys for most used commands.
“ Ex: `\b` for building, `\r` for running and `\b` for running test.
autocmd FileType go nmap <leader>b :<C-u>call <SID>build_go_files()<CR>
autocmd FileType go nmap <leader>r  <Plug>(go-run)
autocmd FileType go nmap <leader>t  <Plug>(go-test)
```

You can visit [official tutorials](https://github.com/fatih/vim-go/wiki/Tutorial) for your own customisations. Complete vim-go documentations are available in your vim editor, you can simply run `:help vim-go` to check them out. This is how my vim look for a Go programm after installing and configuring vim-go:

<img class="fullimg" alt="go program with vim-go" src="https://user-images.githubusercontent.com/20956124/88539500-fd99a780-d02e-11ea-91cf-97ce4c10ccc6.png">

Beautiful?

#### Auto Completion

It’s really hard to code without auto completion and not being able to going to definition. Vim-go can use `gopls` for completion etc. Completion is enabled by default via **omnifunc**. While you are in `INSERT` mode, you can trigger completion with `Ctrl-x Ctrl-p`. If matching names are found, a pop-up menu opens which can be navigated using the `Ctrl-n` and `Ctrl-p` keys. 
To check if vim-go’s `ftplugin/go.vim` has been configured correctly for autocompletion, you can run:

```viml
:verbose setlocal omnifunc?
```

Message below should appear:

```viml
omnifunc=go#complete#Complete
        Last set from ~/.vim/pack/plugins/start/vim-go/ftplugin/go.vim
```

If you want the autocomplete prompt to appear automatically whenever you press the _dot_ (`.`), you can add the following line to your `.vimrc`:

```viml
au filetype go inoremap <buffer> . .<C-x><C-o>
```

Now, typing should offer auto suggestions along with documentation previews. It should look something like this:

<img class="fullimg" alt="coc autocompletion screenshot" src="https://user-images.githubusercontent.com/20956124/88567355-feddcb00-d054-11ea-9ea1-d556d1e13e1c.png">

Apart from vim-go completion, there some good plugins which you can use for auto completion and other LSP features. Some of them are:
- [coc.nvim (Conquer of Completion)](https://github.com/neoclide/coc.nvim): Intellisense engine for Vim8 & Neovim, full language server protocol support as VSCode.
- [LanguageClient-neovim](https://github.com/autozimu/LanguageClient-neovim): Minimal Language Server Protocol client written in Rust.
- [YouCompleteMe](https://github.com/ycm-core/YouCompleteMe): Code-completion engine. It has several completion engines.

#### Language Server Protocol (LSP) and gopls

The Language Server protocol is used between a tool (the client) and a language smartness provider (the server) to integrate features like auto complete, go to definition, find all references and alike into the tool. The goal of the protocol is to allow programming language support to be implemented and distributed independently of any given editor or IDE. The Language Server Protocol was originally developed for Microsoft's Visual Studio Code and is now an open standard. Microsoft collaborated with Red Hat and Codenvy to standardise the protocol's specification.

Before LSP, there used to be different IDEs for different languages (still there are many). Every IDEs had language specific integration (syntax highliting, auto completion, going to defination, etc features). The situation is often referred to as an M × N problem, where the number of integrations is the product of M editors and N languages. What the Language Server Protocol does is change this M × N problem into a M + N problem. Best things about LSP is, it is totally independent of IDEs. As a result we can see that, Visual Studio work for so many different languages via extensions. These extensions make use of LSP.

You can read more about LSP [here](https://microsoft.github.io/language-server-protocol/) and [her](https://docs.microsoft.com/en-us/visualstudio/extensibility/language-server-protocol?view=vs-2019) or watch this [YouTube video](https://www.youtube.com/watch?v=2GqpdfIAhz8) to see how it works.

Official Language Server for Golang is [**gopls**](https://github.com/golang/tools/blob/master/gopls/README.md) (pronounced: _”go please”_). You can check more information about the [status of gopls and its supported features](https://github.com/golang/tools/blob/master/gopls/doc/status.md).

### :electric_plug: [NERDTree](https://github.com/preservim/nerdtree)

After setting up a lot of stuffs in your Vim, one thing you probably still missing the most is _“Side Window”_ where you can browse your project files easily. NERDTree got you there. NERDTree is a fully fledged file system explorer, which helps to visually browse your files using a tree-style side window. With NERDTree, you can quickly open files for reading or editing, and perform basic file system operations.


#### Installation and Setups

You can install NERDTree same as vim-go and coc.nvim. Simply add `Plug 'preservim/nerdtree’` to `.vimrc` and run `:PlugInstall` or run `vim +PlugInstall` from command line.

Further, you can checkout [wiki FAQs](https://github.com/preservim/nerdtree/wiki/F.A.Q.) for setups. My setups for NERDTree is similar to:

```viml
map <C-z> :NERDTreeToggle<CR> “ Toggle side window with `CTRL+z`.
let g:NERDTreeDirArrowExpandable = '▸'
let g:NERDTreeDirArrowCollapsible = '▾'
let NERDTreeShowHidden=1 “ Show hidden files
```

You can open a directory into vim using `vim .` (similar to `code .` for VSCode). You can switch between NERDTree and your code by using `CTRL+ww` or `CTRL+w+ArrowKey`. NERDTree has built-in documentations, you can check them by pressing `?` when you are in NERDTree window.

This is how my Vim setup look like with NERDTree:

<img class="fullimg" alt="NERDTree screenshot of Vim" src="https://user-images.githubusercontent.com/20956124/88571617-90503b80-d05b-11ea-9d57-e21714f66636.png">

You can have a look at my `.vimrc` and vim scripts on GitHub at [**jogendra/dotfiles/vim**](https://github.com/jogendra/dotfiles/tree/master/vim).

### Helpful Links

Here are some links that will help you get started with Vim:

- [OpenVim - Interactive Vim Tutorials](https://www.openvim.com/)
- [Best Vim Tips by Fandom](https://vim.fandom.com/wiki/Best_Vim_Tips)
- [Why, oh WHY, do those nutheads use vi?](http://www.viemu.com/a-why-vi-vim.html)
- [Collection of Vim articles by thoughtbot](https://thoughtbot.com/blog/tags/vim)
- [spf13-vim - The Ultimate Vim Distribution](http://vim.spf13.com/)
- [Vim Cheatsheet](https://vim.rtorr.com/)
- [Vim Tab Madness. Buffers vs Tabs](https://joshldavis.com/2014/04/05/vim-tab-madness-buffers-vs-tabs/)

You can simply run _vimtutor_ in your command line by running `vimtutor` command. It will open up Vim tutorials and help you getting started with it.


### Bottom Lines

Using vim is fun. Initially you may face some problem using it and you may have to learn how to use it but it is definitely worth learning it. Atleast you can continue learning vim along side using a IDE. Start with the minimum and build a little on it each day. There are a lot of plugins available for everything you need with Vim. Using vim for Golang development is super nice with vim-go plugin. Isn't it nice to having everything at one place and being more productive?

Thanks for reading. Do share with your colleagues/friends. You can reach out to me on [Twitter](https://twitter.com/jogendrafx). 
