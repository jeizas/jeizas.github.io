---
layout: post
title: "git常用命令总结"
date: 2017-09-15 00:05
categories: [linux]
tags: linux
---

### git常用命令总结

> git这货经常被用，每每出现问题去上网查询时候，总是费时费力。
### 一、简要说明

![](../media/img/20170915/git.png)

### 二、常用命令

![](../media/img/20170915/git_command.png)

### 三、补充命令

> 除了上图中列出的一些日常经常用到的命令之外，还有些关键时候好用的命令也值得在特定的场合使用。

#### 3.1 本地工作取

{% highlight java linenos %}
//显示暂存区和工作区的差异
git diff
//本次未提交的代码保到本地缓冲区，代码保存到上次的提交
git stash
//将本地缓存区的代码取到工作空间
git stash pop
//本地提交之后回滚（将删除改节点之后工作空间变更的任何代码）
git reset --hard [hash]
//修改之后没有commit到本地代码库
git checkout [filename]
//补增提交
git commit -C head -a --amend
//查看特定文件的历史信息
git blame
//拉取远程分支a，将远程和本地的快照合并，如果冲突产生提交
git pull [branch-a] == git fetch + git merge
//拉取远程分支a，在当前分支基础上把远程交叉节点之后的提交依次转移到当前分支（提交曲线更平滑）
git pull --rebase [branch-a] == git fetch + git rebase
{% endhighlight java%}
	
#### 3.2 本地git库

{% highlight java linenos %}
//更新本地git库
git fetch
//更新本地git库，删除本地git库在远程已经删除的分支
git fetch -p
{% endhighlight java %}
	
#### 3.3 设置忽略文件

> 当多人协作的时候，一些自己个性化的本地配置或者IDE产生的一些不必好的配置文件等，我们不希望提交到代码库中影响他人的使用或影响代码库的整洁，这个时候就需要一个东西来描述那些不可提交的文件列表，因此git官方给出了解决方法，通过添加.gitignore文件，来编写一定的规则去忽略那些不可提交的文件。

##### 3.3.1 语法
- 以#开头的行为注释行
- 以 “/” 结尾表示次目录将被忽略
- 以“*” 通配多个字符
- 以“?” 通配单个字符
- 以方括号 “[]” 包含单个字符的匹配列表
- 以叹号 “!” 表示不忽略(跟踪)匹配到的文件或目录
- 可以在前面添加斜杠 “/” 来避免递归,下面的例子中可以很明白的看出来与下一条的区别

##### 3.3.2 配置实例

{% highlight java linenos %}
//忽略 .a 文件
*.a

//但否定忽略 lib.a, 尽管已经在前面忽略了 .a 文件
!lib.a

//仅在当前目录下忽略 TODO 文件， 但不包括子目录下的 subdir/TODO
/TODO

//忽略 build/ 文件夹下的所有文件
build/

//忽略 doc/notes.txt, 不包括 doc/server/arch.txt
doc/*.txt

//忽略所有的 .pdf 文件 在 doc/ directory 下的
doc/**/*.pdf
{% endhighlight java %}

##### 3.3.3 配置文件模板

[.gitignore配置文件地址](https://github.com/github/gitignore)

##### 3.3.4 修改之后

如果某些文件已经被提交到了版本管理中，则修改.gitignore是无效的，那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交

```shell
git rm -r --cached .
git add .
git commit -am "commit msg"
```

### 友情链接

> [**git常用命令|咖啡猫**](https://cafed.github.io/2017/05/13/git-cmd/)

> [**常用 Git 命令清单**](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

> 本篇持续更新