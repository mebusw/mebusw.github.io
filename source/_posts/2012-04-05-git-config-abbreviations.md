title: 好用的Git config 缩写配置
tags:
  - git
  - productivity
categories:
    - agile
    - engineering
---

初次安装运行Git，建议在命令行中运行以下命令，配置缩写和颜色，支持中文文件名显示和提交等。
可以有效提高效率。

``` bash
git config --global --add user.email mebusw@163.com
git config --global --add user.name JackyShen
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.last 'log -1 HEAD'
git config --global color.diff auto
git config --global color.status auto
git config --global color.branch auto
git config --global alias.co checkout
git config --global core.quotepath false
git config --global merge.tool kdiff3
git config --global meregtool.kdiff3.path /usr/bin/kdiff3
git config --global alias.visual !gitk
```

建立忽略文件  `~/.gitignore_global`

```
###################
*.com
*.class
*.dll
*.exe
*.o
*.so
*.pyc

# Packages #
############
# it's better to unpack these files and commit the raw source
# git has its own built in compression methods
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip

# Logs and databases #
######################
*.log
*.sql
*.sqlite

# OS generated files #
######################
.DS_Store*
ehthumbs.db
Icon?
Thumbs.db
.svn
*.bak
```
