---
author:
  name: 杨博
  email: pop.atry@gmail.com
  url: "http://www.ac.net.blog.163.com/"
  github: Atry
  bio: 岂凡 游戏架构师
  email_md5: 5b3c5026fee43baef3b15d7fef166a7e
layout: post
title: "Haxe嵌套宏导致的海量错误信息"
---

前段时间 @zjhongxian [抱怨](http://qforce.qifun.com/newspaper/2014/11/01/haxe-build-problem/)，使用[haxe-continuation](https://github.com/Atry/haxe-continuation)的函数中出现拼写错误时，会导致编译器Out of memory.

我昨天写了点测试代码，发现这个问题只在Haxe 3中出现。如果用Haxe 2.10，编译器则可以轻松显示正确的错误信息。

于是我就去读Haxe编译器源码，发现Haxe编译器会重复输出嵌套宏的行号，重复次数随嵌套层数成指数级增长。我们有个长达一百多行的函数用了[haxe-continuation](https://github.com/Atry/haxe-continuation)，大概会生成嵌套100层左右的宏调用，一旦出错，就会试图输出2<sup>100</sup>行错误信息。我们的电脑一共只有8GB（即2<sup>33</sup>字节）内存，要输出这么长的错误信息，太强人所难了。

我已经把解决此bug的[补丁](https://github.com/qifun/haxe/commit/3144cf22032c9a12fcd7f836c7a6fc6eec940568)提交给了Haxe官方分支。



