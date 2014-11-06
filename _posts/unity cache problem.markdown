---
author:
  name: 洪鲜
  email: xandy@qifun.com
  github: zjhongxian
  bio: 岂凡 软件工程师
  email_md5: 90d3030569006c7583808c7914862955
  url: 
layout: post
title: "unity cache问题"
---

使用WWW.LoadFromCacheOrDownload接口，记得要在程序开始运行时使用Caching.CleanCache把缓存清除一次。否则总是会load到老版本的资源
