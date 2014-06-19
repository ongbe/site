---
layout: docs
title: C#策略开发指引  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/csharp_env/
---

1. 下载c# sdk并解压，sdk包含两个dll：`libgmnet.dll，libgm.dll`。策略运行依赖于这两个库文件，需要放在策略的可执行文件目录。

2. 在visual studio中创建c#项目， 项目引用中添加对`libgmnet.dll`的引用。

3. 编写自己的策略。策略继承自StrategyBase，添加自己的策略逻辑代码。配置好策略ID（在掘金终端中创建策略时，会分配策略ID，复制过来即可），服务地址等参数。

4. 编译，运行，搞定！策略运行起来后，在掘金终端中可以看到策略的运行的实时状态。

