---
layout: docs
title: C#策略API介绍  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/csharp_api/
---


本节介绍关键的API接口，主要包括3个类：策略基类，行情服务，交易服务。策略基类封装了的完整的功能接口，便于策略编写时灵活的调取数据、下单和接收执行回报等。

另一方面，行情服务和交易服务接口也可以各自独立使用，比如单独使用实时行情，单独使用历史数据，单独回放历史行情，单独调用交易下单或查询接口等。

#### 测试服务地址

为方便策略开发和测试，以下服务在公网开放访问。真实交易时将配置切换到自己的服务地址即可，代码不用作任何修改，开发、测试和生产3个环境可以无缝迁移。地址如有变更，请查询官网通知: http://www.myquant.cn

```
行情服务连接字符串： md_addr=120.24.228.187:8000
交易服务连接字符串： td_addr=120.24.228.187:8001
```

#### GMSDK.MdApi(行情接口)

行情接口的封装类，在策略基类中使用，可分别提供实时，模拟，回放三种行情模式并同时提供历史行情的查询，也可以供研究分析时独立使用。

主要包括订阅、退订行情，连接/重连等接口，以及一系列回调事件，行情接口函数原型如下，具体说明见后面的策略类接口说明。

```c#
public class MdApi
{
    public int Init(
                string md_addr, 
                string username, 
                string password, 
                MDMode mode=MDMode.MD_MODE_NULL,
                string subscribe_symbols = "", 
                string start_time = "", 
                string end_time = "")
                
    public int Run()
    public void Close()
    public int Reconnect()
    
    // 订阅，退订行情
    public int Subscribe(string symbol_list)
    public int Unsubscribe(string symbol_list)

	/// 行情事件
    public virtual void OnTick(ref Tick tick);
    public virtual void OnBar(ref Bar bar);

    /// 其他事件
    public virtual void OnError(int error_code)
    public virtual void OnMdEvent(MarketDataEvent md_event);

    // 查询历史行情
    public List<Tick> GetTicks(string symbol_list, string begin_time, string end_time)
    public List<Bar> GetBars(string symbol_list, int bar_type, string begin_time, string end_time)
    public List<DailyBar> GetDailyBars(string symbol_list, string begin_time, string end_time)
    public List<Tick> GetLastTicks(string symbol_list)
    public List<Tick> GetLastNTicks(string symbol, int n = 1)
    public List<Bar> GetLastBars(string symbol_list, int bar_type)
    public List<Bar> GetLastNBars(string symbol, int bar_type, int n = 1)
    public List<DailyBar> GetLastDailyBars(string symbol_list)
    public List<DailyBar> GetLastNDailyBars(string symbol, int n = 1)
}
```

#### GMSDK.TdApi(交易接口)

交易接口的封装类，给策略提供方便的交易操作，方便下单、撤单，查资金，查持仓，订阅成交回报等事件。

以下是接口函数的原型定义，具体说明见下面策略代码的说明。

```c#
public class TdApi
{
    public int Init(
        string md_addr, 
        string username,
        string password,
        string strategy_id,
        string td_addr="" )
    
    public int Run()
    public void Close()
    public int Reconnect()

	/// 交易事件
    void OnExecRpt( ExecRpt rpt);
    public virtual void OnOrderNew(Order order);
    public virtual void OnOrderFilled(Order order);
    public virtual void OnOrderPartiallyFilled(Order order);
    public virtual void OnOrderStopExecuted(Order order);
    public virtual void OnOrderCancelled(Order order);
    public virtual void OnOrderCancelRejected(ExecRpt rpt);

    /// 其他事件
    public virtual void OnError(int error_code)
    
    /// 交易接口
    // 下单
    // 便捷接口
    public Order OpenLong(string exchange, string sec_id, double price, double volume)
    public Order OpenShort(string exchange, string sec_id, double price, double volume)
    public Order CloseLong(string exchange, string sec_id, double price, double volume)
    public Order CloseShort(string exchange, string sec_id, double price, double volume)
    
    // 基本接口
    public Order PlaceOrder(Order order)
    // 撤单
    public int CancelOrder(string cl_ord_id)
    
    // 查委托
    public Order GetOrder(string cl_ord_id)
    // 查资金
    public Cash GetCash()
    // 查特定证券及方向的持仓
    public Position GetPosition(string exchange, string sec_id, byte side)
    // 查所有持仓
    public List<Position> GetPositions()
}
```

#### GMSDK.StrategyBase(策略基类）

StrategyBase是策略的基类，编写策略时需要继承此类。

基类提供行情、委托执行回报、订单状态变更3类事件的回调虚函数，策略可以视需要改写这些函数，添加对应事件的响应代码和策略逻辑；

另外，基类通过内部成员行情服务接口MdApi, 交易服务接口TdApi实例，提供了行情订阅、历史数据提取和下单接口。下面逐一介绍。

##### StrategyBase接口定义

以下是StrategyBase类的完整接口：

```c#
public class StrategyBase
{
	/// 初始化策略
	public int Init(
            string md_addr,
            string td_addr,
            string username,
            string password,
            string strategy_id,
            string subscribe_symbols,
            MDMode mode = MDMode.MD_MODE_LIVE,
            string start_time = "",
            string end_time = ""
    );
	public int InitWithConfig(string config_file);

	/// 运行/停止策略
	public int Run();
	public void Stop();

	/// 行情订阅管理
    int Subscribe(string symbol_list);
    int Unsubscribe(string symbol_list);

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
    public virtual void OnTick(Tick tick);
    public virtual void OnBar(Bar bar);

	/// 交易事件
    public virtual void OnExecRpt(ExecRpt rpt);
    public virtual void OnOrderNew(Order order);
    public virtual void OnOrderFilled(Order order);
    public virtual void OnOrderPartiallyFilled(Order order);
    public virtual void OnOrderStopExecuted(Order order);
    public virtual void OnOrderCancelled(Order order);
    public virtual void OnOrderCancelRejected(ExecRpt rpt);

    /// 其他事件
    public virtual void OnError(int error_code)
    public virtual void OnMdEvent(MarketDataEvent md_event);

    /// 版本信息
    public string GetSdkVersion();
    /// 错误码的文本信息
    public string GetStrError(int err);
}
```

##### 初始化策略

- **Init 函数**

初始化策略，设置服务地址，账号密码，策略ID，以及需要订阅的symbol列表。策略自动连接服务，并登陆，订阅对应的行情，准备好运行。

函数原型：

```c#
public int Init(
            string md_addr,
            string td_addr,
            string username,
            string password,
            string strategy_id,
            string subscribe_symbols,
            MDMode mode = MDMode.MD_MODE_LIVE,
            string start_time = "",
            string end_time = "")
```


参数说明及返回值说明：

```
* 初始化GMSDK: 连接并登陆行情服务、交易服务
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

调用示例

创建策略对象，并初始化，订阅`CFFEX.IF1406`的`tick`和`60秒`分时数据。

```c#
var strategy = new MyStrategy();
strategy.Init(
	"120.24.228.187:8000",
	"120.24.228.187:8001",
	"your username",
	"your password",
	"your strategy_id",
	"CFFEX.IF1406.tick, CFFEX.IF1406.bar.60",
	3,
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
        md_addr='120.24.228.187:8000'
        td_addr='120.24.228.187:8001'
        username='your username'
        password='your password'
        strategy_id='your strategy_id'
        subscribe_symbols=CFFEX.IF1406.tick,CFFEX.IF1406.bar.60
        mode=3
        start_time=2014-08-11 00:00:00
        end_time=2014-08-12 00:00:00
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
    同上策略初始化部分说明一致。

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
List<Tick> GetTicks(string symbol, string begin_time, string end_time);
```

参数说明及返回值说明：
```
* 提取期货Tick
* @param symbol 证券代码, 带交易所代码以确保唯一，如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @param reserve_file 保留文件名(可以带目录,如d:\data\IF1312.tick或IF1312.tick), 如果为空, 不保留文件，默认为空
* @return List<Tick> tick数据列表
```

- **GetBars函数**

提取指定时间段的历史Bar数据，支持单个代码提取或多个代码组合提取。

函数原型：
```c#
List<Bar> GetBars(string symbol, int bar_type, string begin_time, string end_time);
```

参数说明及返回值说明：
```
* 提取期货Bar
* @param symbol 证券代码, 带交易所代码以确保唯一，如CFFEX.IF1308
* @param bar_type  bar周期，以秒为单位，比如60等与1分钟bar
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return List<Bar> bar数据列表
```

- **GetDailyBars函数**

提取指定时间段的历史日周期Bar数据，支持单个代码提取或多个代码组合提取。DailyBar比Bar多了部分静态数据，如结算价，涨跌停等。

函数原型：
```c#
List<DailyBar> GetDailyBars(string symbol, string begin_time, string end_time);
```

参数说明及返回值说明：
```
* 提取期货DailyBar
* @param symbol 证券代码, 带交易所代码以确保唯一，如CFFEX.IF1308
* @param begin_time 开始时间, 如2013-8-14 00:00:00
* @param end_time 结束时间, 如2013-8-15 00:00:00
* @return List<DailyBar> DailyBar数据列表
```

- **GetLastTicks函数**

提取最新n条Tick数据，支持单个代码提取或多个代码组合提取。如果单个代码，可以提取该代码最新的1-n条数据，如果提取多个组合代码，则只能提取每个组合代码最新的1条数据。

函数原型：
```c#
List<Tick> GetLastTicks(string symbol_list);
```

参数说明及返回值说明：
```
* 提取期货快照, 即最新的Tick
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @return List<Tick> tick数据列表
```

- **GetLastBars函数**

提取最新n条Bar数据，支持单个代码提取或多个代码组合提取。如果单个代码，可以提取该代码最新的1-n条数据，如果提取多个组合代码，则只能提取每个组合代码最新的1条数据。

函数原型：
```c#
List<Bar> GetLastBars(string symbol_list, int bar_type);
```

参数说明及返回值说明：
```
* 提取期货快照, 即最新的Bar
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @return List<Bar> Bar数据列表
```

- **GetLastDailyBars函数**

提取最新n条DailyBar数据，支持单个代码提取或多个代码组合提取。如果单个代码，可以提取该代码最新的1-n条数据，如果提取多个组合代码，则只能提取每个组合代码最新的1条数据。

函数原型：
```c#
List<DailyBar> GetLastDailyBars(string symbol_list);
```

参数说明及返回值说明：
```
* 提取期货快照, 即最新的Bar
* @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
* @return List<DailyBar> DailyBar数据列表
```

- **GetLastNTicks函数**

提取单个代码最新n条Tick数据

```c#
List<Tick> GetLastNTicks(string symbol, int n = 1)
```

- **GetLastNBars函数**

提取单个代码的最新n条Bar数据。

```c#
List<Bar> GetLastNBars(string symbol, int bar_type, int n = 1)
```

- **GetLastNDailyBars函数**

提取单个代码的最新n条DailyBar数据。

```C#
List<DailyBar> GetLastNDailyBars(string symbol, int n = 1)
```

##### 交易接口

掘金SDK包含便利的下单，撤单，以及相关交易查询函数。

- **OpenLong函数**

开多仓，以参数指定的symbol, 价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：
```c#
Order OpenLong(string exchange,   string sec_id,  double price, double volume);
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


- **OpenShort函数**

开空仓，以参数指定的exchange, 证券代码sec_id,  价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：
```c#
Order OpenShort(string exchange, string sec_id,  double price, double volume);
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

- **CloseLong函数**

平多仓，以参数指定的exchange, 证券代码sec_id,  价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：
```c#
Order CloseLong(string exchange, string sec_id,  double price, double volume);
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


- **CloseShort函数**

平空仓，以参数指定的exchange, 证券代码sec_id,  价和量下单。如果价格为0，为市价单，否则为限价单。

函数原型：
```c#
Order CloseShort(string exchange, string sec_id,  double price, double volume);
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
* @return 返回0，执行成功，否则返回错误码。
          
          CancelOrder是异步请求，执行的结果由OnExecRpt, OnOrderCancelled, OnOrderCancelRejected回调返回。
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

查询当前策略指定symbol（由交易所代码和证券ID组成）和买卖方向的持仓信息。

函数原型：
```c#
Position GetPosition(string exchange, string sec_id,  byte side);
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
public virtual void OnTick(Tick tick)
```

参数说明及返回值说明：
```
* @param tick Tick数据
```

- **OnBar函数**

响应Bar事件，收到Bar数据后本函数被调用。

函数原型：
```c#
 public virtual void OnBar(Bar bar)
```

参数说明及返回值说明：
```
* @param bar Bar数据
```

- **OnTrade函数**


响应Trade事件，收到Trade数据后本函数被调用。

函数原型：
```c#
 public virtual void OnTrade(Trade trade)
```

参数说明及返回值说明：
```
* @param trade Trade数据
```


##### 交易事件

- **OnExecRpt函数**


响应委托执行回报事件，收到ExecRpt数据后本函数被调用。

函数原型：
```c#
public virtual void OnExecRpt(ExecRpt rpt)
```

参数说明及返回值说明：
```
* @param ExecRpt rpt数据
```

- **OnOrderRejected函数**

响应订单`被拒绝`事件，收到Order变更数据后本函数被调用。

函数原型：
```c#
public virtual void OnOrderRejected(Order order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **OnOrderNew函数**

响应订单`被交易所接受`事件，收到Order变更数据后本函数被调用。

函数原型：
```c#
public virtual void OnOrderNew(Order order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **OnOrderFilled函数**

响应订单`完全成交`事件，收到Order变更数据后本函数被调用。

函数原型：
```c#
public virtual void OnOrderFilled(Order order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **OnOrderPartiallyFilled函数**

响应订单`部分成交`事件，收到Order变更数据后本函数被调用。

函数原型：
```c#
public virtual void OnOrderPartiallyFilled(Order order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **OnOrderStopExecuted函数**

响应订单`停止执行`事件，比如，限价单到收市仍然未能成交。收到Order变更数据后本函数被调用。

函数原型：
```c#
public virtual void OnOrderStopExecuted(Order order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **OnOrderCancelled函数**

响应订单`撤单成功`事件，收到Order变更数据后本函数被调用。

函数原型：
```c#
public virtual void OnOrderCancelled(Order order)
```

参数说明及返回值说明：
```
* @param order 最新的Order状态
```

- **OnOrderCancelRejected**

响应订单`撤单请求被拒绝`事件，收到ExecRpt数据后本函数被调用。`ord_rej_reason`说明为什么撤单失败。

函数原型：
```c#
public virtual void OnOrderCancelRejected(ExecRpt rpt)
```

参数说明及返回值说明：
```
* @param rpt 撤单失败的执行回报。
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
public virtual void OnMdEvent(MarketDataEvent md_event)
```

参数说明及返回值说明：
```
* @param md_event 开盘，收盘，回放行情结束等。
```
