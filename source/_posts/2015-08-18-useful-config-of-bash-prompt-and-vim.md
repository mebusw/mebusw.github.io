title: 好用的bash提示符和vim配置
date: 2015-08-18 10:31:54
tags:
  - productivity
  - vi
  - bash

categories:
  - agile
  - tools
---
定制bash命令行的提示信息，用彩色显示，还包括当前git branch等。
另外还有vi编辑器的配置，让电脑更好用。

``` bash
##### ~/.bashrc

alias ls="ls -G"
alias ll="ls -alG"

function parse_git_branch_and_add_brackets {
    git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.    *\)/\ \1\]/'
}

export PS1="\[\e[32;40m\]\u@ \[\e[33;40m\]\w \[\033[0;34m\]\$(parse_gi    t_branch_and_add_brackets) \[\033[0m\]\$ "



##### ~/.vimrc

set nocompatible
set cursorline
colorscheme torte
set number
syntax on

```

