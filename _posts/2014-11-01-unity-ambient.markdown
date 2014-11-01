---
author:
  name: goteet
  email: goteet@qifun.com
  github: goteet
  bio: 岂凡 程序员
  email_md5: 07f3c08927ae20a86b03687572bb1c84
layout: post
title: "unity的环境光参数"
---

unity shader uniform param UNITY_LIGHTMODEL_AMBIENT 得不到预期的结果，它的值是真实的项目配置中的1/2，因此，要不就要求美术将材质参数 Kambient的系数放大两倍，要不就在计算环境光的时候做x2操作。
