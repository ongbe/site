---
layout: docs
title: C/C++策略开发指引
permalink: /docs/cpp_tut/
---

GMSDK目录结构
----------

其中头文件在inc目录下，dll和lib文件在lib目录下，如下图所示：

![gmsdk]({{site.baseurl}}/images/docs/cpp/tut/gmsdk_tree.png)


Visual C++中策略开发环境设置
------

在Windows下C++策略的开发环境，红树推荐使用微软的Visual Studio。
红树策略开发SDK是作为第三方库来使用的，为了不影响其他工程，建议按项目来设置，把SDK的include头文件和DLL库文件设置在项目中。

最简单的方法是直接在工程属性中进行相关内容的填写，考虑到策略可能还会用到其他第三方库，我们这里不单独区分红树策略开发SDK或是其他库，统称第三方库，配置方法是一样的。

以下示例中，我们假定需要用到的第三方库放在E:\3rdlibs目录下，我们需要安装的库为SomeLibrary。

## 准备Visual Studio

从获得的Visual Studio安装包安装VS，安装过程护照向导一路确认即可，网上也有很多指导，这里从略。

## 查看示例策略

为了使用方便，我们在SDK包中包含了简单的示例策略，其工程.sln文件使用了包含策略开发SDK的相对路径，只需要把相应的SDK解压到某个目录下，直接双击对应的VS工程文件即可打开。
下面的新建工程中相应设置可以参考示例策略工程来配置。

## 建立一个新工程

假设这里新建了一个名为3rdlibConfigDemo的工程，我们可以右键点击它，然后选择最下方的属性按钮，打开配置的窗口。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_project.png)


## 设置工程的属性

*  头文件路径的配置

   为了能够让编译器在编译时能够找到第三方库的头文件（.h、.hpp等等扩展名的头文件）的位置，首先需要将第三方库的头文件路径添加到属性当中。具体配置的位置可以在属性当中的配置属性-VC++目录-Include目录中找到。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_include_path.png)

设置对应的包含头文件

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_include_path1.png)

*  库文件路径以及引用库名称的配置

      为了能够让链接器在编译时能够找到第三方库的库文件（.lib）的位置，还需要将第三方库的库文件路径添加到属性当中。具体配置的位置可以在属性当中的配置属性-VC++目录-Library目录中找到。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_lib_path.png)

设置库路径

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_lib_path1.png)


* 添加链接库

在添加库文件目录之后，我们还需要指定具体需要链接哪些库文件。添加库文件的名称可以参考第三方库的文档，当然有些库在引用头文件时，会自动的指明需要引用库的名字（例如boost），所以这个步骤在某些情况下也可以省略。但是由于大多数第三方库不支持这种自动指明引用库名字的方式，所以这个步骤还是必须走的过程。具体配置的位置可以在属性当中的配置属性-连接器-其他依赖中找到。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_link_lib.png)

增加link要用到的lib文件

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_link_lib1.png)

* 配置执行路径

    在引用第三方库的代码编译、链接完成之后，会生成一个可执行文件。这个可执行文件在运行时，可能会寻找所依赖库的可执行文件或动态链接库（.exe、.dll），所以我们还需要做一些配置，能够让程序在运行时，找到到这些文件。具体的配置方法为右键点击我的电脑-属性，然后按照下面的方法配置一个叫环境变量的东西。
  
![image]({{site.baseurl}}/images/docs/cpp/tut/vs_dll_path_env.png)

这个方法会影响到所有的运行环境，如果有多个库需要避免的话，可以只在这个工程下面设置，在 "工程属性" ==> "调试" ==> "环境"里，添加类似如下所示的内容：
PATH=%PATH%;$(TargetDir)\DLLS

这样就可以把 $(TargetDir)\DLLS 临时添加到该工程所属的系统 PATH 里。

   当然，也可以简单地把对应的.dll文件拷贝到工程的debug和release目录下，但这会比较麻烦，而且容易因为版本没有对应更新出现一些古怪的错误，不建议这样做，只是在最后策略部署到运行环境时拷贝所用到的dll文件即可。

## 简单可重用的配置方法

在Visual Studio当中，工程属性也是可以继承的，这种特性可以使我们对工程配置进行重用。在创建工程之后，通常我们的工程会自动的继承一些Property Sheets，这样如果多个策略工程需要统一管理的话，配置起来会相对简单一些，下面我们先来看一下如何查看这些默认继承的Property Sheets。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_properties_manager.png)


![image]({{site.baseurl}}/images/docs/cpp/tut/vs_property_config.png)

从上面的图中我们看到了两个文件夹，一个代表Win32平台Debug模式的会自动继承的Property Sheets；另一个代表Win32平台Release模式会自动继承的Property Sheets。其中需要说明的是，Microsoft.Cpp.Win32.user这个Property Sheet是在Win32平台（无论是Debug还是Release）下一定会继承的属性配置，而其他的Property Sheets则是根据用户自定义的工程配置，自动选择继承与否。下面我们来看一下Microsoft.Cpp.Win32.user这个Property Sheet长的是什么样的。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_properties_uni_config.png)

下面以包含目录的配置为例说明，链接库的配置类似，跟在工程中单独配置方法是一样，不再赘述。

![image]({{site.baseurl}}/images/docs/cpp/tut/vs_uni_include_path.png)

