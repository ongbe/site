---
layout: docs
title: Python策略数据类型
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/python_type/
---


本节描述掘金SDK的关键数据类型，包括返回值，常量与枚举，交易数据，行情数据等数据类型定义。

### 返回值编码定义

掘金SDK中的所有API的返回值均统一定义，包括系统级错误和业务级错误。

返回值编码及注释如下, Python引用路径`gmsdk.errors`:

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
ERR_INTERNAL_INIT_ERROR                  = 1008  # "SDK内部初始化错误"
ERR_API_SERVER_CONNECT                   = 1009  # "无法连接掘金服务"

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

### 枚举常量定义

掘金SDK中的枚举和常量定义。枚举或常量用于定义各种状态和标识类型。

- OrderStatus(订单状态）, Python引用路径： `gmsdk.enums`

```python
    OrderStatus_New = 1
    OrderStatus_PartiallyFilled = 2
    OrderStatus_Filled = 3
    OrderStatus_DoneForDay = 4
    OrderStatus_Canceled = 5
    OrderStatus_PendingCancel = 6
    OrderStatus_Stopped = 7
    OrderStatus_Rejected = 8
    OrderStatus_Suspended = 9
    OrderStatus_PendingNew = 10
    OrderStatus_Calculated = 11
    OrderStatus_Expired = 12
    OrderStatus_AcceptedForBidding = 13
    OrderStatus_PendingReplace = 14
```

- OrderRejectReason（订单拒绝原因）, Python引用路径： `gmsdk.enums`

```python
    OrderRejectReason_UnknownReason = 1
    OrderRejectReason_RiskRuleCheckFailed = 2
    OrderRejectReason_NoEnoughCash = 3
    OrderRejectReason_NoEnoughPosition = 4
    OrderRejectReason_IllegalStrategyID = 5
    OrderRejectReason_IllegalSymbol = 6
    OrderRejectReason_IllegalVolume = 7
    OrderRejectReason_IllegalPrice = 8
    OrderRejectReason_NoMatchedTradingChannel = 9
    OrderRejectReason_AccountForbidTrading = 10
    OrderRejectReason_TradingChannelNotConnected = 11
    OrderRejectReason_StrategyForbidTrading = 12
    OrderRejectReason_NotInTradingSession = 13
    CancelOrderRejectReason_OrderFinalized = 101
    CancelOrderRejectReason_UnknownOrder = 102
    CancelOrderRejectReason_BrokerOption = 103
    CancelOrderRejectReason_AlreadyInPendingCancel = 104
```

- OrderSide（订单方向）, Python引用路径： `gmsdk.enums`

```python
    OrderSide_Bid = 1  # 多方向
    OrderSide_Ask = 2  # 空方向
```

- OrderType（订单类型）, Python引用路径： `gmsdk.enums`

```python
    OrderType_Market = 1     # 市价单
    OrderType_Limit = 2      # 限价单
```

- ExecType（订单执行回报类型）, Python引用路径： `gmsdk.enums`

```python
    ExecType_New = 1                # 交易所已接受订单
    ExecType_DoneForDay = 4
    ExecType_Canceled = 5           # 已撤
    ExecType_PendingCancel = 6      # 待撤
    ExecType_Stopped = 7            # 已停
    ExecType_Rejected = 8           # 已拒绝
    ExecType_Suspended = 9          # 暂停
    ExecType_PendingNew = 10        # 待接受
    ExecType_Calculated = 11        # 已折算
    ExecType_Expired = 12           # 过期
    ExecType_Restated = 13          # 重置
    ExecType_PendingReplace = 14    # 待修改
    ExecType_Trade = 15             # 交易
    ExecType_TradeCorrect = 16      # 交易更正
    ExecType_TradeCancel = 17       # 交易取消
    ExecType_OrderStatus = 18       # 更新订单状态
    ExecType_CancelRejected = 19    # 撤单被拒绝
```

- PositionEffect（开平仓类型）, Python引用路径： `gmsdk.enums.PositionEffect`

```python
    PositionEffect_Open = 1         # 开仓
    PositionEffect_Close = 2        # 平仓
```


### 交易数据类型

交易数据类型主要包括委托，执行回报，资金，持仓等数据类型。

- Order(委托订单）, Python引用路径： `gmsdk.message.Order`

```python
class Order(object):

	def __init__(self):
		self.strategy_id = ''                 # 策略ID
		self.account_id = ''                  # 交易账号
		
		self.cl_ord_id = ''                   # 客户端订单ID
		self.order_id = ''                    # 柜台订单ID
		self.ex_ord_id = ''                   # 交易所订单ID
		
		self.exchange = ''                    # 交易所代码
		self.sec_id = ''                      # 证券ID
		
		self.position_effect = 0              # 开平标志
		self.side = 0                         # 买卖方向
		self.order_type = 0                   # 订单类型
		self.order_src = 0                    # 订单来源
		self.status = 0                       # 订单状
		self.ord_rej_reason = 0               # 订单拒绝原因
		self.ord_rej_reason_detail = ''       # 订单拒绝原因描述

		self.price = 0.0                      # 委托价
		self.volume = 0.0                     # 委托量
		self.filled_volume = 0.0              # 已成交量
		self.filled_vwap = 0.0                # 已成交均价
		self.filled_amount = 0.0              # 已成交额

		self.sending_time = 0.0               # 委托下单时间
		self.transact_time = 0.0              # 最新一次成交时间
```


- ExecRpt(委托执行回报）, Python引用路径： `gmsdk.message.ExecRpt`

```python
class ExecRpt(object):

	def __init__(self):
		self.strategy_id = ''                 # 策略ID

		self.cl_ord_id = ''                   # 客户端订单ID
		self.order_id = ''                    # 交易所订单ID
		self.exec_id = ''                     # 订单执行回报ID

		self.exchange = ''                    # 交易所代码
		self.sec_id = ''                      # 证券ID
		
		self.position_effect = 0              # 开平标志
		self.side = 0                         # 买卖方向
		self.ord_rej_reason = 0               # 订单拒绝原因
		self.ord_rej_reason_detail = ''       # 订单拒绝原因描述
		self.exec_type = 0                    # 订单执行回报类型
		
		self.price = 0.0                      # 成交价
		self.volume = 0.0                     # 成交量
		self.amount = 0.0                     # 成交额
		
		self.transact_time = 0.0              # 交易时间
```

- Cash(资金）, Python引用路径： `gmsdk.message.Cash`

```python
class Cash(object):

	def __init__(self):
		self.strategy_id = ''           # 策略ID
		self.currency = 0               # 币种

		self.nav = 0.0                  # 资金余额
		self.fpnl = 0.0                 # 浮动收益
		self.pnl = 0.0                  # 净收益
        self.profit_ratio = 0.0         # 收益率
		self.frozen = 0.0               # 持仓冻结金额
        self.order_frozen = 0.0         # 挂单冻结金额
        self.available = 0.0            # 可用资金

		self.cum_inout = 0.0            # 累计出入金
		self.cum_trade = 0.0            # 累计交易额
		self.cum_pnl = 0.0              # 累计收益
		self.cum_commission = 0.0       # 累计手续费
		
		self.last_trade = 0.0           # 最新一笔交易额
		self.last_pnl = 0.0             # 最新一笔交易收益
		self.last_commission = 0.0      # 最新一笔交易手续费
		self.last_inout = 0.0           # 最新一次出入金
        self.change_reason = 0          # 变动原因
		
		self.transact_time = 0.0        # 交易时间

```

- Position(持仓）, Python引用路径： `gmsdk.message.Position`

```python
class Position(object):

	def __init__(self):
		self.strategy_id = ''           # 策略ID
		
		self.exchange = ''              # 交易所代码
		self.sec_id = ''                # 证券ID
		self.side = ''                  # 买卖方向
		self.volume = 0.0               # 持仓量
		self.amount = 0.0               # 持仓额
		self.vwap = 0.0                 # 持仓均价
		
        self.price = 0.0                # 当前行情价格
        self.fpnl = 0.0                 # 持仓浮动盈亏
        self.cost = 0.0                 # 持仓成本
        self.order_frozen = 0.0         # 挂单冻结仓位
        self.available = 0.0            # 可平仓位

		self.last_price = 0.0           # 上一笔成交价
		self.last_volume = 0.0          # 上一笔成交量
		self.init_time = 0.0            # 初始建仓时间
		self.transact_time = 0.0        # 上一仓位变更时间

```

### 行情数据类型

行情数据类型主要包括Tick，Bar，Trade, DailyBar


- Tick(逐笔行情数据）, Python引用路径： `gmsdk.message.Tick`

```python
class Tick(object):

	def __init__(self):
		self.exchange = ''          # 交易所代码
		self.sec_id = ''            # 证券ID
		self.utc_time = 0.0         # 行情时间戳
		
		self.last_price = 0.0       # 最新价
		self.open = 0.0             # 开盘价
		self.high = 0.0             # 最高价
		self.low = 0.0              # 最低价
		
		self.cum_volume = 0.0       # 成交总量/最新成交量,累计值
		self.cum_amount = 0.0       # 成交总金额/最新成交额,累计值
		self.cum_position = 0.0     # 合约持仓量(期),累计值
		self.last_volume = 0.0      # 瞬时成交量
		self.last_amount = 0.0      # 瞬时成交额

		self.upper_limit = 0.0      # 涨停价
		self.lower_limit = 0.0      # 跌停价
		self.settle_price = 0.0     # 今日结算价
		self.trade_type = 0         # (保留)交易类型,对应多开,多平等类型

		self.bids = []  # [(price, volume), (price, volume), ...] # 1-5档买价,量
		self.asks = []  # [(price, volume), (price, volume), ...] # 1-5档卖价,量
```

- Bar(各种周期的Bar数据）, Python引用路径： `gmsdk.message.Bar`

```python
class Bar(object):

	def __init__(self):
		self.exchange = ''       # 交易所代码
		self.sec_id = ''         # 证券ID

		self.bar_type = ''       # bar类型，以秒为单位，比如1分钟bar, bar_type=60
		self.bar_time = 0.0      # bar时间
		self.utc_time = 0.0      # 行情时间戳

		self.open = 0.0          # 开盘价
		self.high = 0.0          # 最高价
		self.low = 0.0           # 最低价
		self.close = 0.0         # 收盘价
		self.volume = 0.0        # 成交量
		self.amount = 0.0        # 成交额
```

- DailyBar(日频数据，在Bar数据的基础上，还包含结算价，涨跌停价等静态数据）, Python引用路径： `gmsdk.message.DailyBar`

```python

class Dailybar(object):

	def __init__(self):
		self.exchange = ''          # 交易所代码
		self.sec_id = ''            # 证券ID

		self.bar_type = ''          # bar类型
		self.bar_time = 0.0         # bar时间
		self.utc_time = 0.0         # 行情时间戳

		self.open = 0.0             # 开盘价
		self.high = 0.0             # 最高价
		self.low = 0.0              # 最低价
		self.close = 0.0            # 收盘价
		self.volume = 0.0           # 成交量
		self.amount = 0.0           # 成交额
		
		self.position = 0.0         # 仓位
		self.settle_price = 0.0     # 结算价
		self.upper_limit = 0.0      # 涨停价
		self.lower_limit = 0.0      # 跌停价
```
