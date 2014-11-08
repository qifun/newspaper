---
author:
  name: 张修羽
  email: xiuyu@qifun.com
  github: zxiy
  bio: 岂凡 软件工程师
  email_md5: 17cf991f2c20a4b56f5c17c96d0826a3
layout: post
title: "Haxe宏总结(二) @:build()的使用" 
---

上一篇提供了一个生成类的方法，但是，还是不够灵活，只能对类中的内容做一些替换，有更灵活的方法吗？

我们来想一想类的组成。一个类由很多个字段组成，每个字段可以是属性（变量），也可以是方法（函数）。

Haxe就提供了这样的方法：使用一个包含所有字段的数组来生成类。

还是来先看例子。

{% highlight haxe %}
import haxe.macro.Context;
import haxe.macro.Expr;

class TypeBuildingMacro {
  macro static public function
  build(fieldName:String):Array<Field> {
    var fields = Context.getBuildFields();
    var newField = {
      name: fieldName,
      doc: null,
      meta: [],
      access: [AStatic, APublic],
      kind: FVar(macro : String,
        macro "my default"),
      pos: Context.currentPos()
    };
    fields.push(newField);
    return fields;
  }
}
{% endhighlight %}

这段代码做了以下过程：获得了类原本的`Field`数组，加入了一个自定义的变量的`Field`：名字为函数传入的参数，内容为`"my default"`的字符串，属性为`static`和`public`。如果想了解Field这个类型的具体结构，可以阅读Haxe源代码。

然后，用下面的方式来使用这个函数

{% highlight haxe %}
@:build(TypeBuildingMacro.build("myFunc"))
class Main {
  static public function main() {
    trace(Main.myFunc); // my default
  }
}
{% endhighlight %}

在生成的类中，我们可以找到这样的代码：

{% highlight java %}
static 
{
	haxe.root.Main.myFunc = "my default";
}
//........
public static  java.lang.String myFunc;
{% endhighlight %}

通过这样的方法，无论是生成一个全新的类，还是给现有的类加方法，都可以完成了。

下面一个例子是给已有的类加入toString函数，来自于我们的haxe-import-csv项目，略作修改。

{% highlight haxe %}
macro public static function build():Array<Field> return
{
  var fields = Context.getBuildFields();
  var toStringDefExpr = macro function():String return $ { toStringExprMaker() };
  fields.push( {
    name: "toString",
    doc: null,
    meta: [{ name: ":overload", pos: Context.currentPos() }],
    access: [APublic, AOverride],
    kind: FFun(switch(toStringDefExpr.expr) {
      case EFunction(_, f):f;
      default: throw "Unreachable code!";
    }),
    pos: Context.currentPos()
  });
  fields;
}
private static inline function toStringExprMaker():Expr return
{
  var fields = Context.getBuildFields();
  var toStringExprBuilder:Expr = macro $v{Context.getLocalClass().get().name} + "(";

  for (field in fields)
  {
    if (field.name == "new")
    {
      var constructFunction = switch(field.kind)
      {
        case FFun(f):
        {
          f;
        }
        default:
        {
          throw "Unreachable code!";
        }
      }

      var isFirst:Bool = true;
      for (arg in constructFunction.args)
      {
        if (isFirst)
        {
          toStringExprBuilder = macro $toStringExprBuilder + $v {arg.name} + "(" + Std.string( $i { arg.name } ) + ")";
          isFirst = false;
        }
        else
        {
          toStringExprBuilder = macro $toStringExprBuilder + ", " + $v {arg.name} + "(" + Std.string( $i { arg.name } ) + ")";
        }
      }
      break;
    }
  }
  macro $toStringExprBuilder + ")";
}
{% endhighlight %}

这一部分相关的官方文档链接在[这里](http://haxe.org/manual/macro-type-building.html)。