---
layout: docs
title: Python策略API介绍  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/python_api/
---

本节介绍关键的API接口，主要包括5个类：策略基类，实时行情， 历史数据提取，历史行情回放，交易。策略基类汇集了其他4个类的功能接口，以便于策略编写时灵活的调取数据、下单和接收执行回报等。另一方面，以上几个类也可以各自独立使用，比如单独使用实时行情接口，单独使用历史数据接口，单独回放历史行情，单独调用交易下单或查询接口等。

#### 测试服务地址

为方便策略开发和测试，以下服务在公网开放访问。真实交易时将配置切换到自己的服务地址即可，代码不用作任何修改，开发、测试和生产3个环境可以无缝迁移。地址如有变更，请查询官网通知: http://www.hsgo.com.cn

```ini
行情服务连接字符串： md_addr=120.24.228.187:8000
交易服务连接字符串： td_addr=120.24.228.187:8001
```

使用时通过参数mode来确定实时行情、模拟行情、回放行情、或是数据提取模式。

StrategyBase是策略的基类，编写策略时需要继承此类。基类提供行情、委托执行回报、订单状态变更3类事件的回调虚函数，策略可以视需要改写这些函数，添加对应事件的响应代码和策略逻辑；另外，基类提供了行情订阅、历史数据提取和下单接口。下面逐一介绍。

##### StrategyBase接口定义

以下是StrategyBase类的完整接口：

```python
class StrategyBase(object):

	# 初始化策略

    def __init__(self,
                 md_addr='',
                 td_addr='',
                 username='',
                 password='',
                 strategy_id='',
                 subscribe_symbols='',
                 mode=2,
                 start_time='',
                 end_time='',
                 config_file=''):
        ...
        # 行情服务接口
        self.md
        # 交易服务接口
        self.td

	# 运行/停止策略
    def run(self)
    def stop(self)

	# 行情事件
    def on_tick(self, tick)
    def on_bar(self, bar)
    def on_trade(self, trade)
	
	# 交易事件
    def on_execution(self, execution)
    def on_order_new(self, order)
    def on_order_filled(self, order)
    def on_order_partially_filled(self, order)
    def on_order_stop_executed(self, order)
    def on_order_cancelled(self, order)
    def on_order_cancel_rejected(self, execution)

    # 其他事件
    def on_error(self, code)
    def on_md_event(self, event)

    # 取得错误码对应的文本信息
    def get_strerror(self, err_code)

    # 获得版本信息
    def get_version(self)

    # 代理接口信息, 方便直接调用行情接口和交易接口的函数，如：

    def get_last_ticks(self, symbol, begin_time, end_time)
    def open_long(self, exchange, sec_id, price, volume)
    ......
}
```

##### 初始化策略

- **__init__ 函数**

初始化策略，设置服务地址，账号密码，策略ID，以及需要订阅的symbol列表。策略自动连接服务，并登陆，订阅对应的行情，准备好运行。

函数原型：

```python
    def __init__(self,
                 md_addr='',
                 td_addr='',
                 username='',
                 password='',
                 strategy_id='',
                 subscribe_symbols='',
                 mode=2,
                 start_time='',
                 end_time='',
                 config_file=''):
```


参数说明及返回值说明：

```
* 初始化GMSDK: 连接并登陆实时行情服务、历史行情服务、交易服务
* @param md_addr       行情服务器uri, 比如 120.24.228.187:8000
* @param td_addr       交易服务器uri, 比如 120.24.228.187:8001
* @param username     掘金账号
* @param password     掘金密码
* @param strategy_id  策略ID
* @param subscribe_symbols 行情订阅的代码列表
* @param start_time   回放开始时间
* @param end_time     回放结束时间
* @param config_file  策略配置文件，如果config_file不为空，则从文件中读取配置初始策略；
                      如果为空，从以上参数中读取。
* @return 0：成功,
       其他： error code(注：参见SDK错误代码定义文档，或头文件gm_error_code.h)
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

config_file 配置文件的格式约定，比如`strategy_simple.ini`配置的内容：

```ini
[strategy]
md_addr=120.24.228.187:8000
td_addr=120.24.228.187:8001
username=1
password=1
strategy_id=strategy_1
mode=3
subscribe_symbols=*
start_time=2014-08-11 00:00:00
end_time=2014-08-12 00:00:00
```

调用示例（通过参数初始化策略）

创建策略对象，并初始化，订阅`CFFEX.IF1406`的`tick`和`60秒`分时数据。

```python
strategy = MyStrategy(
	md_addr="120.24.228.187:8000",
	td_addr="120.24.228.187:8001",
	username="your username",
	password="your password",
	strategy_id="your strategy_id",
	subscribe_symbols="CFFEX.IF1406.tick, CFFEX.IF1406.bar.60",
	mode=3,
    )
```

调用示例（通过配置文件初始化策略）:

```python
strategy = MyStrategy(config_file="strategy_simple.ini")
```

##### 运行/停止策略

- **run 函数**

运行策略，直到用户关闭策略，或策略内部调用`stop`函数。

函数原型：
```python
run()
```

参数说明及返回值说明：
```
* @return 0：成功, 其他： error code(注：参见SDK错误代码定义文档，或头文件gm_error_code.h) 
```

- **stop 函数**


函数原型：
```python
stop()
```

参数说明及返回值说明：
```
无
```

##### 行情订阅管理

- **subscribe函数**

订阅行情

函数原型：

```python
subscribe(symbol_list)
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
subscribe("CFFEX.IF1406.tick, CFFEX.IF1406.bar.60");
```

- **unsubscribe函数**

退订行情

函数原型：

```python
unsubscribe(symbol_list);
```

参数说明及返回值说明：`同subscribe`

调用示例: `同subscribe`


##### 数据提取

数据提取服务封装在类MdApi中，python 引用路径`gmsdk.api.MdApi`, 使用前需要通过init()进行初始化：

```python
    def init(self, 
             md_addr,       # 行情服务地址
             username,      # 掘金用户
             password,      # 掘金密码
             mode=1,        # 服务模式
             subscribe_symbols='',   #  订阅代码列表
             start_time='',          #  回放开始时间
             end_time='',            #  回放结束时间
             )
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


- **get_ticks函数**

提取指定时间段的历史Tick数据，支持单个代码提取或多个代码组合提取。

函数原型：
```python
def get_ticks(self, symbol, begin_time, end_time)
```

参数说明及返回值说明：
```
* 提取期货Tick
* @param symbol 证券代码, 如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return tick数据列表
```

- **get_bars函数**

提取指定时间段的历史Bar数据，支持单个代码提取或多个代码组合提取。

函数原型：
```python
def get_bars(self, symbol, bar_type, begin_time, end_time)
```

参数说明及返回值说明：
```
* 提取期货Bar
* @param symbol 证券代码, 如CFFEX.IF1308
* @param bar_type  bar周期，以秒为单位，比如60等与1分钟bar
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return bar数据列表
```

- **get_dailybars函数**

提取指定时间段的历史日周期Bar数据，支持单个代码提取。DailyBar比Bar多了部分静态数据，如结算价，涨跌停等。

函数原型：
```python
def get_dailybars(self, symbol, begin_time, end_time)
```

参数说明及返回值说明：
```
* 提取期货DailyBar
* @param symbol 证券代码, 如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
*
* @return DailyBar数据列表
```

- **get_last_ticks函数**

提取最新1条Tick数据，支持单个代码提取或多个代码组合提取, 只能提取每个代码最新的1条数据。

函数原型：

```python
def get_last_ticks(self, symbols)
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Tick
* @param symbol_list 多个证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @param n 数据个数, 当n > 1 时, 只取第一只代码的数据
* @return tick数据列表
```

- **get_last_bars函数**

提取最新1条Bar数据，支持单个代码提取或多个代码组合提取, 只能提取每个代码最新的1条数据。

函数原型：

```python
def get_last_bars(self, symbols, bar_type)
```

参数说明及返回值说明：
```
* 提取期货快照, 即最新的Bar
* @param symbols 多个证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @return Bar数据列表
```

- **get_last_dailybars函数**

提取最新1条DailyBar数据，支持单个代码提取或多个代码组合提取, 只能提取每个代码最新的1条数据。

函数原型：

```python
def get_last_dailybars(self, symbols)
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Bar
* @param symbol_list 证券代码列表, 如 'CFFEX.IF1308,CFFEX.1401,SHFE.AG1311'
* @return DailyBar数据列表
```

- **get_last_n_ticks函数**

提取单个代码最新n条Tick数据。

函数原型：

```python
def get_last_ticks(self, symbols)
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Tick
* @param symbol 证券代码, 如CFFEX.IF1308
* @param n 数据个数
* @return tick数据列表
```

- **get_last_n_bars函数**

提取单个代码的最新n条Bar数据。

函数原型：

```python
def get_last_n_bars(self, symbol, bar_type)
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Bar
* @param symbol 证券代码, 如CFFEX.IF1308
* @param n 数据个数
* @return Bar数据列表
```

- **get_last_n_dailybars函数**

提取单个代码的最新n条DailyBar数据。

函数原型：

```python
def get_last_n_dailybars(self, symbol)
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

- **open_long函数**

开多仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
def open_long(self, exchange, sec_id, price, volume)
```

参数说明及返回值说明：

```
* 开多仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```


- **open_short函数**

开空仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
def open_short(self, exchange, sec_id, price, volume)
```

参数说明及返回值说明：

```
* 开空仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```

- **close_long函数**

平多仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
def close_long(self, exchange, sec_id, price, volume)
```

参数说明及返回值说明：

```
* 平多仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```


- **close_short函数**

平空仓，以参数指定的交易所代码和证券代码, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```python
def close_short(self, exchange, sec_id, price, volume)
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


- **place_order函数**


下单原生函数，需要创建Order对象，填充对应字段，一般建议使用上面4个快捷下单接口。如果价格price字段为0，为市价单，否则为限价单。

函数原型：

```python
def place_order(self, order)
```

参数说明及返回值说明：

```
* 委托下单
* @param order  委托Order对象
* @return Order 直接返回参数order对象
```

- **cancel_order函数**

撤单，根据参数cl_ord_id指定的客户端订单ID，撤销之前的下单委托。取决于订单当前的状态，撤单可能成功，也可能被拒绝。

函数原型：

```python
def cancel_order(self, cl_ord_id)
```

参数说明及返回值说明：

```
* 撤单
* @param cl_ord_id  委托Order的客户端订单ID
* @return 返回0，执行成功，否则返回错误码。
*         cancel_order是异步请求，执行的结果由on_execution, on_order_cancelled, on_order_cancel_rejected回调返回。
```

- **get_order函数**

查询委托订单，返回订单当前的最新状态，如果订单不存在，返回null。

函数原型：

```python
def get_order(self, cl_ord_id)
```

参数说明及返回值说明：

```
* 查单
* @param cl_ord_id  委托Order的客户端订单ID
* @return Order 查询到的order对象或null.
```


- **get_cash函数**

查询当前策略的资金信息。

函数原型：

```python
def get_cash(self)
```

参数说明及返回值说明：

```
* 查单
* @return Cash 当前策略的资金信息。
```


- **get_position函数**

查询属于当前策略的, 以参数指定的交易所代码和证券代码, 和买卖方向的持仓信息。

函数原型：

```python
def get_position(self, exchange, sec_id, side)
```

参数说明及返回值说明：
```
* 查持仓
* @param exchange 交易所代码
* @param sec_id   证券代码
* @param side   买卖方向
* @return Position  持仓信息。
```

- **get_positions函数**

查询当前策略的全部持仓信息。

函数原型：

```python
def get_positions(self)
```

参数说明及返回值说明：
```
* 查当前策略全部持仓
* @return 当前策略全部持仓列表。
```

##### 行情事件

响应行情事件的虚函数，改写这些函数添加策略逻辑。

- **on_tick函数**

响应Tick事件，收到Tick数据后本函数被调用。

函数原型：
```python
def on_tick(self, tick)
```

参数说明及返回值说明：
```
* @param tick Tick数据
```

- **on_bar函数**

响应Bar事件，收到Bar数据后本函数被调用。

函数原型：
```python
def on_bar(self, bar)
```

参数说明及返回值说明：
```
* @param bar Bar数据
```


##### 交易事件

- **on_execution函数**


响应委托执行回报事件，收到成交回报 `ExecRpt` 数据后本函数被调用。

函数原型：
```python
def on_execrpt(self, execrpt)
```

参数说明及返回值说明：
```
* @param execrpt ExecRpt数据
```

- **on_order_rejected函数**

响应订单`被拒绝`事件，收到Order变更数据后本函数被调用。

函数原型：
```python
def on_order_rejected(self, order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **on_order_new函数**

响应订单`被交易所接受`事件，收到Order变更数据后本函数被调用。

函数原型：
```python
def on_order_new(self, order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **on_order_filled函数**

响应订单`完全成交`事件，收到Order变更数据后本函数被调用。

函数原型：
```python
def on_order_filled(self, order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **on_order_partially_filled函数**

响应订单`部分成交`事件，收到Order变更数据后本函数被调用。

函数原型：
```python
def on_order_partially_filled(self, order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **on_order_stop_executed函数**

响应订单`停止执行`事件，比如，限价单到收市仍然未能成交。收到Order变更数据后本函数被调用。

函数原型：
```python
def on_order_stop_executed(self, order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **on_order_cancelled函数**

响应订单`撤单成功`事件，收到Order变更数据后本函数被调用。

函数原型：
```python
def on_order_cancelled(self, order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **on_order_cancel_rejected函数**

响应订单`撤单请求被拒绝`事件，收到ExecRpt数据后本函数被调用。`ExecRpt.ord_rej_reason`说明为什么撤单失败。

函数原型：
```python
def on_order_cancel_rejected(self, execrpt)
```

参数说明及返回值说明：
```
* @param execrpt 撤单失败的执行回报。
```

##### 其他事件

- **on_error函数**

响应`错误`事件，策略内部出现错误时，比如行情或交易连接断开，数据错误，超时等，将触发本函数。

函数原型：
```python
def on_error(int error_code)
```

参数说明及返回值说明：
```
* @param error_code 错误编码，参考 gmsdk.error 中的定义。
```


- **on_md_event函数**

响应`行情状态`事件，收到MarketDataEvent数据后本函数被调用。

函数原型：
```python
def on_md_event(self, event):
```

参数说明及返回值说明：
```
* @param md_event 开盘，收盘，回放行情结束等。
```
