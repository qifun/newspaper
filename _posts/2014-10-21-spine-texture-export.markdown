---
author:
  name: 洪鲜
  email: xandy@qifun.com
  github: zjhongxian
  bio: 岂凡 软件工程师
  email_md5: 90d3030569006c7583808c7914862955
  url: 
layout: post
title: "Spine的纹理导出问题"
---

发现美术给过来的资源，集合到unity后，发现用Spine的默认材质Spine/Skeleton有毛边问题。对比demo的图片后发现demo的图片（都是png格式）没有白色块，而自己的图片有。

原因是Spine工具导出png图片的时候没有选择premultiplied alpha选项（Export->JSON->Create Altas勾上，有个Setting按钮，点击它后可以设置）。
