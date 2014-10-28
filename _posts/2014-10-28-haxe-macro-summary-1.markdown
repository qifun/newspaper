---
author:
  name: 张修羽
  email: xiuyu@qifun.com
  github: zxiy
  bio: 岂凡 软件工程师
  email_md5: 17cf991f2c20a4b56f5c17c96d0826a3
layout: post
title: "Haxe宏总结(一)"
---

# Haxe宏总结

这段时间使用了很多Haxe的宏，深感其功能强大，但因官方文档晦涩，缺乏中文资料，所以写了这样一篇总结，错误之处，敬请斧正。

## 什么是Haxe的宏(macro)？

大家都知道在C/C++中有一个`#define`语句，被称为宏定义，其作用是在预编译时对源代码做字符串的替换。

而在Haxe中，宏也是编译期做的一些处理，其中就包括了替换，但替换的并不是字符串，而是表达式。

由于Haxe是由Ocaml语言编写的，因此也保留了Ocaml一些函数式语言的特性，比如，不存在所谓的控制结构，一切都是表达式。比如，`if(a) b else c`是`if(a,b,c)`表达式，`switch(c0) case c1:a; case c2,c3:b; default:d;`是`switch(c0,[{[c1],a},{[c2,c3],b}],d)`表达式，至于函数本身这种东西，那就更是表达式了。

所以我们就可以使用表达式的数据结构来描述代码了。

更进一步，我们可以在编译时获得定义的什么类啦、函数啦、变量声明啦、数据的处理步骤的表达式，在对其进行修改后，就可以生成新的修改后的代码。甚至根据需求完全凭空生成新的代码。

再结合Haxe的直接编译成目标源代码的特性，我们就可以生成java、c++、c#、js、as、python、php等目标代码，于是，这，就成了Haxe的最强杀手级特性。

## 从最简单的例子讲起

这篇文章的例子以Haxe官方文档上的内容为主，因为Haxe官方文档上没有提供生成代码的样子，所以理解起来比较晦涩，所以我会在本文中提供Java目标代码并简单加以解释。由于生成代码可读性差，所以我会把生成代码略加整理，但不会破坏其原本的结构、顺序。

第一个例子

以下为源代码
``` haxe
import haxe.macro.Expr;

class Main {
  static public function main() {
    var x = 0;
    var b = add(x++);
    trace(x); // 2
  }

  macro static function add(e:Expr) {
    return macro $e + $e;
  }
}
```
来看一下生成的Java代码吧。

``` java
    public static   void main()
    {
		int x = 0;
		int b = ( x++ + x++ );
		//Java目标代码中的trace()太长了，不写出来了
    }
```

正如这个例子说明的这样，带`macro`关键字的函数add，返回的并不是函数值，而是表达式。表达式在编译时被替换成了其对应的代码，就有了下面的Java代码。

而在Haxe源代码中，`macro`语句中`$`符号代表这个变量当作表达式来拼接到整个表达式，而不是把这个变量本身放入表达式。表达式会在编译时被替换，而变量不会。

关于怎样把各种类型的变量转换成表达式，可以看这里[这里](http://haxe.org/manual/macro-reification-expression.html)。

而这个例子的官方文档链接在[这里](http://haxe.org/manual/macro-arguments.html)。

## macro语句的运行时机

那就来看下一个例子

``` haxe
class Main {
  static public function main() {
    const("foo", 1, 1.5, true);
  }

  macro static function
  const(s:String, i:Int, f:Float, b:Bool) {
    trace(s);
    trace(i);
    trace(f);
    trace(b);
    return macro null;
  }
}
```
生成的java代码:

``` java
    public static   void main()
	{
		java.lang.Object __temp_expr26 = null;
	}
```
Nani!?什么都没有？不对，这里有一个`null`，正是`macro`函数所返回的。再看下刚才执行编译命令的控制台：

``` cmd
>haxe -main Main -java target
Main.hx:8: foo
Main.hx:9: 1
Main.hx:10: 1.5
Main.hx:11: true
haxelib run hxjava hxjava_build.txt --haxe-version 3200
javac.exe "-sourcepath" "src" "-d" "obj" "-g:none" "@cmd"

>
```
这下明白了。原来输出的内容都在编译时输出了，而生成的代码里面，是不包含macro函数内的中间过程的。

因为，macro关键字表达式内的代码，都是在编译期执行的。

这个例子的官方文档链接在[这里](http://haxe.org/manual/macro-constant-arguments.html)。

## 开始真正的生成一段类的代码

我们还是先从最简单的开始，这仍是一个来自与官方文档的例子：

``` haxe
class Main {
  macro static function
  generateClass(funcName:String) {
    var c = macro class MyClass {
      public function new() { }
      public function $funcName() {
        trace($v{funcName} + " was called");
      }
    }
    haxe.macro.Context.defineType(c);
    return macro new MyClass();
  }

    public static function main() {
    var c = generateClass("myFunc");
    c.myFunc();
    }
}
```

看下生成代码的目录，果然多了一个MyClass。让我们看看里面的内容吧。

``` java
public  class MyClass extends haxe.lang.HxObject
{
    public   void myFunc()
	{
		haxe.Log.trace.__hx_invoke2_o(0.0, ( "myFunc" + " was called" ), .......//之后省略
	}
	//还有些反射取所有字段之类的方法，省略
}
```

而main的目标代码如下：

``` java
	public static   void main()
	{
		haxe.root.MyClass c = new haxe.root.MyClass();
		c.myFunc();
	}
	
```

我们就这样生成了一个自己的类，类里面成员函数的名字都是在生成的时候传进去的。

这个例子的官方文档链接在[这里](http://haxe.org/manual/macro-reification-class.html)。