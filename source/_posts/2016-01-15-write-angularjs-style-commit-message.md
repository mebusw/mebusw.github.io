title: 写出angularjs风格的源代码提交历史
date: 2016-01-15 10:18:05
tags:
    - time code
    - commitizen
categories:
    - agile
    - engineering

---

# 你的项目中是怎么写提交历史的？

不论你是用SVN、GIT或是其他源代码仓库，去看看实际项目中的提交历史吧，你是否能很快地从中找到之前某次改动吗？

看看这张图中的提交历史，眼熟吗？

![](http://res.uperform.cn//bad-commit-message.png)

这还算是不错的呢，还有很多项目的提交历史根本就是大段空白！很多程序员其实并不清楚为什么要写 **提交历史(commit messge)**。

<!--more -->

早年间，提交历史是直接写在源代码第一页的注释里面的，标明何时何人改动了什么内容，便于后人维护的时候来理解、定位、评审、追溯。
后来有了更好用的源代码版本仓库，于是这些内容就需要在每次提交时候写上。历史不能修改，也不能后补，发生了就发生了。
提交历史是给人读的，所以一定要考虑可读性、准确性、完整性，并且通过关键词便于后期查找。每一次提交只做一件事情，是写出好的提交历史的关键。

# Angular.js开源项目如何编写提交历史

`Angular.js`是著名的开源项目，看看人家提交[提交历史规范][AngularJS's commit message convention]。

![](http://res.uperform.cn//good-commit-message.png)

每条提交历史包含 **题头、主体、脚注**三部分，而题头又分为 **类型、范围、主题**三部分。

    < 类型 >(< 范围 >): < 主题 >
    <-空白行->
    < 主体 >
    <-空白行->
    < 脚注 >

## 类型(type)

必须是下列中的一种：
* feat: 新功能(feature)
* fix: 修复缺陷
* docs: 仅文档更新
* style: 不影响代码含义的变更，如空格、格式化、漏掉分号等
* refactor: 重构，并未修复缺陷或添加功能
* perf: 性能(performance)提升
* test: 补充测试用例
* chore: 改进构建过程或辅助工具，例如文档生成工具

## 范围(scope)

指定修改的位置和范围，如包名、类名、函数名等。非必填，但是建议填。

## 主题(subject)

描述变更的内容。
* 使用现在时态的祈使句，如"change"，但不要写"changed"或"changes"。对于中文那就是"修改xxx"而不要写"修改了xxx"
* 英文首字母不要大写
* 结尾不用写句号(.或。)

## 主体(body)

主体的风格与主题部分一致，用于详细描述本次变更的动机，以及与上次提交差异。

## 脚注(footer)
脚注应该包含关于 **破坏性变更** 的信息，以及所关闭的任务ID，如github issue或JIRA-ID。

**破坏性变更** 需要以`BREAKING CHANGE:`作为开头，后面接1个空格或2个空行。


## 回滚(Revert)

如果需要撤销之前的Commit，那么本次Commit Message中必须以`revert：`开头，后面紧跟要撤销那次提交的题头部分，格式不变。主体则必须要记录撤销那次提交版本号。


# 形成团队纪律

每个团队参考这份指南，制定出自己的提交历史规范。

[Commitizen]是2015年4月出现在Github上的开源项目，用于增强git命令行，使人们能方便的写出如angular.js项目般的提交历史，还可以直接将这些提交历史导出为发布说明(change log)。首先安装，

    npm install -g commitizen
    commitizen init cz-conventional-changelog --save --save-exact

然后，在需要`git commit`的地方更换成`git cz`指令即可呼出Commitizen的交互界面，跟着提示做即可。

这个工具只支持GIT，SVN用户自己想办法吧（也可以考虑换用GIT:p）。
其实还可以在工具的commit hook脚本里做文章，加入一些正则表达式来阻止无效的提交历史，把规范和纪律自动化掉。

# 自动生成变更说明

规范了Commit格式之后，发布新版本时，使用Conventional Changelog就能够自动生成Change log

    npm i conventional-changelog-cli --save-dev
    npm run changelog

生成的文档包括以下3个部分：

- New features
- Bug fixes
- Breaking changes(不向上兼容的部分，我们的规范不要求footer，所以这一项不会出现)

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。



当然，各种敏捷实践还是要让大家理解目的和意义，这样才能形成真正的内在驱动力，而不是被迫做事情。


[AngularJS's commit message convention]: https://github.com/angular/angular.js/blob/master/CONTRIBUTING.md#-git-commit-guidelines
[Commitizen]: https://github.com/commitizen/cz-cli

