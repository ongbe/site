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

```ini
行情服务连接字符串： md_addr=cloud.myquant.cn:8000
交易服务连接字符串： td_addr=cloud.myquant.cn:8001
```

### R语言扩展包gmsdk

掘金量化交易平台的R语言版SDK跟其他语言接口一样，可以完整地应用掘金量化平台的后台服务，主要封装了三大部分的功能，包括实时行情的订阅、历史行情的回放、交易通道接口的使用。

另外，提供了几个方便的辅助函数，如用来查询当前package版本的version()函数，用来方便策略进行初始化的```strategy_init```, 用于设置各种R语言中的事件响应函数，比如```set_tick_handler```等。

以下是R语言的gmsdk包的功能函数列表，以及函数功能的简要说明：

#### gmsdk包提供的函数列表 

以下是gmsdk包的完整接口：


##### SDK服务的登录、登出
`gmsdk::login`  掘金服务登录     

```
    * @param addr       掘金数据服务器地址, <机器名/IP>:<端口号>格式，如 cloud.myquant.cn:8000
    * @param username   掘金账号
    * @param password   掘金密码
```

`gmsdk::logout`  登出

##### 行情初始化方法
gmsdk::md_init 行情初始化方法，新版行情接口进行了简化，通过mode来区分实时、模拟、回放和查询模式

##### 订阅和退订
`gmsdk::subscribe`        订阅，参数格式: 'CFFEX.IF1506.tick,SHFE.ag1506.bar.60'
`gmsdk::unsubscribe`      退订

##### 历史行情查询部分

`gmsdk::query_bars` 按时间段查询分时行情数据，支持单个代码

```
* @param symbol 证券代码, 如CFFEX.IF1308
* @param bar_type  bar周期，以秒为单位，比如60表示1分钟bar
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return bar数据列表
```

`gmsdk::query_last_bars` 查询最近的分时数据，支持多个代码

```
* @param symbols 多个证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @param bar_type bar类型，以秒为单位, 如 60 表示1分钟分时K线
* @return Bar数据列表
```

`gmsdk::query_last_n_bars` 查询最近的分时数据，支持单个代码

```
* @param symbol 证券代码, 如CFFEX.IF1308
* @param bar_type bar类型，以秒为单位, 如 60 表示1分钟分时K线
* @param n 数据个数
* @return Bar数据列表
```

`gmsdk::query_ticks` 按时间段查询逐笔行情

```
* @param symbol 证券代码, 如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return tick数据列表
```

`gmsdk::query_last_ticks` 查询最近的逐笔行情

```
* @param symbol_list 多个证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @return tick数据列表
```

`gmsdk::query_last_n_ticks` 查询最近的逐笔行情

```
* @param symbol 证券代码, 如CFFEX.IF1308
* @param n 数据个数
* @return tick数据列表
```

`gmsdk::query_daily_bars`，按时间段查询日线数据

```
* @param symbol 证券代码, 如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return DailyBar数据列表
```

`gmsdk::query_last_daily_bars` 查询最近日线数据

```
* @param symbol_list 证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @return DailyBar数据列表
```

`gmsdk::query_last_daily_bars` 查询最近日线数据

```
* @param symbol 证券代码, 如CFFEX.IF1308
* @param n 数据个数
* @return DailyBar数据列表
```

##### 行情部分事件响应处理的关联函数
`gmsdk::set_bar_handler`  设置分时数据响应处理函数

```
    回调函数模板，  
    on_bar <- function(bar) {
        //handle the bar 
    }
```

`gmsdk::set_tick_handler` 设置tick逐笔行情响应处理函数

```
    回调函数模板，  
    on_tick <- function(tick) {
        //handle the tick 
    }
```

`gmsdk::set_live_error_handler` 设置行情错误处理函数

```
    回调函数模板，  
    on_md_error <- function(error, msg) {
        //handle the error info 
    }
```

##### 交易部分
`gmsdk::trade_init` 交易初始化

```
* @param strategy_id 策略ID
* @param addr        交易服务器地址, 格式<host name/ip>:<port num>
```

`gmsdk::trade_reconnect` 重新连接交易通道

###### 交易下单、撤单
`gmsdk::trade_open_long`     开多单

```
    * @param exchange 交易所代码
    * @param sec_id 证券代码
    * @param price  价格：price==0为市价单，否则为限价单
    * @param volume 数量
    * @return order 返回委托请求的Order对象
```

`gmsdk::trade_open_short`     开空单

```
    * @param exchange 交易所代码
    * @param sec_id 证券代码
    * @param price  价格：price==0为市价单，否则为限价单
    * @param volume 数量
    * @return order 返回委托请求的Order对象
```

`gmsdk::trade_close_long`     平多单

```
    * @param exchange 交易所代码
    * @param sec_id 证券代码
    * @param price  价格：price==0为市价单，否则为限价单
    * @param volume 数量
    * @return order 返回委托请求的Order对象
```

`gmsdk::trade_close_short`    平空单

```
    * @param exchange 交易所代码
    * @param sec_id 证券代码
    * @param price  价格：price==0为市价单，否则为限价单
    * @param volume 数量
    * @return order 返回委托请求的Order对象
```

`gmsdk::trade_cancel_order`   撤单

```
    *@param cl_ord_id 客户端委托ID，委托的唯一识别符（client order id)
```

###### 交易帐户查询
`gmsdk::trade_list_positions` 查询所有持仓

`gmsdk::trade_query_specified_position` 查询指定代码和方向的持仓

`gmsdk::trade_query_cash` 查询帐户资金情况

`gmsdk::trade_query_order`查询订单

###### 设置交易事件的响应函数
`gmsdk::trade_set_execrpt_handler` 设置成交回报响应的处理函数

`gmsdk::trade_set_order_cancel_rejected_handler` 设置撤单失败的响应处理

`gmsdk::trade_set_order_new_handler` 设置委托被接受的处理

`gmsdk::trade_set_order_rejected_handler` 设置委托被拒绝的处理

`gmsdk::trade_set_order_partially_filled_handler` 设置委托部分成交的处理

`gmsdk::trade_set_order_filled_handler` 设置委托订单完全成交的处理

`gmsdk::trade_set_order_stopped_handler` 设置委托停止交易的处理

`gmsdk::trade_set_trade_error_handler` 设置交易错误的响应处理

##### 策略初始化
`gmsdk::strategy_init`  初始化策略，同时初始化策略需要的行情和交易接口，输入参数如下：

```
    *md_addr        行情服务uri, 比如 cloud.myquant.cn:8000
    *td_addr        交易服务uri, 比如 cloud.myquant.cn:8001
    *username       掘金账号
    *password       掘金密码
    *strategy_id    策略ID
    *subscribe_symbols 行情订阅的代码列表
    *start_time     回放开始时间
    *end_time       回放结束时间
```

##### 策略启动
`gmsdk::run`  在相关事件响应函数都设置完成后，用run函数来启动策略，

##### 版本查询
`gmsdk::version`


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
