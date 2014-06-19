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

返回值编码及注释如下, Python引用路径`gmsdk.errors`：

```python

# 共用的配置或通讯层面的错误 1000～1499 

SUCCESS                                  = 0     # "成功"

ERR_INVALID_URI                          = 1001  # "非法URI"
ERR_CORRUPT_FILE                         = 1002  # "非法文件或内容已损坏"
ERR_FILE_NOT_EXIST                       = 1003  # "文件不存在"
ERR_CONNECTION_FAILED                    = 1005  # "连接失败"
ERR_REQUEST_TIMEOUT                      = 1006  # "同步请求超时"
ERR_INVALID_PARAMETER                    = 1007  # "非法参数"
ERR_STRATEGY_INIT                        = 1008  # "策略未初始化"

# 业务层面错误码 共有部分， 1500～1999

ERR_INVALID_SYMBOL                       = 1501  # "非法代码"
ERR_INVALID_DATE                         = 1502  # "非法日期"

# 交易部分 2000 ~ 2999 

ERR_TRADE_CONNECT                        = 2000  # "交易服务连接失败"
ERR_TRADE_LOGIN                          = 2001  # "交易服务登录失败"
ERR_TRADE_TIMEOUT                        = 2002  # "交易命令超时"
ERR_TRADE_PROCESS_ERROR                  = 2003  # "命令处理内部错误"

ERR_TRADE_INVALID_SESSION                = 2011  # "没有登陆"
ERR_TRADE_PLACE_ORDER                    = 2012  # "下单错误"
ERR_TRADE_ALREADY_LOGIN                  = 2013  # "策略已登录"
ERR_TRADE_INVALID_PARAMETER              = 2014  # "非法参数"
ERR_TRADE_STRATEGY_LOCKED                = 2015  # "策略被禁止交易" 
ERR_TRADE_CONNECT_ALREADY_EXIST          = 2018  # "连接已经存在"


# 数据服务部分 3000～4999

ERR_MD_CONNECT                           = 3000  # "历史数据服务连接失败"
ERR_MD_LOGIN                             = 3001  # "历史数据服务登录失败"


# 期货历史行情数据服务 3100~3199 

ERR_MD_HIST_CONNECT                      = 3100  # "历史数据服务连接失败"
ERR_MD_HIST_LOGIN                        = 3101  # "历史数据服务登录失败"
ERR_MD_JIST_TIMEOUT                      = 3102  # "命令处理超时"
ERR_MD_HIST_PROCESS_ERROR                = 3103  # "命令处理内部错误"

ERR_MD_HIST_COMMUNICATE                  = 3111  # "数据服务通讯失败"
ERR_MD_HIST_NO_RESULT                    = 3112  # "该条件没查到数据"
ERR_MD_HIST_DOWNLOAD_FILE                = 3113  # "下载文件失败"
ERR_MD_HIST_DECOMPRESS                   = 3114  # "解压缩失败"
ERR_MD_HIST_FETCH_LOCAL_FILE             = 3115  # "读取本地文件内容失败"
ERR_MD_HIST_INVALID_PARAMETER            = 3116  # "请求参数错误，格式错误"
ERR_MD_HIST_RESP_DATA_COUNT              = 3118  # "返回数据量个数错误"
ERR_MD_HIST_BUFFER_ALLOC                 = 3119  # "分配缓冲区错误"


# 实时行情数据订阅服务 4000~4799

ERR_MD_LIVE_CONNECT                      = 4000  # "行情服务连接失败"
ERR_MD_LIVE_LOGIN                        = 4001  # "行情服务登录失败"
ERR_MD_LIVE_TIMEOUT                      = 4002  # "命令处理超时"
ERR_MD_LIVE_PROCESS_ERROR                = 4003  # "命令处理内部错误"

ERR_MD_LIVE_NOT_LOGIN                    = 4011  # "行情服务没有登录"
ERR_MD_LIVE_CONNECT_ALREADY_EXIST        = 4012  # "行情服务连接已经存在"
ERR_MD_LIVE_INVALID_PARAMETER            = 4013  # "非法订阅行情参数"

# 回放部分 4800~4999 

ERR_MD_INVALID_PLAYBACK_FILE             = 4801  # "行情数据文件不存在或格式错误"
ERR_MD_CORRUPT_PLAYBACK_FILE             = 4802  # "行情数据文件已损坏"

```

### 枚举常量定义

掘金SDK中的枚举和常量定义。枚举或常量用于定义各种状态和标识类型，与FIX协议的定义一致，可查阅FIX协议文档作为补充。

- OrderStatus(订单状态）, Python引用路径： `gmsdk.enums.OrderStatus`

```python
	class OrderStatus(object):
		New = '0'                           # 交易所已接受订单
		PartiallyFilled = '1'               # 部分成交
		Filled = '2'                        # 全部成交
		Canceled = '4'                      # 已撤
		PendingCancel = '6'                 # 待撤
		Rejected = '8'                      # 已拒绝
		Suspended = '9'                     # 已挂起
		PendingNew = 'A'                    # 待接受
		Expired = 'C'                       # 过期
```

- OrderRejectReason（订单拒绝原因）, Python引用路径： `gmsdk.enums.OrderRejectReason`

```python
	class OrderRejectReason(object):
		# 下单被拒绝原因
		UnknownReason = '0'                 # 未知原因
		NoEnoughCash = '1'                  # 资金不足
		NoEnoughPosition = '2'              # 仓位不足
		IllegalSymbol = '3'                 # 委托symbol错误
		IllegalVolume = '4'                 # 委托量错误
		IllegalPrice = '5'                  # 委托价错误
		StrategyForbidTrading = '6'         # 策略被禁止交易
		NoMatchedTradingChannel = '7'       # 没有匹配的交易通道
		NotInTradingTimespan = '8'          # 非交易时间
                                            
		# 撤单被拒绝原因        
		OrderFinalized = 'A'                # 订单已是最终状态
		UnknowOrder = 'B'                   # 未知订单
		BrokerOption = 'C'                  # 柜台拒绝
		AlreadyInPendingCancel = 'D'        # 重复撤单
```

- OrderSide（订单方向）, Python引用路径： `gmsdk.enums.OrderSide`

```python
	class OrderSide(object):

		""" order side from FIX, only buy, sell used by now """
		Bid = '2'    # 多方向
		Ask = '1'    # 空方向
```

- OrderType（订单类型）, Python引用路径： `gmsdk.enums.OrderType`

```python
	class OrderType(object):
		Market = '0'   # 市价单
		Limit = '1'    # 限价单
```

- ExecType（订单执行回报类型）, Python引用路径： `gmsdk.enums.ExecType`

```python
	class ExecType(object):
		New = '0'                # 交易所已接受订单
		Canceled = '4'           # 已撤
		PendingCancel = '6'      # 待撤
		Rejected = '8'           # 已拒绝
		PendingNew = 'A'         # 待接受
		Expired = 'C'            # 过期
		Trade = 'F'              # 交易
		CancelReject = 'J'       # 撤单被拒绝
```

- PositionEffect（开平仓类型）, Python引用路径： `gmsdk.enums.PositionEffect`

```python
	class PositionEffect(object):
		Open = '0'                  # 开仓
		Close = '1'                 # 平仓
```


### 交易数据类型

交易数据类型主要包括委托，执行回报，资金，持仓等数据类型。

- Order(委托订单）, Python引用路径： `gmsdk.proto.Order`

```python
	class Order(object):

		def __init__(self):
			self.broker_id = ''                                   # 经纪商ID
			self.account_id = ''                                  # 交易账号
			self.strategy_id = ''                                 # 策略ID
			self.cl_ord_id = ''                                   # 客户端订单ID
			self.order_id = ''                                    # 交易所订单ID
			self.symbol = ''                                      # symbol
			self.position_effect = PositionEffect.Open            # 开平标志
			self.side = OrderSide.Bid                             # 买卖方向
			self.order_type = OrderType.Limit                     # 订单类型
			self.order_src = OrderSrc.Strategy                    # 订单来源
			self.status = OrderStatus.PendingNew                  # 订单状
			self.ord_rej_reason = ''                              # 订单拒绝原因
			self.price = 0.0                                      # 委托价
			self.volume = 0.0                                     # 委托量
			self.filled_volume = 0.0                              # 已成交量
			self.filled_vwap = 0.0                                # 已成交均价
			self.filled_amount = 0.0                              # 已成交额
			self.sending_time = 0.0                               # 委托下单时间
			self.transact_time = 0.0                              # 最新一次成交时间
```


- Execution(委托执行回报）, Python引用路径： `gmsdk.proto.Execution`

```python
	class Execution(object):

		def __init__(self):
			self.strategy_id = ''           # 策略ID
			self.cl_ord_id = ''             # 客户端订单ID
			self.order_id = ''              # 交易所订单ID
			self.exec_id = ''               # 订单执行回报ID
			self.symbol = ''                # symbol
			self.ord_rej_reason = ''        # 订单拒绝原因
			self.position_effect = ''       # 开平标志
			self.side = ''                  # 买卖方向
			self.exec_type = ''             # 订单执行回报类型
			self.price = 0.0                # 成交价
			self.volume = 0.0               # 成交量
			self.amount = 0.0               # 成交额
			self.transact_time = 0.0        # 交易时间
```

- Cash(资金）, Python引用路径： `gmsdk.proto.Cash`

```python
	class Cash(object):

		def __init__(self):
			self.symbol = ''                # symbol
			self.currency = ''              # 币种

			self.balance = 0.0              # 资金余额
			self.frozen = 0.0               # 持仓冻结
			self.cum_inout = 0.0            # 累计出入金
			self.cum_trade = 0.0            # 累计交易额
			self.cum_pnl = 0.0              # 累计收益
			self.cum_commission = 0.0       # 累计手续费
			self.last_trade = 0.0           # 最新一笔交易额
			self.last_pnl = 0.0             # 最新一笔交易收益
			self.last_commission = 0.0      # 最新一笔交易手续费
			self.last_inout = 0.0           # 最新一次出入金
			self.transact_time = 0.0        # 交易时间

			self.strategy_id = ''           # 策略ID
```

- Position(持仓）, Python引用路径： `gmsdk.proto.Position`

```python
	class Position(object):

		def __init__(self):
			self.symbol = ''                # symbol
			self.side = ''                  # 买卖方向
			self.position_effect = ''       # 开平标志
			self.position_type=''           # 持仓类型
			self.volume = 0.0               # 持仓量
			self.amount = 0.0               # 持仓额
			self.vwap = 0.0                 # 持仓均价
			self.last_price = 0.0           # 上一笔成交价
			self.last_volume = 0.0          # 上一笔成交量
			self.init_time = 0.0            # 初始建仓时间
			self.transact_time = 0.0        # 上一笔成交时间
			self.strategy_id = ''           # 策略ID

```

### 行情数据类型

行情数据类型主要包括Tick，Bar，Trade, DailyBar


- Tick(逐笔行情数据）, Python引用路径： `gmsdk.proto.Tick`

```python
	class Tick(object):

		def __init__(self):
			self.symbol = ''            # symbol
			self.transact_time = 0.0    # 行情时间戳
			self.last_price = 0.0       # 最新价
			self.open = 0.0             # 开盘价
			self.high = 0.0             # 最高价
			self.low = 0.0              # 最低价
			self.last_volume = 0.0      # 瞬时成交额
			self.last_amount = 0.0      # 瞬时成交量(中金所提供)
			self.cum_volume = 0.0       # 成交总量/最新成交量,累计值
			self.cum_amount = 0.0       # 成交总金额/最新成交额,累计值
			self.cum_position = 0.0     # 合约持仓量(期),累计值
			self.settle_price = 0.0     # 今日结算价
			self.upper_limit = 0.0      # 涨停价
			self.lower_limit = 0.0      # 跌停价
			self.trade_type = 0         # (保留)交易类型,对应多开,多平等类型

			self.bids = []  # [(price, volume), (price, volume), ...] # 1-5档买价,量
			self.asks = []  # [(price, volume), (price, volume), ...] # 1-5档卖价,量
```

- Bar(各种周期的Bar数据）, Python引用路径： `gmsdk.proto.Bar`

```python
	class Bar(object):

		def __init__(self):
			self.symbol = ''         # symbol
			self.transact_time = 0.0 # 行情时间戳

			self.bar_type = ''       # bar类型，以秒为单位，比如1分钟bar, bar_type=60
			self.bar_time = 0.0      # bar时间
			self.open = 0.0          # 开盘价
			self.high = 0.0          # 最高价
			self.low = 0.0           # 最低价
			self.close = 0.0         # 收盘价
			self.prev_close = 0.0    # 昨收
			self.volume = 0.0        # 成交量
			self.amount = 0.0        # 成交额
```

- Trade(逐笔成交数据，不包含报价，仅当有成交时推送）, Python引用路径： `gmsdk.proto.Trade`

```python
	class Trade(object):

		def __init__(self):
			self.symbol = ''            # symbol
			self.transact_time = 0.0    # 行情时间戳

			self.last_price = 0.0       # 现价|最新成交价|今收盘价
			self.last_volume = 0.0      # 瞬时成交量(中金所提供)
			self.last_amount = 0.0      # 瞬时成交额
			self.cum_volume = 0.0       # 成交总量/最新成交量
			self.cum_amount = 0.0       # 成交总金额/最新成交额    
			self.trade_type = ''        # 交易类型,对应多开,多平等类型

```

- DailyBar(日频数据，在Bar数据的基础上，还包含结算价，涨跌停价等静态数据）, Python引用路径： `gmsdk.proto.DailyBar`

```python

	class Dailybar(object):

		def __init__(self):
			self.symbol = ''            # symbol
			self.transact_time = 0.0    # 行情时间戳

			self.bar_type = ''          # bar类型
			self.bar_time = 0.0         # bar时间
			self.open = 0.0             # 开盘价
			self.high = 0.0             # 最高价
			self.low = 0.0              # 最低价
			self.close = 0.0            # 收盘价
			self.prev_close = 0.0       # 昨收
			self.volume = 0.0           # 成交量
			self.amount = 0.0           # 成交额
			self.position = 0.0         # 仓位
			self.settle_price = 0.0     # 结算价
			self.upper_limit = 0.0      # 涨停价
			self.lower_limit = 0.0      # 跌停价
```