---
layout: docs
title: Matlab语言API介绍  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/matlab_api/
---


本节描述掘金SDK的关键数据类型，包括返回值，常量与枚举，交易数据，行情数据等数据类型定义。

### 返回值编码定义

掘金SDK中的所有API的返回值均统一定义，包括系统级错误和业务级错误。

返回值编码及注释如下, matlab引用`ErrorCode.错误码`:

```python

# 共用的配置或通讯层面的错误 1000～1499 

SUCCESS                                  = 0     # "成功"

ERR_CONFIG_FILE_NOT_EXIST                = 1001  # "策略配置文件不存在"
ERR_CONFIG_PARSE                         = 1002  # "策略配置文件格式错误"
ERR_AUTH_CONNECT                         = 1003  # "无法连接掘金认证服务"
ERR_AUTH_LOGIN                           = 1004  # "无法登录掘金认证服务"
ERR_REQUEST_TIMEOUT                      = 1005  # "请求超时"
ERR_INVALID_PARAMETER                    = 1006  # "非法参数"
ERR_STRATEGY_INIT                        = 1007  # "策略未初始化"

# 业务层面错误码 共有部分， 1500～1999

ERR_INVALID_SYMBOL                       = 1501  # "非法证券代码"
ERR_INVALID_DATE                         = 1502  # "非法日期格式"

# 交易部分 2000 ~ 2999

ERR_TD_CONNECT                           = 2000  # "交易服务连接失败"
ERR_TD_LOGIN                             = 2001  # "交易服务登录失败"
ERR_TD_TIMEOUT                           = 2002  # "交易命令请求超时"
ERR_TD_NO_RESULT                         = 2003  # "该条件没查到数据"
ERR_TD_INVALID_SESSION                   = 2004  # "交易请求没有登陆"
ERR_TD_INVALID_PARAMETER                 = 2005  # "交易请求非法参数"
ERR_TD_STRATEGY_LOCKED                   = 2006  # "策略被禁止交易"
ERR_TD_SERVER_ERROR                      = 2007  # "交易服务内部错误"
ERR_TD_CORRUPT_DATA                      = 2008  # "返回数据包错误"


# 数据服务部分 3000～3999

ERR_MD_CONNECT                           = 3000  # "数据服务连接失败"
ERR_MD_LOGIN                             = 3001  # "数据服务登录失败"
ERR_MD_TIMEOUT                           = 3002  # "数据服务请求超时"
ERR_MD_NO_RESULT                         = 3003  # "该条件没查到数据"
ERR_MD_BUFFER_ALLOC                      = 3005  # "分配缓冲区错误"
ERR_MD_INVALID_PARAMETER                 = 3006  # "数据请求非法参数"
ERR_MD_SERVER_ERROR                      = 3007  # "数据服务内部错误"
ERR_MD_CORRUPT_DATA                      = 3007  # "返回数据包错误"

```

### 常量定义

掘金SDK中的常量定义。常量用于定义各种状态和标识类型。

- 行情模式，作为Init函数的输入参数, matlab引用方式： `MDMode.常量名`

```python
            MD_MODE_NULL       = 1,  // 不接收行情流, 仅查询
            MD_MODE_LIVE       = 2,  // 接收实时行情
            MD_MODE_SIMULATED  = 3,  // 接收模拟行情
            MD_MODE_PLAYBACK   = 4   // 接收回放行情
```

- OrderStatus(订单状态）, matlab引用方式： `OrderStatus.常量名`

```python
        New = 1;
        PartiallyFilled = 2;
        Filled = 3;
        DoneForDay = 4;
        Canceled = 5;
        PendingCancel = 6;
        Stopped = 7;
        Rejected = 8;
        Suspended = 9;
        PendingNew = 10;
        Calculated = 11;
        Expired = 12;
        AcceptedForBidding = 13;
        PendingReplace = 14;
```

- OrderRejectReason（订单拒绝原因）, matlab引用方式： `OrderRejectReason.常量名`

```python
        UnknownReason = 1;
        RiskRuleCheckFailed = 2;
        NoEnoughCash = 3;
        NoEnoughPosition = 4;
        IllegalStrategyID = 5;
        IllegalSymbol = 6;
        IllegalVolume = 7;
        IllegalPrice = 8;
        NoMatchedTradingChannel = 9;
        AccountForbidTrading = 10;
        TradingChannelNotConnected = 11;
        StrategyForbidTrading = 12;
        NotInTradingSession = 13;
```

- CancelOrderRejectReason（撤单拒绝原因）, matlab引用方式： `CancelOrderRejectReason.常量名`

```python
        OrderFinalized = 101;
        UnknownOrder = 102;
        BrokerOption = 103;
        AlreadyInPendingCancel = 104;
```

- OrderSide（订单方向）, matlab引用方式： `OrderSide.常量名`

```python
    Bid = 1  # 多方向
    Ask = 2  # 空方向
```

- OrderType（订单类型）, matlab引用方式： `OrderType.常量名`

```python
    Market = 1     # 市价单
    Limit = 2      # 限价单
```

- ExecType（订单执行回报类型）, matlab引用方式： `ExecType.常量名`

```python
    New = 1                # 交易所已接受订单
    DoneForDay = 4
    Canceled = 5           # 已撤
    PendingCancel = 6      # 待撤
    Stopped = 7            # 已停
    Rejected = 8           # 已拒绝
    Suspended = 9          # 暂停
    PendingNew = 10        # 待接受
    Calculated = 11        # 已折算
    Expired = 12           # 过期
    Restated = 13          # 重置
    PendingReplace = 14    # 待修改
    Trade = 15             # 交易
    TradeCorrect = 16      # 交易更正
    TradeCancel = 17       # 交易取消
    OrderStatus = 18       # 更新订单状态
    CancelRejected = 19    # 撤单被拒绝
```

- PositionEffect（开平仓类型）, matlab引用方式： `PositionEffect.常量名`

```python
    Open = 1         # 开仓
    Close = 2        # 平仓
```


### 交易数据类型

交易数据类型主要包括委托，执行回报，资金，持仓等数据类型, matlab中均表现为table类型

- Order(委托订单）, table中每一行为一条委托信息，列信息如下：
```python

		strategy_id                  # 策略ID
		account_id                   # 交易账号
		
		cl_ord_id                    # 客户端订单ID
		order_id                     # 柜台订单ID
		ex_ord_id                    # 交易所订单ID
		
		exchange                     # 交易所代码
		sec_id                       # 证券ID
		
		position_effect              # 开平标志
		side                         # 买卖方向
		order_type                   # 订单类型
		order_src                    # 订单来源
		status                       # 订单状
		ord_rej_reason               # 订单拒绝原因
		ord_rej_reason_detail        # 订单拒绝原因描述

		price                        # 委托价
		volume                       # 委托量
		filled_volume                # 已成交量
		filled_vwap                  # 已成交均价
		filled_amount                # 已成交额

		sending_time                 # 委托下单时间
		transact_time                # 最新一次成交时间
```


- Execution(委托执行回报）, table中每一行为一条委托信息，列信息如下：

```python
		strategy_id                 # 策略ID

		cl_ord_id                   # 客户端订单ID
		order_id                    # 交易所订单ID
		exec_id                     # 订单执行回报ID

		exchange                    # 交易所代码
		sec_id                      # 证券ID
		
		position_effect             # 开平标志
		side                        # 买卖方向
		ord_rej_reason              # 订单拒绝原因
		ord_rej_reason_detail       # 订单拒绝原因描述
		exec_type                   # 订单执行回报类型
		
		price                       # 成交价
		volume                      # 成交量
		amount                      # 成交额
		
		transact_time               # 交易时间
```

- Cash(资金）, table中每一行为一条资金信息，列信息如下：

```python
		strategy_id          # 策略ID
		currency             # 币种
		
		nav                  # 资金余额
		fpnl                 # 浮动收益
		pnl                  # 净收益
		profit_ratio         # 收益率
		frozen               # 持仓冻结金额
		order_frozen         # 挂单冻结金额
		available            # 可用资金
		
		cum_inout            # 累计出入金
		cum_trade            # 累计交易额
		cum_pnl              # 累计收益
		cum_commission       # 累计手续费
		
		last_trade           # 最新一笔交易额
		last_pnl             # 最新一笔交易收益
		last_commission      # 最新一笔交易手续费
		last_inout           # 最新一次出入金
		change_reason        # 变动原因
		
		transact_time        # 交易时间

```

- Position(持仓）, table中每一行为一条持仓信息，列信息如下：

```python
		strategy_id           # 策略ID
		
		exchange              # 交易所代码
		sec_id                # 证券ID
		side                  # 买卖方向
		volume               # 持仓量
		amount               # 持仓额
		vwap                 # 持仓均价
		
		price                # 当前行情价格
		fpnl                 # 持仓浮动盈亏
		cost                 # 持仓成本
		order_frozen         # 挂单冻结仓位
		available            # 可平仓位
		
		last_price           # 上一笔成交价
		last_volume          # 上一笔成交量
		init_time            # 初始建仓时间
		transact_time        # 上一仓位变更时间

```

### 行情数据类型

行情数据类型主要包括Tick，Bar，Trade, DailyBar


- Tick(逐笔行情数据）, table中每一行为一笔Tick行情信息，列信息如下

```python
		exchange                 #交易所代码
		sec_id                   #证券ID
		utc_time                 #utc时间，精确到毫秒
		
		last_price               #最新价
		open                     #开盘价
		high                     #最高价
		low                      #最低价
		
		bid_p1                   #买价一
		bid_p2                   #买价二
		bid_p3                   #买价三
		bid_p4                   #买价四
		bid_p5                   #买价五
		bid_v1                   #买量一
		bid_v2                   #买量二
		bid_v3                   #买量三
		bid_v4                   #买量四
		bid_v5                   #买量五
		ask_p1                   #卖价一
		ask_p2                   #卖价二
		ask_p3                   #卖价三
		ask_p4                   #卖价四
		ask_p5                   #卖价五
		ask_v1                   #卖量一
		ask_v2                   #卖量二
		ask_v3                   #卖量三
		ask_v4                   #卖量四
		ask_v5                   #卖量五
		
		cum_volume               #成交总量
		cum_amount               #成交总金额/最新成交额,累计值
		cum_position             #合约持仓量(期),累计值
		last_amount              #瞬时成交额
		last_volume              #瞬时成交量(中金所提供)
		upper_limit              #涨停价
		lower_limit              #跌停价
		settle_price             #今日结算价
		trade_type               #(保留)交易类型,对应多开,多平等类型
		pre_close                #昨收价
```

- Bar(各种周期的Bar数据）, table中每一行为一笔Bar行情信息，列信息如下

```python
		exchange       # 交易所代码
		sec_id         # 证券ID

		bar_type       # bar类型，以秒为单位，比如1分钟bar, bar_type=60
		bar_time       # bar时间
		utc_time       # 行情时间戳

		open           # 开盘价
		high           # 最高价
		low            # 最低价
		close          # 收盘价
		volume         # 成交量
		amount         # 成交额
```

- DailyBar(日频数据，在Bar数据的基础上，还包含结算价，涨跌停价等静态数据）, table中每一行为一笔DailyBar行情信息，列信息如下

```python

		exchange         # 交易所代码
		sec_id           # 证券ID

		bar_type         # bar类型
		bar_time         # bar时间
		utc_time         # 行情时间戳

		open             # 开盘价
		high             # 最高价
		low              # 最低价
		close            # 收盘价
		volume           # 成交量
		amount           # 成交额
		
		position         # 仓位
		settle_price     # 结算价
		upper_limit      # 涨停价
		lower_limit      # 跌停价
```

## 掘金SDK关键API接口

本节介绍关键的API接口，主要包括4类：实时行情， 历史数据提取，历史行情回放，交易。以上几类API可以各自独立使用，比如单独使用实时行情接口，单独使用历史数据接口，单独回放历史行情，单独调用交易下单或查询接口等。

#### 测试服务地址

为方便策略开发和测试，以下服务在公网开放访问。真实交易时将配置切换到自己的服务地址即可，代码不用作任何修改，开发、测试和生产3个环境可以无缝迁移。地址如有变更，请查询官网通知: http://www.myquant.cn

```
行情服务连接字符串： md_addr=cloud.myquant.cn:8000
交易服务连接字符串： td_addr=cloud.myquant.cn:8001
```

##### 初始化与运行

- **Init 函数**

初始化策略，设置服务地址，账号密码，策略ID，以及需要订阅的symbol列表。策略自动连接服务，并登陆，订阅对应的行情，准备好运行。

函数原型：

```python
function [ ret ] = Init( md_addr,            ...
						 username,           ...
						 password,           ...
						 mode,               ...
						 subscribe_symbols,  ...
						 start_time,         ...
						 end_time,           ...
						 td_addr,            ...
						 strategy_id         ...
						)
```


参数说明及返回值说明：

```
* 初始化GMSDK: 连接并登陆实时行情服务、历史行情服务、交易服务
* @param md_addr      行情服务器uri, 比如 cloud.myquant.cn:8000
* @param username     掘金账号
* @param password     掘金密码
* @param mode         行情模式，参机MDMode常量
* @param subscribe_symbols 行情订阅的代码列表
* @param start_time   回放开始时间
* @param end_time     回放结束时间
* @param td_addr      交易服务器uri, 比如 cloud.myquant.cn:8001
* @param strategy_id  策略ID
* @return 0：成功, 其他： error code(参见ErrorCode定义)
```

行情订阅`symbol_list`的参数说明:

```
    symbol_list 订阅代码表,参数格式如下。
    
    参数格式：

    订阅串有三节或四节组成,用'.'分隔，格式：
    
        对应交易所exchange.代码code.数据类型data_type.周期类型bar_type

    只有订阅bar数据时, 才用到第四节, 周期类型才有作用
    交易所exchange统一四个字节: 
    
    CFFEX-中金所 SHFE-上期所 DCE-大商所 CZCE-郑商所 SHSE-上交所  SZSE-深交所

    支持6种格式的订阅,使用如下:
    * CFFEX.* : 中金所,所有数据
    * CFFEX.IF1403.*    : 中金所,IF1403,所有数据
    * CFFEX.IF1403.tick : 中金所,IF1403, tick数据
    * CFFEX.IF1403.trade : 中金所,IF1403, trade数据
    * CFFEX.IF1403.bar.60: 中金所,IF1403, 1分钟(60秒)Bar数据
    * CFFEX.IF1403.*,CFFEX.IF1312.* : 中金所,IF1403和IF1312所有数据(订阅多个代码)
```

其中mode表示行情服务模式，可选项有如下几个：

```
            MD_MODE_NULL       = 1,  // 不接收行情流, 仅查询
            MD_MODE_LIVE       = 2,  // 接收实时行情
            MD_MODE_SIMULATED  = 3,  // 接收模拟行情
            MD_MODE_PLAYBACK   = 4   // 接收回放行情
```
当mode为1，也就是查询模式时，后面三个参数subscribe_symbols, start_time, end_time不用给定。
仅当mode为回放模式时，才需要给定start_time, end_time参数。


调用示例（通过参数初始化策略）

创建策略对象，并初始化，订阅`CFFEX.IF1406`的`tick`和`60秒`分时数据。

```python
ret = Init(
	'cloud.myquant.cn:8000',
	'your username',
	'your password',
	'CFFEX.IF1406.tick, CFFEX.IF1406.bar.60',
	MDMode.MD_MODE_SIMULATED,
	'',
	''
	'cloud.myquant.cn:8001',
	'your strategy_id',
    )
```

- **Run 函数**

运行策略，直到用户关闭策略，或策略内部调用`stop`函数。

函数原型：
```python
function [ ] = Run(  )
```

参数说明及返回值说明：
```
* @return 0：成功, 其他： error code
```


##### 行情订阅管理

- **Subscribe函数**

订阅行情

函数原型：

```python
function [ ret ] = Subscribe( symbol_list )
```

参数说明及返回值说明：

```
* 订阅行情
* @param symbol_list 订阅代码表,参考订阅规则
* @return 0:success 
*
* @note symbol_list 订阅规则和算法说明:
*
* 交易所exchange统一四个字节: 
* 
*  CFFEX-中金所 SHFE-上期所 DCE-大商所 CZCE-郑商所  SHSE-上交所  SZSE-深交所
*
* 订阅串有三节或四节组成, 格式：
*     交易所exchange.代码code.数据类型data_type.周期类型bar_type
*
* 只有订阅bar数据时, 才用到第四节, 周期类型才有作用
* 支持6种格式的订阅,使用如下: 
* CFFEX.*	    : 中金所,所有数据
* CFFEX.IF1403.*	: 中金所,IF1403,所有数据
* CFFEX.IF1403.tick   : 中金所,IF1403, tick数据
* CFFEX.IF1403.trade  : 中金所,IF1403, trade数据
* CFFEX.IF1403.bar.60: 中金所,IF1403, 1分钟(60秒)bar数据
* CFFEX.IF1403.*,CFFEX.IF1312.*	: 中金所,IF1403和IF1312所有数据(订阅多个代码)
*
* 退定规则, 和订阅的格式一样
```

调用示例:

```python
Subscribe('CFFEX.IF1406.tick, CFFEX.IF1406.bar.60');
```

- **Unsubscribe函数**

退订行情

函数原型：

```python
function [ ret ] = Unsubscribe( symbol_list )
```

参数说明及返回值说明：`同Subscribe`

调用示例: `同Subscribe`


##### 数据提取

- **GetTicks函数**

提取指定时间段的历史Tick数据，支持单个代码提取或多个代码组合提取。

函数原型：
```python
function [ ticks ] = GetTicks( symbol_list, begin_time, end_time )
```

参数说明及返回值说明：
```
* 提取期货Tick
* @param symbol_list 证券代码, 如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return tick数据列表
```

- **GetBars函数**

提取指定时间段的历史Bar数据，支持单个代码提取或多个代码组合提取。

函数原型：
```python
function [ bars ] = GetBars( symbol_list, bar_type, begin_time, end_time )
```

参数说明及返回值说明：
```
* 提取期货Bar
* @param symbol_list 证券代码, 如CFFEX.IF1308
* @param bar_type  bar周期，以秒为单位，比如60等与1分钟bar
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return bar数据列表
```

- **GetDailyBars函数**

提取指定时间段的历史日周期Bar数据，支持单个代码提取。DailyBar比Bar多了部分静态数据，如结算价，涨跌停等。

函数原型：
```python
function [ DailyBar ] = GetDailyBars( symbol_list, begin_time, end_time )
```

参数说明及返回值说明：
```
* 提取期货DailyBar
* @param symbol_list 证券代码, 如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
*
* @return DailyBar数据列表
```

- **GetLastTicks函数**

提取最新1条Tick数据，支持单个代码提取或多个代码组合提取, 只能提取每个代码最新的1条数据。

函数原型：

```python
function [ ticks ] = GetLastTicks( symbol_list )
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Tick
* @param symbol_list 多个证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @return tick数据列表
```

- **GetLastBars函数**

提取最新1条Bar数据，支持单个代码提取或多个代码组合提取, 只能提取每个代码最新的1条数据。

函数原型：

```python
function [ bars ] = GetLastBars( symbol_list, bar_type )
```

参数说明及返回值说明：
```
* 提取期货快照, 即最新的Bar
* @param symbol_list 多个证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @param bar_type bar类型，以秒为单位
* @return Bar数据列表
```

- **GetLastDailyBars函数**

提取最新1条DailyBar数据，支持单个代码提取或多个代码组合提取, 只能提取每个代码最新的1条数据。

函数原型：

```python
function [ DailyBar ] = GetLastDailyBars( symbol_list )
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Bar
* @param symbol_list 证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @return DailyBar数据列表
```

- **GetLastNTicks函数**

提取单个代码最新n条Tick数据。

函数原型：

```python
function [ ticks ] = GetLastNTicks( symbol , n)
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Tick
* @param symbol 证券代码, 如CFFEX.IF1308
* @param n 数据个数
* @return tick数据列表
```

- **GetLastNBars函数**

提取单个代码的最新n条Bar数据。

函数原型：

```python
function [ bars ] = GetLastNBars( symbol, bar_type, n)
```

参数说明及返回值说明：

```
* 提取最新的Bar
* @param symbol 证券代码, 如CFFEX.IF1308
* @param bar_type bar类型，以秒为单位, 如 60 表示1分钟分时K线
* @param n 数据个数
* @return Bar数据列表
```

- **GetLastDailyBars函数**

提取单个代码的最新n条DailyBar数据。

函数原型：

```python
function [ DailyBar ] = GetLastDailyBars( symbol_list )
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Bar
* @param symbol 证券代码, 如CFFEX.IF1308
* @param n 数据个数
* @return DailyBar数据列表
```

##### 交易接口

掘金SDK包含便利的下单，撤单，以及相关交易查询函数。

- **OpenLong函数**

开多仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
function [ order ] = OpenLong( exchange, sec_id, price, volume )
```

参数说明及返回值说明：

```
* 开多仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order table
```


- **OpenShort函数**

开空仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
function [ order ] = OpenShort( exchange, sec_id, price, volume )
```

参数说明及返回值说明：

```
* 开空仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order table
```

- **CloseLong函数**

平多仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
function [ order ] = CloseLong( exchange, sec_id, price, volume )
```

参数说明及返回值说明：

```
* 平多仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order table
```


- **CloseShort函数**

平空仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
function [ order ] = CloseShort( exchange, sec_id, price, volume )
```

参数说明及返回值说明：

```
* 平空仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```

- **PlaceOrder函数**

下单原生函数，需要创建Order对象，填充对应字段，一般建议使用上面4个快捷下单接口。

函数原型：

```python
function [ order ] = PlaceOrder( order_param )
```

参数说明及返回值说明：

```
* 平空仓
* @param order_param 委托下单参数结构。
* @return Order 委托下单生成的Order对象
```

order_param 必须包含以下字段:

```
order_param.exchange         交易所代码
order_param.sec_id           证券代码
order_param.position_effect  开/平仓标志, 参见常量定义PositionEffect
order_param.side             交易方向, 参见常量定义OrderSide
order_param.price            委托价
order_param.volume           委托量
order_param.order_type       委托类型, 参见常量定义OrderType
```

例如，以限价16.00开仓买入SZSE.000001 100股, 则

```python

o.exchange = 'SZSE';
o.sec_id = '000001';
o.position_effect = PositionEffect.Open;
o.side = OrderSide.Bid;
o.price = 16.00;
o.volume = 100;
o.order_type = OrderType.Limit;

PlaceOrder(o);
```


- **CancelOrder函数**

撤单，根据参数cl_ord_id指定的客户端订单ID，撤销之前的下单委托。取决于订单当前的状态，撤单可能成功，也可能被拒绝。

函数原型：

```python
function [ ret ] = CancelOrder( cl_ord_id )
```

参数说明及返回值说明：

```
* 撤单
* @param cl_ord_id  委托Order的客户端订单ID
* @return 返回0，执行成功，否则返回错误码。
*         cancel_order是异步请求，执行的结果由on_execution, on_order回调返回。
```

- **GetOrder函数**

查询委托订单，返回订单当前的最新状态，如果订单不存在，返回空矩阵。

函数原型：

```python
function [ order ] = GetOrder( cl_ord_id )
```

参数说明及返回值说明：

```
* 查单
* @param cl_ord_id  委托Order的客户端订单ID
* @return Order 查询到的order table 或 空矩阵
```


- **GetCash函数**

查询当前策略的资金信息。

函数原型：

```python
function [ cash ] = GetCash( )
```

参数说明及返回值说明：

```
* 查单
* @return Cash 当前策略的资金信息。
```


- **GetPosition函数**

查询属于当前策略的, 以参数指定的交易所代码和证券代码, 和买卖方向的持仓信息。

函数原型：

```python
function [ position ] = GetPosition( exchange, sec_id, side )
```

参数说明及返回值说明：
```
* 查持仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param side   买卖方向
* @return Position  持仓信息。
```

- **GetPositions函数**

查询当前策略的全部持仓信息。

函数原型：

```python
function [ positions ] = GetPositions(  )
```

参数说明及返回值说明：
```
* 查当前策略全部持仓
* @return 当前策略全部持仓列表。
```

##  注册Login, Bar, Tick，Trade, Error事件的处理函数

-   Login成功时回调方法

```c
    function [  ] = func( mask )
```
	+   参数	
		* mask: 1-行情服务器登录成功，2-交易服务器登录成功
		
	+   设置登录回调方法

```c
   	function [ ] = SetLoginHandle( handle )
```

    

-   Tick行情数据回调方法

```c
    function [  ] = func( tick )
```

	+   参数
    	*   tick       逐笔行情

	+   设置Tick行情回调方法

```c
    function [  ] = SetTickHandle(handle)
```


-   Bar行情回调方法

```c
    function [  ] = func( bar )
```

    +   参数 
        *   bar             分时行情      
    +   设置Bar行情回调方法

```c
    function [  ] = SetBarHandle(handle)
```

- 	Error回调方法

```c
    function [  ] = func( code, msg )
```

	+   参数
        * @param code    错误编号
        * @param msg     错误描述
	
	+   设置error回调方法
	
```c
	function [  ] = SetErrorHandle(handle)
```

- 	委托变化回调方法

```c
    function [  ] = func( order )
```

	+   参数
        * @param order    委托单状态
        	
	+   设置委托变化事件回调方法
	
```c
	function [  ] = SetOrderHandle(handle)
```

- 	执行回报回调方法

```c
    function [  ] = func( rpt )
```

	+   参数
        * @param rpt    执行回报
        	
	+   设置执行回报事件回调方法
	
```c
	function [  ] = SetExecRptHandle(handle)
```

## 掘金SDK使用示例

### 一个简单的策略示例

下面是一个matlab策略程序示例，主要展现如何初始化和运行过程，策略逻辑很简单，每个bar开仓或平仓, 每个tick打印最新成交价。这里为了演示，列出了全部的订单和回报事件处理示例供参考，实际编写策略时，未必需要处理这么多事件，请视需要处理。
注：gm.Init中的参数：账号/密码/策略ID(strategy_id)请替换为您自己的值

```python

function [  ] = TestStrategy(  )

%初始化平台
gm.Init('cloud.myquant.cn:8000', '', '', MDMode.MD_MODE_LIVE, 'CFFEX.IF1506.tick,CFFEX.IF1506.bar.30', '', '', 'cloud.myquant.cn:8001', 'e6208536-d688-11e4-9478-00163e003744');

%设置逐笔行情(Tick)处理函数
gm.SetTickHandle(@OnTick);

%设置分时行情(Bar)处理函数
gm.SetBarHandle(@OnBar);

%设置订单状态变化处理函数
gm.SetOrderHandle(@OnOrder);

%设置执行回报处理函数
gm.SetExecRptHandle(@OnExecRpt);

%设置登录gm服务器成功处理函数
gm.SetLoginHandle(@OnLogin);

%设置系统错误处理函数
gm.SetErrorHandle(@OnError);

%启动运行
gm.Run();

end

function [  ] = OnTick( tick )

%处理逐笔行情事件

x = sprintf('行情报价: %s,%s,%d', char(tick{1,'exchange'}), char(tick{1,'sec_id'}), tick{1,'last_price'});
disp(x);

end

function [  ] = OnBar( bar )

%处理分时行情

global f;
if isempty(f)
   f = 0; 
end

if f == 0
   x = sprintf('开多仓 : %s,%s,%d', char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'});
   disp(x);
   
   %开多仓
   gm.OpenLong(char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'}, 1);
   f = 1;
else
    x = sprintf('平多仓 : %s,%s,%d', char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'});
    disp(x);
    
    %平多仓
    gm.CloseLong(char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'}, 1);
    f = 0;
end

end

function [  ] = OnOrder( order )

   %处理委托单变化
   x = sprintf('委托 %s.%s 状态：%d', char(order{1, 'exchange'}), char(order{1 ,'sec_id'}), order{1, 'status'});
   disp(x);
   
end


function [  ] = OnExecRpt( rpt )

% 处理执行回报

x = sprintf('执行回报 类型: %d', rpt{1, 'exec_type'});
disp(x);

end

function [  ] = OnLogin( mask )

%服务器登录信息 mask=1 行情服务；mask=2 交易服务
if mask == 1
    disp('登录行情服务成功');
else
    disp('登录交易服务成功');
    
    %查询所有未结的委托
    orders = gm.GetUnfinishedOrdes();
    disp(orders);
    
    %查询资金状况
    cash = gm.GetCash();
    disp(cash);
    
    %查询持仓情况
    positions = gm.GetPositions();
    disp(positions);
end

end

function [  ] = OnError( code, msg )

%处理错误
x = sprintf('error: code = %d, msg = %s', code, msg);
disp(x);

end

```


### 更多示例

我们将陆续在github提供更丰富的策略示例程序，请猛击： `https://github.com/myquant/gmsdk`


## 联系我们
有任何问题，意见和建议，请随时联系我们。

```
地址： 深圳南山数字文化产业基地东塔2-61号
电话： 0775-86962125
邮件： support@myquant.cn
网址： www.myquant.cn
 QQ ：2319312574
```

深圳红树科技有限公司版权所有　　本文档最后更新于： `2015/4/9 13:24:52 `

