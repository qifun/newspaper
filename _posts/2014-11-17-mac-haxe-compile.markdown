---
author:
  name: 车雄生
  email: john@qifun.com
  github: chexiongsheng
  bio: 岂凡 软件工程师
  email_md5: 56545cd8ec40dddb94f9aea0ef423dee
  url: 
layout: post
title: "苹果下编译haxe经验分享"
---

  正常情况下相当简单，依次输入如下命令即可：
  
```bash
sudo brew install ocaml #安装ocaml，haxe是用这语言写的
sudo brew install neko  #安装neko，haxe编译器运行要依赖它
git clone --recursive --depth 1  https://github.com/qifun/haxe.git #git clone我们维护的分支
cd haxe
make
sudo make install
```

  事情不是总那么一帆风顺，上述命令执行到make的时候报错，一番定位后发现是最近ocaml升级，haxe依赖的一些部件缺失导致的。经查阅haxe的持续集成日志，发现最近一次成功的mac版本build用的是ocaml 4.01。
  幸好brew可以安装制定版本的软件。但要先安装点东西使其支持versions命令。
```bash
sudo brew tap homebrew/boneyard  #brew支持versions命令
sudo brew tap homebrew/versions  #安装软件版本信息库
```
  输入`brew versions ocaml`可以看到如下信息：
  
```text
4.02.1   git checkout 03ddcda /usr/local/homebrew/Library/Formula/objective-caml.rb
4.01.0   git checkout 924387b /usr/local/homebrew/Library/Formula/objective-caml.rb
4.00.1   git checkout b04e346 /usr/local/homebrew/Library/Formula/objective-caml.rb
4.00.0   git checkout e2140fd /usr/local/homebrew/Library/Formula/objective-caml.rb
3.12.1   git checkout df16522 /usr/local/homebrew/Library/Formula/objective-caml.rb
3.12.0   git checkout 0476235 /usr/local/homebrew/Library/Formula/objective-caml.rb
3.11.2   git checkout ed51a5b /usr/local/homebrew/Library/Formula/objective-caml.rb
```
  我们要安装的是4.01版本：
```bash
sudo brew uninstall ocaml 
cd `brew --prefix`
sudo git checkout 924387b /usr/local/homebrew/Library/Formula/objective-caml.rb
sudo brew install ocaml
```
  现在我们输入ocaml命令可以看到版本已经回到4.01，然后再次回到haxe源码目录输入`make`，`sudo make install`就ok了。