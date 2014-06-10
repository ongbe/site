---
layout: docs
title: 快速上手
prev_section: home
next_section: installation
permalink: /docs/quickstart/
---

只需要如下几个步骤，就可以快速搭建起一个简单的演示策略，并运行起来，体验完整的策略开发流程。

* 下载策略开发包
* 向管理员申请用户名、密码、及策略ID
* 修改开发包中带的策略配置文件，就可以运行策略了
* 需要更方便地管理和实时查看策略运行绩效，以及更多功能？只需要下载策略监控终端，登录到我们提供的演示服务器就行了

下面以python策略为例，讲解如何运行起一个简单的演示策略。

### Python策略运行环境的准备

Python策略的运行依赖于Python环境和掘金SDK包Python版，相关环境的准备可参考[Python策略开发指引](/docs/python_tut/)中的环境准备部分。

Python SDK下载后解压目录结构如下：

```
  --python-sdk
     |
     ---examples
     |
     ---lib
```

其中lib目录下是需要安装到python环境下的策略开发包，examples目录下是提供的演示策略。

### 策略运行
我们以strategy_simple.py为例，讲解如下运行这个策略。

* 首先，修改.ini配置文件，用申请到的用户名、密码、策略ID分别替换相应的your name, your password, strategy_id文字内容；

* 在命令行终端， 运行如下命令：

```
   python strategy_simple.py
```
就可以发现策略会把收到的相应行情数据打印出来。

### 策略终端查看
如果需要查看更详细的策略运行情况，可以通过我们的策略运行监控终端来查看，用申请到的用户名、密码登录。

![login]({{site.baseurl}}/images/docs/terminal_login.png)

策略运行情况查看

![strategy]({{site.baseurl}}/images/docs/terminal_str_run.png) 

  






