---
author:
  name: 杨博
  email: pop.atry@gmail.com
  url: "http://www.ac.net.blog.163.com/"
  github: Atry
  bio: 岂凡 游戏架构师
  email_md5: 5b3c5026fee43baef3b15d7fef166a7e
layout: post
title: "<code>haxe-continuation</code>的新功能"
---

今天为<a href='https://github.com/Atry/haxe-continuation'>haxe-continuation</a>增加了几项新功能：

 * 支持array comprehension功能
 * 支持用`for`循环遍历`haxe.ds.Vector`;
 * 增加了`com.dongxiguo.continuation.utils.Generator.toEnumerator`，可以把异步函数转成C#的`IEnumerator`，方便配合Unity的Coroutine使用。

我今天白天要各位升级到haxe-continuation 1.2.2了。但不好意思，可能明天你们还得再升级一次升到1.3.0。
