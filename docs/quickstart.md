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

Python策略的运行依赖于Python环境和掘金SDK包Python版，相关环境的准备可参考[Python策略开发指引]({{site.baseurl}}/docs/python_tut/)中的环境准备部分。

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
我们以strategy_dualma.py为例，讲解如下运行这个策略。

* 首先，修改.ini配置文件，用申请到的用户名、密码、策略ID分别替换相应的your name, your password, strategy_id文字内容；

Python程序中，需要注意，是用配置文件初始化的。

```python  
    # init with config file
    strategy = DualMA(config_file='strategy_dual_ma.ini')

    print 'strategy ready, waiting for market data ......'
```

```ini
[strategy]
md_addr=cloud.myquant.cn:8000
td_addr=cloud.myquant.cn:8001
username=your username
password=your password
strategy_id=your strategy_id

```

如果用参数而不是配置文件，则需要修改程序中的如下部分代码中的your name, your password, your strategy_id为相应内容：

```python
    # init by arguments
    strategy = DualMA(
        md_addr='cloud.myquant.cn:8000',
        td_addr='cloud.myquant.cn:8001',
        username='your username',
        password='your password',
        strategy_id='your strategy_id',
        mode=2,
        subscribe_symbols='CFFEX.IF1406.tick,CFFEX.IF1406.bar.15',  # 订阅tick和15s周期bar
    )
```

* 在命令行终端， 运行如下命令：

```
   python strategy_dual_ma.py
```

  






