# BYY版本控制规范

>  本文基于gitflow开发模型制定软件开发过程中的版本控制规范，项目维护者，开发者需按照最新的修订版定义分支，合并推送代码。

* 文档版本：0.0.1
* 更新日期：2018年 8月31日
* 作者：高炼

>  参考资料

[nvie gitflow](https://nvie.com/posts/a-successful-git-branching-model/)

[伯乐在线](http://blog.jobbole.com/76867/)



## 1. 各类分支及其作用

### master 分支（主分支）

master分支作为项目的主分支，分支上不提交任何代码，只在发布的时候打上版本号的Tag，作为发布版的历史记录

### develop 分支（开发分支/集成分支）

开发分支用来集成各种新特性，开发分支通常需要合并特性分支的代码，达到凝聚力量的目的。开发分支上也可以独立进行小功能的开发，或者修复一些与其他特性无关的Bug。

> develop分支上不能急于合并未开发完成，或者在下个版本中不能发布的新特性，如果已经并入了暂时不需要的特性，需要用revert命令拆开

### 测试分支/测试环境分支

专门用于提交测试的分支，测试分支上不需要单独提交任何代码，测试分支上的提交均来自其他分支。测试分支相对灵活，允许任意的reset —hard到任意一个提交，允许合并新特性。

> 测试分支可以任意合并特性分支，包括下个发布版本中不需要的特性

### feature/* 分支（特性分支）

大的功能模块需要在特性分支上单独进行开发，分支命名格式：feature/ + 使用`-`号分割的特性名称英文缩写，例如`feature/upload-bank-info`，特性分支一般从develop上切出，阶段性合并入开发分支或者测试分支。

### release/* 分支（发布分支）

发布分支从开发分支切出，分支命名格式：release/+当前发布的版本号，为下一次版本发布做准备，上线前发现的一些bug的修改，版本号更改可以在该分支上完成，上线后发布分支的内容合到开发分支以及master分支，发布分支的内容合并到master分支之后打上版本号Tag，版本号必须以v或者V开头（eg: release/v1.0.0）

### hotfix/*分支（热修复分支）

热修复分支用于紧急修复线上bug，分支命名格式：hotfix/+使用`-`分隔的bug名称（eg: hotfix/splash-crash）热修复分支从master分支上的版本Tag切出，修复bug之后，首先合并进入develop分支；然后合并进入master分支，并打上新的Tag。

【场景】：v1.0.4的开发过程中，发现线上v1.0.3中有首页刷新闪退的bug

【解决方案】：

```shell
$ git checkout bugfix/home-refresh-crash v1.0.3

# ... 解决bug，并提交

$ git checkout develop
$ git merge bugfix/home-refresh-crash

$ git checkout master
$ git merge bugfix/home-refresh-crash

$ git tag v1.0.3.1
```

