---
author:
  name: 方里权
  email: fangliquan@qq.com
  github: chank
  bio: 岂凡 软件工程师
  email_md5: 5851b66b5708deba39cb20fca4adf312
  url: 
layout: post
title: "Unity3d构建总结"
---

做Unity持续集成总结

## 一、mono编译机制

Unity是使用mono来编译C#脚本的，而mono支持二种编译机制：JIT(Just-In-Time)[^JIT]编译和AOT(Ahead-of-Time)[^AOT]编译。因此Unity也是支持以这两种方式来编译脚本的。JIT编译是指首先将源文件编译成中间语言(IL)，在运行时JIT编译器再将IL编译成能够再机器上运行的指令。因此JIT编译可以大大提高编译速度，并且代码只需要编译一次就能在安装了JIT编译器的机器上运行。而AOT编译则是指直接将代码编译成能够在特定机器上运行的程序。一般情况下，mono都是使用的JIT机制编译成.NET dll 文件的。但在iOS中，mono使用的是AOT机制。

## 二、Unity在Linux上做持续集成存在的问题

在做Unity的持续集成时，我们本来是想在Linux上做的，但由于下面两个原因我们将jenkins搭到了mac上，最主要是第二个原因。

（一）在Linux上做Unity的持续集成时，会报 `unity error:debugger-agent Unable to listen on 6` 异常，这主要是因为Unity跟mono是使用SDB(Mono Soft Debugger)[^SDB]协议来调试编译程序的，而SDB协议是基于socket上的，但在Linux上如果不是root用户的话是不能监听低于1024的端口的。目前在Unity上还没有找到修改debugger-agent监听端口的方法，虽然在mono中是可以的，但Unity只提供了一个Unity命令来构建代码。

（二）在Linux上，Unity是需要使用playonlinux中管理的wine才能运行的，即Unity只不过是运行在windows虚拟机上而已，并不支持Linux。而jenkins中的Unity插件是不支持Linux的。要使用jenkins中的Unity插件是因为Unity的命令行是运行在后台的，它的编译错误只会记录到日志文件上，因此只有通过分析日志文件我们才能知道Unity有没有构建成功。jenkins上的Unity插件就是做了这个事。

## 三、Unity命令行的执行

如果我们直接调用 `Unity -quit -batchmode` 来编译项目，会生成Assembly-CSharp.dll和Assembly-CSharp-Editor.dll文件。Assembly-CSharp.dll是编译运行时代码产生的文件，而Assembly-CSharp-Editor.dll是编译Unity编辑器代码产生的文件。

但如果我们使用 `Unity -quit -batchmode -executeMethod Qifun.Sun.Demo.Editor.BuildUtil.BuildApk` 命令调用下面代码来生成apk时，通过分析日志，发现是会依次生成Assembly-CSharp.dll，Assembly-CSharp-Editor.dll和Assembly-CSharp.dll的。第一次生成的Assembly-CSharp.dll主要是针对开发平台生成给Unity编辑器用的，而第二次编译生成的Assembly-CSharp.dll则是针对Android平台的。

{% highlight scala %}

// #if UNITY_EDITOR
using UnityEditor;
using System.Collections;
using System.Collections.Generic;

namespace Qifun.Sun.Demo.Editor
{
    public class BuildUtil 
    {
        private static string[] GetBuildScenes()
        {
            List<string> sceneNames = new List<string>();
            foreach(EditorBuildSettingsScene scene in EditorBuildSettings.scenes)
            {
                if(scene == null)
                    continue;
                if(scene.enabled)
                {
                    sceneNames.Add(scene.path);
                }
            }
            return sceneNames.ToArray();
        }

        public static void BuildApk()
        {
            PlayerSettings.bundleIdentifier = "com.qifun.sun";
            BuildPipeline.BuildPlayer(GetBuildScenes(), "sun.apk", BuildTarget.Android, BuildOptions.ShowBuiltPlayer);
        }
    }
}
// #endif

{% endhighlight %}

注意上面代码中被注释掉的 `#if UNITY_EDITOR`。 `UNITY_EDITOR`是Unity的平台定义，在Unity中使用了平台依赖编译[^PDC]，会根据不同的平台来编译不同的代码。`UNITY_EDITOR`是针对Unity编辑器的代码(`UNITY_ANDROID`是针对android平台的代码等等)。那么上面代码应该放在哪个文件夹里呢？在Unity中，有关编辑器代码是应该放在Editor目录的，因此上面代码应该是放在Editor目录里的。Unity在编译Assembly-CSharp-Editor.dll是默认定义了`UNITY_EDITOR`的，因此上面代码不需要加入`#if UNITY_EDITOR`。那么如果我们把上面代码放到Editor文件夹外呢(请注意这是不规范的做法，规范的做法是放到Editor文件夹里)？那就需要加`#if UNITY_EDITOR`了。因为Unity命令行在第二次编译Assembly-CSharp.dll时是没有定义`UNITY_EDITOR`并不会引用UnityEditor.dll的，但会编译这段代码，这时候如果不加`#if UNITY_EDITOR`的话Unity会报UnityEditor找不到错误。之前我们遇到过把上面代码放到Editor文件夹报错的情况，其实这主要是因为Unity对文件有记录，如果在系统的文件管理器下移动文件，第一次编译时Unity发现之前的文件没了会抛异常，并会从记录中移除对这个文件的记录，这时第二次编译时是可以编译通过的。这也是为什么Unity官方建议我们不要在系统的文件管理器下添加、移动和删除文件，而应该在Unity编辑器的Project Panel下操作文件的原因。

---

[^AOT]: mono官方对AOT机制的描述：<a href="http://www.mono-project.com/docs/advanced/aot/">http://www.mono-project.com/docs/advanced/aot/<a>

[^JIT]: 一篇分析JIT机制的文章：<a href="http://blogs.telerik.com/justteam/posts/13-05-28/understanding-net-just-in-time-compilation">http://blogs.telerik.com/justteam/posts/13-05-28/understanding-net-just-in-time-compilation<a>

[^SDB]: SDB：<a href="http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/">http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/<a>

[^PDC]: 平台依赖编译：<a href="http://docs.unity3d.com/Manual/PlatformDependentCompilation.html">http://docs.unity3d.com/Manual/PlatformDependentCompilation.html<a>
