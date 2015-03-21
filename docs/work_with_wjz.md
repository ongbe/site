---
layout: docs
title: 在挖金子网上展示量化策略  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/work_with_wjz/
---

在挖金子网（[link](http://www.wajinzi.me)）上展示您的量化策略，只需要三步：

1 注册成为专家用户；
2 创建一个量化产品；
3 读取产品的唯一ID，并配置在您的量化策略程序中；

### 注册成专家用户
登录[挖金子网站](http://www.wajinzi.me "挖金子")，进入[注册页面](http://www.wajinzi.me/register/?expert),填写相关信息。

如下图：

![Image]({{site.baseurl}}/images/docs/wjz/register_expert.png)

### 创建量化产品

进入“我的产品”页面，如下图：

![Image]({{site.baseurl}}/images/docs/wjz/create_prod1.png)

点击左上侧的“＋”或者移动鼠标到空白产品上，然后点击“创建产品”。

如下图所示，填写必要的产品信息：

![Image]({{site.baseurl}}/images/docs/wjz/create_prod2.png)

保存成功后，会有如下提示：

![Image]({{site.baseurl}}/images/docs/wjz/create_success.png)

恭喜您！ 产品已经成功创建。

## 获得产品ID

点击“我的产品”页面上对应产品图标下的“编辑”按钮，可以看到产品的ID.

如图：

编辑产品页面
![Image]({{site.baseurl}}/images/docs/wjz/edit_prod.png)

注意第二行，产品ID，这一项是只读的，移动下鼠标到这个框，双击鼠标按键，或者按“ctrl + A"，*确保选中全部内容*。
 
鼠标右键菜单或“ctrl + c”复制下来，粘贴到您的量化策略程序或配置文档中‘strategy_id=’后面，大概是这样子：

```ini
[strategy]
...
username=username
password=password
strategy_id=2bee4fac-cf6f-11e4-96b9-00163e003744
...
```

在本地机器上运行您的策略，就可以在挖金子网上看到策略的交易情况和绩效，同时可以参与产品业绩展示和排行，供您的粉丝膜拜〜〜

