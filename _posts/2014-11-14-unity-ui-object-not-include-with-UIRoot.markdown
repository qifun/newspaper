---
author:
  name: 洪鲜
  email: xandy@qifun.com
  github: zjhongxian
  bio: 岂凡 软件工程师
  email_md5: 90d3030569006c7583808c7914862955
  url: 
layout: post
title: "unity中ngui控件不在UIRoot下面的时候要注意的问题"
---

  UIPanel负责创建实际的集合图形（意味着不同的UIPanel，渲染批次肯定是分开了）。你不需要手动的添加UIPanel-一旦你创建一个控件，
它会自动被添加(UIRoot里面自动带了一个）。当创建一个ui控件，它不是挂在UIRoot下面的时候(一般用法都是把它挂在UIRoot下面，但
是不排除例外)，就要注意它自动创建的UIPanel和UIRoot上面的UIPanel之间的depth的冲突（他们的depth值都是默认为0）。
  碰到ui控件不是挂在UIRoot下面的需求，比较好的做法是：手动创建一个GameObject，给它添加UIPanel组件，设置它的depth值让之不跟
UIRoot的UIPanel冲突。
