---
author:
  name: 方里权
  email: fangliquan@qq.com
  github: chank
  bio: 岂凡 软件工程师
  email_md5: 5851b66b5708deba39cb20fca4adf312
  url: 
layout: post
title: "hxcs生成的dll问题"
---

刚开始用hxcs生成entity项目的dll时，在unity中用会报莫名其妙地报 Internal compiler error 错误。但用mono创建项目生成dll在unity中使用却很正常，估计这是unity中使用的.net版本太低了跟csc用的.net版本不兼容吧

接着想让hxcs用mcs来编译csharp项目，但在path环境变量中加入mcs的路径hxcs依然不用mcs来编译csharp项目。看了hxcs的代码发现它的实现很奇葩，只要csc存在就优先用csc来编译项目。

没办法只能修改hxcs的代码让它能够指定编译器来编译csharp项目。最后还向官方提交了个补丁[https://github.com/HaxeFoundation/hxcs/pull/13)，不知道会不会被接受呢

