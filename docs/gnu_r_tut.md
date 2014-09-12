---
layout: docs
title: R语言gmsdk使用说明  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/gnu_r_tut/
---

本节简单介绍如何在R环境中安装和使用gmsdk

### R语言环境的安装

![image](http://www.r-project.org/Rlogo.jpg)


linux或其他类unix操作系统中，R语言环境的安装大多推荐是用源码安装方式，tar包请从［Cran镜像］(http://cran.r-projects.org/)下载, 因为需要Rcpp扩展包的支持，请选择最新的R3.1+版本。

解压后进入目录，运行命令
$ ./configure
$ make

另外，现在常用的linuxn发行版本，如debian,CentOS等都有直接可用的二进制安装包。
比如，在debian下，用包管理器可以查到相关的R扩展工具包：
$ aptitude search r-cran- 
可根据需要选择安装相应的二进制包，不需要自己编译。

windows环境下，可以直接选择下载[R-3.1.1-win.exe](http://cran.r-project.org/bin/windows/base/R-3.1.1-win.exe)，安装注意根据你的操作系统32位还是64位来选择，然后在本地运行即可。

### R环境中扩展包的安装
首先，运行R，或R的图形界面窗口中的菜单。
下面以控制台命令为例说明：
知道package名，要安装，可以直接运行install.packages('package_name') 命令，把参数换成你需要的扩展包名即可，比如需要安装xts
> install.packages('xts')
会让你选择镜像站点，然后安装过程是全自动的，包括依赖的包也会自动完成，上例中, 会先安装zoo包

如果记不清楚包名，还可以直接不带参数，install.packages()会返回所有的可用包列表。

为了方便安装，我们的包提供的是编译好的二进制包，直接解压在你个人的R扩展包目录下即可使用。

比如我的机器中，扩展包目录是```~/R/i486-pc-linux-gnu-library/3.1/```

Windows环境下，通常可以把解压的gmsdk.dll直接放在R安装目录下的library中，类似于```<R_HOME>\library\gmsdk\libs\i386\```

### R环境中使用扩展包
先需要加载包，用命令：

```
> library('gmsdk')
```
或

```
> require('gmsdk')  
```

如果提示需要别的包，请按提示安装,比如

```
> install.packages('Rcpp')
```

### 检查和使用
安装好后，输入如下命令检查：

```
> gmsdk::version()
[1] "v1.1.0"

```

使用我们提供的互联网上演示服务，测试下历史数据的查询：

```
> query_uri = "tcp://211.154.152.181:5104"

> gmsdk::query_init(query_uri, username, password)
[1] "成功"

> gmsdk::query_bars('CFFEX.IF1409', 300, '2014-09-09 09:15:00', '2014-09-09 09:30:00')
            timestamp       bar_time exchange symbol   open   high    low
1 2014-09-09 09:19:59 20140909091500    CFFEX IF1409 2471.4 2473.0 2468.8
2 2014-09-09 09:24:59 20140909092000    CFFEX IF1409 2470.2 2470.4 2468.0
3 2014-09-09 09:29:59 20140909092500    CFFEX IF1409 2468.6 2470.4 2467.8
   close volume
1 2470.2  15338
2 2468.8   5684
3 2470.0   5949
```


 
