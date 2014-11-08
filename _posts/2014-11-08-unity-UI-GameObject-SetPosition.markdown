---
author:
  name: 洪鲜
  email: xandy@qifun.com
  github: zjhongxian
  bio: 岂凡 软件工程师
  email_md5: 90d3030569006c7583808c7914862955
  url: 
layout: post
title: "unity的UI GameObject设置位置要注意的问题"
---

对于一个新生成的UI GameObject，设置它的位置前，记得先把它的transform.parent和transform.localScale初始化好，否则会导致position值
有意想不到的结果。
