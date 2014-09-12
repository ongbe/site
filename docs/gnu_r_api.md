---
layout: docs
title: R语言API介绍  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/gnu_r_api/
---
![image](http://www.r-project.org/Rlogo.jpg)
本节介绍R语言版gmsdk包的API接口，方便在R环境中完成实时行情订阅、历史数据提取、历史行情回放，交易。gm_前缀的函数汇集了其他类的功能接口，以便于策略的编写时完成灵活的调取数据、下单和接收执行回报等功能。
另一方面，实时行情(live)、历史行情查询(query)和回放(playback)、交易(trade)四个部分功能都可以分别独立使用，比如单独使用实时行情接口，单独使用历史数据接口，单独回放历史行情，单独调用交易下单或查询接口等。

#### 测试服务地址

为方便策略开发和测试，以下服务在公网开放访问。真实交易时将配置切换到自己的服务地址即可，代码不用作任何修改，开发、测试和生产3个环境可以无缝迁移。地址如有变更，请查询官网通知: http://www.myquant.cn

```
实时行情服务: tcp://211.154.152.181:5103
模拟行情服务: tcp://211.154.152.181:5106
数据查询服务: tcp://211.154.152.181:5104
模拟交易服务: tcp://211.154.152.181:5050
```

#### R语言扩展包gmsdk

掘金量化交易平台的R语言版SDK跟其他语言接口一样，可以完整地应用掘金量化平台的后台服务，主要封装了三大部分的功能，包括实时行情的订阅、历史行情的回放、交易通道接口的使用。

另外，提供了几个方便的辅助函数，如用来查询当前package版本的version()函数，用来方便策略进行初始化的```gm_init```, 用于设置各种R语言中的事件响应函数，比如```set_tick_handler```等。

以下是R语言的gmsdk包的功能函数列表，以及函数功能的简要说明：

##### gmsdk包提供的函数列表 

以下是StrategyBase类的完整接口：

```
# SDK服务初始化、启动、停止
gmsdk::gm_init,  掘金服务初始化     
gmsdk::gm_start, 启动
gmsdk::gm_stop,  停止 

# 实时行情部分
gmsdk::live_init, 实时行情初始化
gmsdk::live_close, 断开实时行情
gmsdk::live_reconnect, 重连实时行情
gmsdk::live_subscribe, 订阅交易品种的行情
gmsdk::live_unsubscribe, 取消订阅品种的行情

# 行情回放部分
gmsdk::playback_init, 回放初始化
gmsdk::playback_start, 开始回放
gmsdk::playback_stop,  停止回放

# 历史行情查询部分
gmsdk::query_init, 查询初始化
gmsdk::query_bars, 按时间段查询分时行情数据
gmsdk::query_last_bars, 查询最近的分时数据

gmsdk::query_ticks, 按时间段查询逐笔行情
gmsdk::query_last_ticks, 查询最近的逐笔行情
gmsdk::query_timepoint_tick, 查询时间点的逐笔行情

gmsdk::query_trades, 按时间段查询逐笔成交

gmsdk::query_daily_bars，按时间段查询日线数据
gmsdk::query_last_daily_bars, 查询最近日线数据

# 行情部分事件响应处理的关联函数
gmsdk::set_bar_handler,  设置分时数据响应处理函数
gmsdk::set_tick_handler, 设置tick逐笔行情响应处理函数
gmsdk::set_trade_handler, 设置逐笔成交响应处理函数

gmsdk::set_live_error_handler, 设置行情错误处理函数

# 交易部分
gmsdk::trade_init, 交易初始化
gmsdk::trade_close, 退出交易
gmsdk::trade_reconnect, 重新连接交易通道

## 交易下单、撤单
gmsdk::trade_open_long,   开多单
gmsdk::trade_open_short,  开空单
gmsdk::trade_close_long,  平多单
gmsdk::trade_close_short, 平空单
gmsdk::trade_cancel_order,撤单

## 交易帐户查询
gmsdk::trade_list_positions, 查询所有持仓
gmsdk::trade_query_specified_position， 查询指定代码和方向的持仓
gmsdk::trade_query_cash， 查询帐户资金情况
gmsdk::trade_query_order，查询订单

## 设置交易事件的响应函数
gmsdk::trade_set_execution_handler, 设置成交回报响应的处理函数
gmsdk::trade_set_order_cancel_rejected_handler, 设置撤单失败的响应处理
gmsdk::trade_set_order_new_handler, 设置委托被接受的处理
gmsdk::trade_set_order_rejected_handler, 设置委托被拒绝的处理
gmsdk::trade_set_order_partially_filled_handler, 设置委托部分成交的处理
gmsdk::trade_set_order_filled_handler, 设置委托订单完全成交的处理
gmsdk::trade_set_order_stopped_handler, 设置委托停止交易的处理
gmsdk::trade_set_trade_error_handler, 设置交易错误的响应处理

# 策略启动
gmsdk::run，在相关事件响应函数都设置完成后，用run函数来启动策略，

# 版本查询
gmsdk::version

```

##### 简单说明：

为方便R语言中数据的处理习惯，gmsdk包中的接口数据返回已经转换成了Data Frame格式，可根据对应的列名直接访问，相应的字段意义也可以参考列名直接获得，省去了查询文档的麻烦。

举例如下：

```
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
如果对应时间段没有数据，会返回如下信息:

```
> gmsdk::query_bars('CFFEX.IF1409', 15, '2014-09-08 09:15:00', '2014-09-08 09:30:00')
             ERROR
1 该条件没查到数据

```
