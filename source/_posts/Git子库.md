---
title: Git子库 
date: 2019-04-29 14:42:18
tags:
    - Git
---

一个中大型项目往往会依赖几个模块，`git`提供了子库的概念。可以将这些子模块存放在不同的仓库中，通过`submodule`或`subtree`实现仓库的嵌套。



### Submodule

`submodule`：子模块的意思，表示将一个版本库作为子库引入到另一个版本库中：

#### 1.引入子库

需要使用如下命令：

```bash
git submodule add 子库地址 保存目录
```

例如：

```
git submodule add git@github.com:AhuntSun/git_child.git mymodule
```



执行上述命令会将地址对应的远程仓库作为子库，保存到当前版本库的`mymodule`目录下：