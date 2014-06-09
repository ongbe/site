---
layout: docs
title: Python策略开发指引  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/python_tut/
---

Python 开发环境的设置及示例
=============

红树推荐使用JetBrains的Pycharm社区免费版作为Python策略的开发环境，PyCharm是JetBrains系列产品的一员，也是现在最好用的IDE之一。PyCharm维持了JetBrains一贯高度智能的作风，简要枚举如下：

![image]({{site.baseurl}}/images/miner.png)
![image]({{site.baseurl}}/images/tutorial/pycharm.jpg)

* 独特的本地VCS系统
* 强大的重构功能
* 基于上下文的智能代码提示和纠错
* 方便的程序调试功能

## 部署基本的Python编程调试环境

### 准备相应软件
* **下载PyCharm**
 
PyCharm社区版免费下载地址：http://www.jetbrains.com/pycharm/, 当前最新版本为V3.4，下载并安装 Community Edition for FREE

* **下载Python**

安装完PyCharm后，还需要安装Python解释器，下载地址：https://www.python.org/downloads/

推荐安装最稳定且比较新的版本V2.7.7，如果对新版本Python有兴趣，也可以安装3.X学习，两者可以并存。

### 创建一个简单项目

通过创建一个简单项目来测试环境是否安装好。

* **打开PyCharm新建第一个项目**

此时Python解释器还处于未配置的状态，通过如下操作告诉PyCharm我们安装了Python的路径：
方法：在新建项目（New Project）在Interpreter处填入Python安装路径.
操作路径一：File>New Project...>Inerterter
操作路径二：File> Setting...> Project Interpreter

![image](tutorial/newproject.png)

* **增加一个解释器:**

![image](tutorial/pythoninterpreter.png)


***增加之后PyCharm会智能地提示你安装setuptool和pip，照着提示一路点击就行了。（Python2.7的setuptool安装会报错UnicodeDecodeError: 'ascii' codec can't decode byte 0xc4 in position 33: ordinal not in range(128)，需要手工修改脚本再安装）。***

* **设置一个简单的Hello World!程序**

配置完成后填入项目路径新建一个项目，然后新建一个.py文件，写一句helloworld：
输入：print 'hello world'

![image](tutorial/helloworld.png)


* **配置程序的运行环境和参数**

此时还无法运行，因为没有配置项目的入口脚本，通过下图的步骤指定一个：

![image](tutorial/config_run.png)


![image](tutorial/config_run1.png)


在scrip框里填入你的入口脚本, script配置框中选择或填入相应的python文件路径。

![image](tutorial/config_runn.png)

* **Run Hello World程序**

Run Hello World输出如图：

![image](tutorial/config_run3.png)
![image](tutorial/hello_output.png)

以上测试检查无误后，说明Python基本环境安装完成；

## 安装掘金策略开发工具包

安装GMSDK及其依赖库, 选择前面安装的Python v2.7.6解释器路径。

### 安装依赖库

安装GMSDK所需要用到的第三方基础库

* 安装CFFI:

![image](tutorial/gmsdk_install_cffi.png) 

* 安装PyCParser:

![image](tutorial/gmsdk_install_cparser.png) 

### 安装GMSDK

安装掘金策略开发SDK库，注意选择Python路径，当前GMSDK建议只在Python V2.7.x 上使用：

![image](tutorial/gmsdk_install_gmsdk.png) 


### 运行例子策略

下面我们通过一个简单的例子策略来检查一下策略开发包是否安装正常。

同样，新建一个.py文件，拷贝我们的Demo策略strategy_simple.py的内容：

![image](tutorial/strategy_demo.png)

**配置运行/调试脚本：**
操作路径：Run>Edit Configurations...

![image](tutorial/strategy_run.png)

**配置后：**

![image](tutorial/strategy_run1.png)

**运行状态：**

点击运行，在有行情时段，会看到策略正常运行，打印出收到的行情数据。

![image](tutorial/result.png)


接下来就可以参考示例，开始编写你自己的策略了！

