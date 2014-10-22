---
author:
  name: 杨博
  email: pop.atry@gmail.com
  url: "http://www.ac.net.blog.163.com/"
  github: Atry
  bio: 岂凡 游戏架构师
  email_md5: 5b3c5026fee43baef3b15d7fef166a7e
layout: post
title: "你已经忙得没空改进代码了吗？"
---

分享一篇文章：[Goodbye munit](https://proletariat.com/2014/09/24/goodbye-munit/)。

该文总结了[World Zombination](http://worldzombination.com/)开发过程中测试框架的变更历程。他们的独特经验在于利用Headless Browser对客户端进行测试。

World Zombination和我们一样，严重依赖异步操作，所以他们使用了我开发的[haxe-continuation](https://github.com/Atry/haxe-continuation)。

我们sun项目的动作控制可能也会基于haxe-continuation来编写异步过程。类似于他们提及的Headless Browser，我们则可能需要基于Unity的运行时对象模型编写测试用例。

他们的最后一条经验是：

![你已经忙得没空改进代码了吗？]({{ site.baseurl }}/assets/media/are-you-too-busy-to-improve2.png)