---
author:
  name: 车雄生
  email: john@qifun.com
  github: chexiongsheng
  bio: 岂凡 软件工程师
  email_md5: 56545cd8ec40dddb94f9aea0ef423dee
  url: 
layout: post
title: "通过sortingOrder控制ngui和spine的显示层"
---
  在项目2的UI里头，头像我们用的是spine骨骼动画，而有些NGUI要求在头像下面（信息框的底纹之类），有些要求在头像上面。
  
  调Z不行，spine和NGUI的shader都关闭了深度纪录（ZWrite Off），调NGUI的Depth也不行，NGUI的Depth只是用来进行内部的排序使用。
  
  经一番研究后，发现sortingOrder是可用的，具体做法把要在上面的NGUI控件放到一个panel，设置较高的SortOrder（实际上就是render的sortingOrder），spine头像的sortingOrder值设置在上下两层的值之间即可。

