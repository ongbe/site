---
layout: docs
title: Matlab环境 gmsdk使用说明  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/matlab_tut/
---

本节简单介绍如何在Matlab安装和使用gmsdk

### gmsdk支持的Matlab版本

gmsdk支持Matlab R2013b 及以后版本。现阶段暂支持windows 32位 Matlab版本。

### Matlab中 安装 gmsdk toolbox(以R2013b为例)

1. 将gmsdk 解压到$MatlabRoot\toolbox中(其他路径也可以，但是为了方便管理，我们一般都安装在这里)，$MatlabRoot是你的Matlab安装路径，你可以在Matlab中输入`matlabroot`命令获取
2. 在Matlab中如下操作，选择 `HOME—>Set Path...`，点击`Add Folder...`
3. 在浏览文件中，选择刚才的安装路径`$MatlabRoot/toolbox/.../goldminer`后，点击确定
4. 此时返回到Set Path对话框，点击左下角的保存按钮(记住一定要保存)，至此工具箱安装完毕，点击Close关闭对话框


### 检查和使用

输入命令:


```c#
> gm.Init('cloud.myquant.cn:8000', '', '');
> ticks = gm.GetLastNTicks('CFFEX.IF1506', 10);
> ticks

ticks = 

    exchange     sec_id     utc_time     last_price    open     high 
    ________    ________    _________    __________    ____    ______

    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09      4224        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09    4223.4        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09    4224.2        4118    4229.6
    'CFFEX'     'IF1506'    1.428e+09    4224.2        4118    4229.6
 

...
```

在matlab console 中显示行情记录说明 gmsdk正常安装。更多SDK用法指引请参考gmsdk包中的examples.
