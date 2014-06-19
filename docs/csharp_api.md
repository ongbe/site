---
layout: docs
title: C#策略API介绍  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/csharp_api/
---

本节介绍关键的API接口，主要包括5个类：策略基类，实时行情， 历史数据提取，历史行情回放，交易。策略基类汇集了其他4个类的功能接口，以便于策略编写时灵活的调取数据、下单和接收执行回报等。另一方面，以上几个类也可以各自独立使用，比如单独使用实时行情接口，单独使用历史数据接口，单独回放历史行情，单独调用交易下单或查询接口等。

#### 测试服务地址

为方便策略开发和测试，以下服务在公网开放访问。真实交易时将配置切换到自己的服务地址即可，代码不用作任何修改，开发、测试和生产3个环境可以无缝迁移。地址如有变更，请查询官网通知: http://www.hsgo.com.cn

```
实时行情服务: tcp://211.154.152.181:5103
模拟行情服务: tcp://211.154.152.181:5106
数据查询服务: tcp://211.154.152.181:5104
模拟交易服务: tcp://211.154.152.181:5050
```

#### GMSDK.Future.StrategyBase(策略基类）

StrategyBase是策略的基类，编写策略时需要继承此类。基类提供行情、委托执行回报、订单状态变更3类事件的回调虚函数，策略可以视需要改写这些函数，添加对应事件的响应代码和策略逻辑；另外，基类提供了行情订阅、历史数据提取和下单接口。下面逐一介绍。

##### StrategyBase接口定义

以下是StrategyBase类的完整接口：

```c#
public class StrategyBase : IMdLive, IMdQuery, ITradeLive
{
	/// 初始化策略
	public int Init(
	    string md_uri, 
	    string tr_uri, 
	    string query_uri, 
	    string username, 
	    string password, 
	    string strategy_id, 
	    string subscribe_symbols="");
	public int InitWithConfig(string config_file);

	/// 运行/停止策略
	public int Run();
	public void Stop();

	/// 行情订阅管理
    int Subscribe(string symbol_list);
    int Unsubscribe(string symbol_list);

	/// 历史数据提取
    List<Bar> GetBars(
					string symbol_list, 
					int bar_type, 
					string begin_time, 
					string end_time, 
					string reserve_file = "");
    List<DailyBar> GetDailyBars(
					string symbol_list, 
					string begin_time, 
					string end_time, 
					string reserve_file = "");
    List<Bar> GetLastBars(
					string symbol_list, 
					int bar_type, int n = 1);
    List<DailyBar> GetLastDailyBars(
					string symbol_list, 
					int n = 1);
    List<Tick> GetLastTicks(
					string symbol_list, 
					int n = 1);
    List<Tick> GetTicks(
					string symbol_list, 
					string begin_time, 
					string end_time, 
					string reserve_file = "");
    List<Tick> GetTicksByTimepoint(
					string symbol_list, 
					string timePoint, 
					int left_right = 0);
    List<Trade> GetTrades(
					string symbol_list, 
					string begin_time, 
					string end_time, 
					string reserve_file = "");

	/// 交易与交易查询
    Order OpenLong(string symbol, double price, double volume);
    Order OpenShort(string symbol, double price, double volume);
    Order CloseLong(string symbol, double price, double volume);
    Order CloseShort(string symbol, double price, double volume);
    Order PlaceOrder(Order order);
    int CancelOrder(string cl_ord_id);
    Cash GetCash();
    Order GetOrder(string cl_ord_id);
    Position GetPosition(string symbol, byte side);
    List<Position> GetPositions();

	/// 行情事件
    public virtual void OnTick(ref Tick tick);
    public virtual void OnBar(ref Bar bar);
    public virtual void OnTrade(ref Trade trade);
	
	/// 交易事件
    public virtual void OnExecution(ref Execution execution);
    public virtual void OnOrderNew(ref Order order);
    public virtual void OnOrderFilled(ref Order order);
    public virtual void OnOrderPartiallyFilled(ref Order order);
    public virtual void OnOrderStopExecuted(ref Order order);
    public virtual void OnOrderCancelled(ref Order order);
    public virtual void OnOrderCancelRejected(ref Execution execution);

    /// 其他事件
    public virtual void OnError(int error_code)
    public virtual void OnMdEvent(ref MarketDataEvent md_event);

    /// 版本信息
    public string get_version();
    /// 错误码的文本信息
    public string get_strerror(int err); 
}
```

##### 初始化策略

- **Init 函数**

初始化策略，设置服务地址，账号密码，策略ID，以及需要订阅的symbol列表。策略自动连接服务，并登陆，订阅对应的行情，准备好运行。

函数原型：

```c#
public int Init(
    string md_uri, 
    string tr_uri, 
    string query_uri, 
    string username, 
    string password, 
    string strategy_id, 
    string subscribe_symbols="")
```


参数说明及返回值说明：

```
* 初始化GMSDK: 连接并登陆实时行情服务、历史行情服务、交易服务
* @param md_uri       实时行情服务器uri, 比如 tcp://211.154.152.181:5103 或行情文件 file://your_path/tick.dat
* @param tr_uri       交易服务器uri, 比如 tcp://211.154.152.181:5050
* @param query_uri    数据提取与查询服务器uri, 比如 tcp://211.1.54.152.181:5104
* @param username     掘金账号
* @param password     掘金密码
* @param strategy_id  策略ID
* @param subscribe_symbols 行情订阅的代码列表
* @return 0：成功, 其他： error code(注：参见SDK错误代码定义文档，或头文件gm_error_code.h) 
```

行情订阅`symbol_list`的参数说明:

```
 
    symbol_list 订阅代码表,参数格式如下。
    
    参数格式：

    订阅串有三节或四节组成,用'.'分隔，分别对应交易所exchange.代码code.数据类型data_type.周期类型bar_type

    只有订阅bar数据时, 才用到第四节, 周期类型才有作用
    交易所exchange统一四个字节: CFFEX-中金所 SHFE-上期所 DCE-大商所 CZCE-郑商所

    支持6种格式的订阅,使用如下:

        *    CFFEX.IF1403.*      : 中金所,IF1403,所有数据
        *    CFFEX.IF1403.tick : 中金所,IF1403, tick数据
        *    CFFEX.IF1403.trade : 中金所,IF1403, trade数据
        *    CFFEX.IF1403.bar.60: 中金所,IF1403, 1分钟(60秒)Bar数据
        *    CFFEX.IF1403.*,CFFEX.IF1312.* : 中金所,IF1403和IF1312所有数据(订阅多个代码)
```

调用示例

创建策略对象，并初始化，订阅`CFFEX.IF1406`的`tick`和`60秒`分时数据。

```c#
var strategy = new MyStrategy();
strategy.Init(
	"tcp://211.154.152.181:5106",
	"tcp://211.154.152.181:5050",
	"tcp://211.154.152.181:5104",
	"your username",
	"your password",
	"your strategy_id",
	"CFFEX.IF1406.tick, CFFEX.IF1406.bar.60"
);
```

- **InitWithConfig 函数**

初始化策略，从配置文件中读取配置项。

函数原型：

```c#
public int InitWithConfig(string config_file)
```

参数说明及返回值说明：

```
* 初始化GMSDK，根据用户配置文件设定。
* @param config_file gmsdk配置文件名（全路径）
* @return 0：成功, 其他： error code(注：参见SDK错误代码定义文档，或头文件gm_error_code.h) 
```

config_file 配置文件的格式约定，比如`strategy_simple.ini`配置的内容：

```ini
;md_uri=playback://211.154.152.181:5109
;md_uri=file://SHFE.cu1403.tick
md_uri=tcp://211.154.152.181:5106
tr_uri=tcp://211.154.152.181:5050
query_uri=tcp://211.154.152.181:5104
username=your username
password=your password
strategy_id=your strategy id
subscribe_symbols=SHFE.cu1401.TICK,SHFE.cu1403.TICK
;playback_start_time='2014-04-08 09:00:00'
;playback_end_time='2014-04-10 16:00:00'
;playback_speed=100
```

调用示例:

```c#
var strategy = new MyStrategy();
strategy.InitWithConfig("strategy_simple.ini");
```

##### 运行/停止策略

- **Run 函数**

运行策略，直到用户关闭策略，或策略内部调用`Stop`函数。

函数原型：

```c#
public int Run();
```

参数说明及返回值说明：

```
* @return 0：成功, 其他： error code(注：参见SDK错误代码定义文档，或头文件gm_error_code.h) 
```

- **Stop 函数**


函数原型：

```c#
public void Stop();
```

参数说明及返回值说明：

```
无
```

##### 行情订阅管理

- **Subscribe函数**

订阅行情

函数原型：

```c#
int Subscribe(string symbol_list);
```

参数说明及返回值说明：

```
* 订阅行情
* @param symbol_list 订阅代码表,参考订阅规则
* @return 0:success 
*
* @note symbol_list 订阅规则和算法说明:
*
* 交易所exchange统一四个字节: CFFEX-中金所 SHFE-上期所 DCE-大商所 CZCE-郑商所
*
* 订阅串有三节或四节组成, 分别对应 交易所exchange.代码code.数据类型data_type.周期类型bar_type
* 只有订阅bar数据时, 才用到第四节, 周期类型才有作用
* 支持6种格式的订阅,使用如下: 
* CFFEX.IF1403.*	: 中金所,IF1403,所有数据
* CFFEX.IF1403.tick   : 中金所,IF1403, tick数据
* CFFEX.IF1403.trade  : 中金所,IF1403, trade数据
* CFFEX.IF1403.bar.60: 中金所,IF1403, 1分钟(60秒)bar数据
* 
* CFFEX.IF1403.*,CFFEX.IF1312.*	: 中金所,IF1403和IF1312所有数据(订阅多个代码)
*
* 退定规则, 和订阅的格式一样
```

调用示例:

```c#
strategy.Subscribe("CFFEX.IF1406.tick, CFFEX.IF1406.bar.60");
```

- **Unsubscribe函数**

退订行情

函数原型：

```c#
int Unsubscribe(string symbol_list);
```

参数说明及返回值说明：`同Subscribe`

调用示例: `同Subscribe`


##### 数据提取


- **GetTicks函数**

提取指定时间段的历史Tick数据，支持单个代码提取或多个代码组合提取。

函数原型：

```c#
List<Tick> GetTicks(string symbol_list, string begin_time, string end_time, string reserve_file = "");
```

参数说明及返回值说明：

```
* 提取期货Tick
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @param reserve_file 保留文件名(可以带目录,如d:\data\IF1312.tick或IF1312.tick), 如果为空, 不保留文件，默认为空
* @return List<Tick> tick数据列表
```

- **GetBars函数**

提取指定时间段的历史Bar数据，支持单个代码提取或多个代码组合提取。

函数原型：

```c#
List<Bar> GetBars(string symbol_list, int bar_type, string begin_time, string end_time, string reserve_file = "");
```

参数说明及返回值说明：

```
* 提取期货Bar
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param bar_type  bar周期，以秒为单位，比如60等与1分钟bar
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @param reserve_file 保留文件名(可以带目录,如d:\data\IF1312.tick或IF1312.tick), 如果为空, 不保留文件，默认为空
* @return List<Bar> bar数据列表
```

- **GetTrades函数**

提取指定时间段的历史Trade数据，支持单个代码提取或多个代码组合提取。

函数原型：

```c#
List<Trade> GetTrades(string symbol_list, string begin_time, string end_time, string reserve_file = "");
```

参数说明及返回值说明：

```
* 提取期货Trade
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @param reserve_file 保留文件名(可以带目录,如d:\data\IF1312.tick或IF1312.tick), 如果为空, 不保留文件，默认为空
* @return List<Trade> Trade数据列表
```

- **GetDailyBars函数**

提取指定时间段的历史日周期Bar数据，支持单个代码提取或多个代码组合提取。DailyBar比Bar多了部分静态数据，如结算价，涨跌停等。

函数原型：

```c#
List<DailyBar> GetDailyBars(string symbol_list, string begin_time, string end_time, string reserve_file = "");
```

参数说明及返回值说明：

```
* 提取期货DailyBar
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @param reserve_file 保留文件名(可以带目录,如d:\data\IF1312.tick或IF1312.tick), 如果为空, 不保留文件，默认为空
* @return List<DailyBar> DailyBar数据列表
```

- **GetLastTicks函数**

提取最新n条Tick数据，支持单个代码提取或多个代码组合提取。如果单个代码，可以提取该代码最新的1-n条数据，如果提取多个组合代码，则只能提取每个组合代码最新的1条数据。

函数原型：

```c#
List<Tick> GetLastTicks(string symbol_list, int n = 1);
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Tick
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param n 数据个数, 当n > 1 时, 只取第一只代码的数据
* @return List<Tick> tick数据列表
```

- **GetLastBars函数**

提取最新n条Bar数据，支持单个代码提取或多个代码组合提取。如果单个代码，可以提取该代码最新的1-n条数据，如果提取多个组合代码，则只能提取每个组合代码最新的1条数据。

函数原型：

```c#
List<Bar> GetLastBars(string symbol_list, int bar_type, int n = 1);
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Bar
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param n 数据个数, 当n > 1 时, 只取第一只代码的数据
* @return List<Bar> Bar数据列表
```

- **GetLastDailyBars函数**

提取最新n条DailyBar数据，支持单个代码提取或多个代码组合提取。如果单个代码，可以提取该代码最新的1-n条数据，如果提取多个组合代码，则只能提取每个组合代码最新的1条数据。

函数原型：

```c#
List<DailyBar> GetLastDailyBars(string symbol_list, int n = 1);
```

参数说明及返回值说明：

```
* 提取期货快照, 即最新的Bar
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param n 数据个数, 当n > 1 时, 只取第一只代码的数据
* @return List<DailyBar> DailyBar数据列表
```

- **GetTicksByTimepoint函数**

提取指定时间点的Tick数据，支持单个代码提取或多个代码组合提取。如果指定时间点没有数据，则根据left_right参数靠前或靠后提取最近一条数据，left_right默认为0，靠前提取。

函数原型：

```c#
List<Tick> GetTicksByTimepoint(string symbol_list, string timePoint, int left_right = 0);
```

参数说明及返回值说明：

```
* 提取期货固定时间点的快照
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @param timepoint    时间点,精确到秒, 如 如2013-8-14 00:00:00
* @return List<Tick> Tick数据列表
```

##### 交易接口

掘金SDK包含便利的下单，撤单，以及相关交易查询函数。

- **OpenLong函数**

开多仓，以参数指定的symbol, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```c#
Order OpenLong(string symbol, double price, double volume);
```

参数说明及返回值说明：

```
* 开多仓
* @param symbol 委托的symbol
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```


- **OpenShort函数**

开空仓，以参数指定的symbol, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```c#
Order OpenShort(string symbol, double price, double volume);
```

参数说明及返回值说明：

```
* 开空仓
* @param symbol 委托的symbol
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```

- **CloseLong函数**

平多仓，以参数指定的symbol, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```c#
Order CloseLong(string symbol, double price, double volume);
```

参数说明及返回值说明：

```
* 平多仓
* @param symbol 委托的symbol
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```


- **CloseShort函数**

平空仓，以参数指定的symbol, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：

```c#
Order CloseShort(string symbol, double price, double volume);
```

参数说明及返回值说明：

```
* 平空仓
* @param symbol 委托的symbol
* @param price  委托价，如果price=0,为市价单，否则为限价单。
* @param volume 委托量
* @return Order 委托下单生成的Order对象
```


- **PlaceOrder函数**


下单原生函数，需要创建Order对象，填充对应字段，一般建议使用上面4个快捷下单接口。如果价格price字段为0，为市价单，否则为限价单。

函数原型：

```c#
Order PlaceOrder(Order order);
```

参数说明及返回值说明：

```
* 委托下单
* @param order  委托Order对象
* @return Order 直接返回参数order对象
```

- **CancelOrder函数**

撤单，根据参数cl_ord_id指定的客户端订单ID，撤销之前的下单委托。取决于订单当前的状态，撤单可能成功，也可能被拒绝。

函数原型：

```c#
int CancelOrder(string cl_ord_id);
```

参数说明及返回值说明：

```
* 撤单
* @param cl_ord_id  委托Order的客户端订单ID
* @return 返回0，执行成功，否则返回错误码。CancelOrder是异步请求，执行的结果由OnExecution, OnOrderCancelled, OnOrderCancelRejected回调返回。
```

- **GetOrder函数**

查询委托订单，返回订单当前的最新状态，如果订单不存在，返回null。

函数原型：

```c#
Order GetOrder(string cl_ord_id);
```

参数说明及返回值说明：

```
* 查单
* @param cl_ord_id  委托Order的客户端订单ID
* @return Order 查询到的order对象或null.
```


- **GetCash函数**

查询当前策略的资金信息。

函数原型：

```c#
Cash GetCash();
```

参数说明及返回值说明：

```
* 查单
* @return Cash 当前策略的资金信息。
```


- **GetPosition函数**

查询当前策略指定symbol和买卖方向的持仓信息。

函数原型：

```c#
Position GetPosition(string symbol, byte side);
```

参数说明及返回值说明：

```
* 查持仓
* @param symbol symbol
* @param side   买卖方向
* @return Position  持仓信息。
```

- **GetPositions函数**

查询当前策略的全部持仓信息。

函数原型：

```c#
List<Position> GetPositions();
```

参数说明及返回值说明：

```
* 查当前策略全部持仓
* @return List<Position>  当前策略全部持仓列表。
```

##### 行情事件

响应行情事件的虚函数，改写这些函数添加策略逻辑。

- **OnTick函数**

响应Tick事件，收到Tick数据后本函数被调用。

函数原型：

```c#
public virtual void OnTick(ref Tick tick)
```

参数说明及返回值说明：

```
* @param tick Tick数据
```

- **OnBar函数**

响应Bar事件，收到Bar数据后本函数被调用。

函数原型：

```c#
 public virtual void OnBar(ref Bar bar)
```

参数说明及返回值说明：

```
* @param bar Bar数据
```

- **OnTrade函数**


响应Trade事件，收到Trade数据后本函数被调用。

函数原型：

```c#
 public virtual void OnTrade(ref Trade trade)
```

参数说明及返回值说明：

```
* @param trade Trade数据
```


##### 交易事件

- **OnExecution函数**


响应委托执行回报事件，收到Execution数据后本函数被调用。

函数原型：

```c#
public virtual void OnExecution(ref Execution execution)
```

参数说明及返回值说明：

```
* @param execution Execution数据
```

- **OnOrderRejected函数**

响应订单`被拒绝`事件，收到Order变更数据后本函数被调用。

函数原型：

```c#
public virtual void OnOrderRejected(ref Order order)
```

参数说明及返回值说明：

```
* @param order 最新的Order状态
```

- **OnOrderNew函数**

响应订单`被交易所接受`事件，收到Order变更数据后本函数被调用。

函数原型：

```c#
public virtual void OnOrderNew(ref Order order)
```

参数说明及返回值说明：

```
* @param order 最新的Order状态
```

- **OnOrderFilled函数**

响应订单`完全成交`事件，收到Order变更数据后本函数被调用。

函数原型：

```c#
public virtual void OnOrderFilled(ref Order order)
```

参数说明及返回值说明：

```
* @param order 最新的Order状态
```

- **OnOrderPartiallyFilled函数**

响应订单`部分成交`事件，收到Order变更数据后本函数被调用。

函数原型：

```c#
public virtual void OnOrderPartiallyFilled(ref Order order)
```

参数说明及返回值说明：

```
* @param order 最新的Order状态
```

- **OnOrderStopExecuted函数**

响应订单`停止执行`事件，比如，限价单到收市仍然未能成交。收到Order变更数据后本函数被调用。

函数原型：

```c#
public virtual void OnOrderStopExecuted(ref Order order)
```

参数说明及返回值说明：

```
* @param order 最新的Order状态
```

- **OnOrderCancelled函数**

响应订单`撤单成功`事件，收到Order变更数据后本函数被调用。

函数原型：

```c#
public virtual void OnOrderCancelled(ref Order order)
```

参数说明及返回值说明：

```
* @param order 最新的Order状态
```

- **OnOrderCancelRejected**

响应订单`撤单请求被拒绝`事件，收到Execution数据后本函数被调用。`Execution.ord_rej_reason`说明为什么撤单失败。

函数原型：

```c#
public virtual void OnOrderCancelRejected(ref Execution execution)
```

参数说明及返回值说明：

```
* @param execution 撤单失败的执行回报。
```

##### 其他事件

- **OnError函数**

响应`错误`事件，策略内部出现错误时，比如行情或交易连接断开，数据错误，超时等，将触发本函数。

函数原型：

```c#
public virtual void OnError(int error_code)
```

参数说明及返回值说明：

```
* @param error_code 错误编码，参考 GMSDK.ErrorCode 中的定义。
```


- **OnMdEvent函数**

响应`行情状态`事件，收到MarketDataEvent数据后本函数被调用。

函数原型：

```c#
public virtual void OnMdEvent(ref MarketDataEvent md_event)
```

参数说明及返回值说明：

```
* @param md_event 开盘，收盘，回放行情结束等。
```

#### GMSDK.Future.MdLive(实时行情订阅)

MdLive类是实时行情接口的封装类，可以独立使用。主要包括订阅、退订行情，连接/重连等接口，以及一系列回调事件。所有函数的详细说明请参考StrategyBase，不再重复描述。

MdLive类实现了Singleton模式，使用对象时直接调用 `MdLive.Instance` 即可，不用创建对象。

MdLive接口定义：

```c#
    public class MdLive : IMdLive
    {
		/// 访问MdLive对象实例
        public static MdLive Instance {get;}

		/// 行情事件
		public event MD_FutureTickCallback TickEvent;
        public event MD_FutureBarCallback BarEvent;
        public event MD_FutureTradeCallback TradeEvent;
        public event MD_FutureEventCallback MdEvent;
        public event MD_FutureSimpleErrorCallback ErrorEvent;

		/// 初始化
        public int Init(string md_uri, 
						string username = "", 
						string password = "", 
						string strategy_id = "", 
						string subscribe_symbols = "")
        /// 关闭连接
		public void Close();
		/// 重连
        public int Reconnect();

		/// 订阅/退订
        int Subscribe(string symbol_list);
        int Unsubscribe(string symbol_list);
```

#### GMSDK.Future.MdQuery(历史数据提取)

MdQuery类用于查询各种行情相关历史数据，可以独立使用。所有函数的详细说明请参考StrategyBase，不再重复描述。

MdQuery类实现了Singleton模式，使用对象时直接调用 `MdQuery.Instance` 即可，不用创建对象。

MdQuery接口定义：

```c#
    public class MdQuery : IMdQuery
    {
		/// 访问MdQuery对象实例
        public static MdQuery Instance {get;}


		/// 初始化
        public int Init(string query_uri, 
						string username, 
						string password, 
						string strategy_id);

		/// 查询接口
        List<Bar> GetBars(
						string symbol_list, 
						int bar_type, 
						string begin_time, 
						string end_time, 
						string reserve_file = "");
        List<DailyBar> GetDailyBars(
						string symbol_list, 
						string begin_time, 
						string end_time, 
						string reserve_file = "");
        List<Bar> GetLastBars(
						string symbol_list, 
						int bar_type, int n = 1);
        List<DailyBar> GetLastDailyBars(
						string symbol_list, 
						int n = 1);
        List<Tick> GetLastTicks(
						string symbol_list, 
						int n = 1);
        List<Tick> GetTicks(
						string symbol_list, 
						string begin_time, 
						string end_time, 
						string reserve_file = "");
        List<Tick> GetTicksByTimepoint(
						string symbol_list, 
						string timePoint, 
						int left_right = 0);
        List<Trade> GetTrades(
						string symbol_list, 
						string begin_time, 
						string end_time, 
						string reserve_file = "");
	}
```


#### GMSDK.Future.MdPlayback(历史行情回放)

MdPlayback是回放历史行情数据功能的封装，可以独立使用, 其中包含了两种类型的回放，一个是在线服务的回放，另一个是本地数据文件的回放。
本地文件回放参数设置很简单，只需要指定预先准备好的文件即可；在线回放服务的参数稍多一些，需要指定回放的URI包括服务器IP和通讯端口，
跟实时行情一样，需要指定需要的品种代码，另外，还需要指定回放数据的开始结束时间，回放速度。
MdPlayback继承自MdLive，因此接口与MdLive相同，区别在于MdLive回调返回的是实时行情，MdPlayback回调返回的历史行情中的数据。

Mdplayback另外多了一对函数： `start/stop` 用于启动和停止回放。

MdPlayback类实现了Singleton模式，使用对象时直接调用 `MdPlayback.Instance` 即可，不用创建对象

```c#
   public class MdPlayback : MdLive
    {
		public void Init(string file_uri)
		
        public void Init(
            string md_uri,
            string tr_uri,
            string query_uri,
            string username,
            string password,
            string strategy_id,
            string subscribe_symbols="",
            string playback_start_time="",
            string playback_end_time="",
            int playback_speed=100)
        public void Start()
        public void Stop()
	}
```

#### GMSDK.Future.TradeLive(交易接口)

TradeLive类封装了实时交易的接口，可以独立使用。交易接口详细说明见StrategyBase,此处不再重复描述。
TradeLive类实现了Singleton模式，使用对象时直接调用 `TradeLive.Instance` 即可，不用创建对象

TradeLive接口定义：

```c#
    public class TradeLive : ITradeLive
    {
		/// 访问TradeLive对象实例
        public static TradeLive Instance {get;}

		/// 交易事件
        public event TR_FutureExecutionCallback ExecutionEvent;
        public event TR_FutureOrderNewCallback OrderNewEvent;
        public event TR_FutureOrderFilledCallback OrderFilledEvent;
        public event TR_FutureOrderPartiallyFilledCallback OrderPartiallyFilledEvent;
        public event TR_FutureOrderStopExecutedCallback OrderStopExecutedEvent;
        public event TR_FutureOrderCancelledCallback OrderCancelledEvent;
        public event TR_FutureOrderCancelRejectedCallback OrderCancelRejectedEvent;
        public event TR_FutureSimpleErrorCallback ErrorEvent;

		/// 初始化
        public int Init(string tr_uri, 
						string username = "", 
						string password = "", 
						string strategy_id = "")
        /// 关闭连接
		public void Close();
		/// 重连
        public int Reconnect();

		/// 交易和相关查询
        Order OpenLong(string symbol, double price, double volume);
        Order OpenShort(string symbol, double price, double volume);
        Order CloseLong(string symbol, double price, double volume);
        Order CloseShort(string symbol, double price, double volume);
        Order PlaceOrder(Order order);
        int CancelOrder(string cl_ord_id);
        Cash GetCash();
        Order GetOrder(string cl_ord_id);
        Position GetPosition(string symbol, byte side);
        List<Position> GetPositions();
```
