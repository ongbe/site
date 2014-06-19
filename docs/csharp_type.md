---
layout: docs
title: C#策略数据类型
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/csharp_type/
---


本节描述掘金SDK的关键数据类型，包括返回值，常量与枚举，交易数据，行情数据等数据类型定义。

### 返回值编码定义

掘金SDK中的所有API的返回值均统一定义，包括系统级错误和业务级错误。

C#引用路径：`class GMSDK.ErrorCode`。返回值编码及注释如下：

```c

/* 共用的配置或通讯层面的错误 1000～1499 */

#define SUCCESS                             0       // "成功"

#define ERR_INVALID_URI                     1001    // "非法URI"
#define ERR_CORRUPT_FILE                    1002    // "非法文件或内容已损坏"
#define ERR_FILE_NOT_EXIST                  1003    // "文件不存在"
#define ERR_CONNECTION_FAILED               1005    // "连接失败"
#define ERR_REQUEST_TIMEOUT                 1006    // "同步请求超时"
#define ERR_INVALID_PARAMETER               1007    // "非法参数"
#define ERR_STRATEGY_INIT                   1008    // "策略未初始化"

/* 业务层面错误码 共有部分， 1500～1999*/

#define ERR_INVALID_SYMBOL                  1501    // "非法代码"
#define ERR_INVALID_DATE                    1502    // "非法日期"

/* 交易部分 2000 ~ 2999 */ 

#define ERR_TRADE_CONNECT                   2000    // "交易服务连接失败"
#define ERR_TRADE_LOGIN                     2001    // "交易服务登录失败"
#define ERR_TRADE_TIMEOUT                   2002    // "交易命令超时" 
#define ERR_TRADE_PROCESS_ERROR             2003    // "命令处理内部错误"

#define ERR_TRADE_INVALID_SESSION           2011    // "没有登陆"
#define ERR_TRADE_PLACE_ORDER               2012    // "下单错误"
#define ERR_TRADE_ALREADY_LOGIN             2013    // "策略已登录"
#define ERR_TRADE_INVALID_PARAMETER         2014    // "非法参数" 
#define ERR_TRADE_STRATEGY_LOCKED           2015    // "策略被禁止交易" 
#define ERR_TRADE_CONNECT_ALREADY_EXIST     2018    // "连接已经存在"


/* 数据服务部分 3000～4999*/

#define ERR_MD_CONNECT                      3000    // "历史数据服务连接失败"
#define ERR_MD_LOGIN                        3001    // "历史数据服务登录失败"


/* 期货历史行情数据服务 3100~3199 */

#define ERR_MD_HIST_CONNECT                 3100    // "历史数据服务连接失败"
#define ERR_MD_HIST_LOGIN                   3101    // "历史数据服务登录失败"
#define ERR_MD_JIST_TIMEOUT                 3102    // "命令处理超时"
#define ERR_MD_HIST_PROCESS_ERROR           3103    // "命令处理内部错误"

#define ERR_MD_HIST_COMMUNICATE             3111    // "数据服务通讯失败"
#define ERR_MD_HIST_NO_RESULT               3112    // "该条件没查到数据"
#define ERR_MD_HIST_DOWNLOAD_FILE           3113    // "下载文件失败"
#define ERR_MD_HIST_DECOMPRESS              3114    // "解压缩失败"
#define ERR_MD_HIST_FETCH_LOCAL_FILE        3115    // "读取本地文件内容失败"
#define ERR_MD_HIST_INVALID_PARAMETER       3116    // "请求参数错误，格式错误"
#define ERR_MD_HIST_RESP_DATA_COUNT         3118    // "返回数据量个数错误"
#define ERR_MD_HIST_BUFFER_ALLOC            3119    // "分配缓冲区错误"


/* 实时行情数据订阅服务 4000~4799*/

#define ERR_MD_LIVE_CONNECT                 4000    // "行情服务连接失败"
#define ERR_MD_LIVE_LOGIN                   4001    // "行情服务登录失败"
#define ERR_MD_LIVE_TIMEOUT                 4002    // "命令处理超时"
#define ERR_MD_LIVE_PROCESS_ERROR           4003    // "命令处理内部错误"

#define ERR_MD_LIVE_NOT_LOGIN               4011    // "行情服务没有登录"
#define ERR_MD_LIVE_CONNECT_ALREADY_EXIST   4012    // "行情服务连接已经存在"
#define ERR_MD_LIVE_INVALID_PARAMETER       4013    // "非法订阅行情参数" 

/* 回放部分 4800~4999 */

#define ERR_MD_INVALID_PLAYBACK_FILE        4801    // "行情数据文件不存在或格式错误"
#define ERR_MD_CORRUPT_PLAYBACK_FILE        4802    // "行情数据文件已损坏"

```


### 枚举常量定义

掘金SDK中的枚举和常量定义。枚举或常量用于定义各种状态和标识类型，与FIX协议的定义一致，可查阅FIX协议文档作为补充。

- OrderStatus(订单状态）, C#引用路径： `class GMSDK.OrderStatus`

```c#
    public partial class OrderStatus
    {
        public static readonly byte New = Convert.ToByte('0');              // 交易所已接受订单
        public static readonly byte PartiallyFilled = Convert.ToByte('1');  // 部分成交
        public static readonly byte Filled = Convert.ToByte('2');           // 全部成交
        public static readonly byte Canceled = Convert.ToByte('4');         // 已撤
        public static readonly byte PendingCancel = Convert.ToByte('6');    // 待撤
        public static readonly byte Rejected = Convert.ToByte('8');         // 已拒绝
        public static readonly byte Suspended = Convert.ToByte('9');        // 已挂起
        public static readonly byte PendingNew = Convert.ToByte('A');       // 待接受
        public static readonly byte Expired = Convert.ToByte('C');          // 过期
    }
```

- OrderRejectReason（订单拒绝原因）, C#引用路径： `class GMSDK.OrderRejectReason`

```c#
    public partial class OrderRejectReason
    {
		/// 下单被拒绝原因
        public static readonly byte UnknownReason = Convert.ToByte('0');           // 未知原因
        public static readonly byte NoEnoughCash = Convert.ToByte('1');            // 资金不足
        public static readonly byte NoEnoughPosition = Convert.ToByte('2');        // 仓位不足
        public static readonly byte IllegalSymbol = Convert.ToByte('3');           // 委托symbol错误
        public static readonly byte IllegalVolume = Convert.ToByte('4');           // 委托量错误
        public static readonly byte IllegalPrice = Convert.ToByte('5');            // 委托价错误
        public static readonly byte StrategyForbidTrading = Convert.ToByte('6');   // 策略被禁止交易
        public static readonly byte NoMatchedTradingChannel = Convert.ToByte('7'); // 没有匹配的交易通道
        public static readonly byte NotInTradingTimespan = Convert.ToByte('8');    // 非交易时间

		/// 撤单被拒绝原因
        public static readonly byte OrderFinalized = Convert.ToByte('A');          // 订单已是最终状态
        public static readonly byte UnknowOrder = Convert.ToByte('B');             // 未知订单
        public static readonly byte BrokerOption = Convert.ToByte('C');            // 柜台拒绝
        public static readonly byte AlreadyInPendingCancel = Convert.ToByte('D');  // 重复撤单
    }
```

- OrderSide（订单方向）, C#引用路径： `class GMSDK.OrderSide`

```c#
    public partial class OrderSide
    {
        public static readonly byte Ask = Convert.ToByte('1');  // 空方向
        public static readonly byte Bid = Convert.ToByte('2');  // 多方向
    }
```

- OrderType（订单类型）, C#引用路径： `class GMSDK.OrderType`

```c#
    public partial class OrderType
    {
        public static readonly byte Market = Convert.ToByte('0');  // 市价单
        public static readonly byte Limit = Convert.ToByte('1');   // 限价单
    }
```

- ExecType（订单执行回报类型）, C#引用路径： `class GMSDK.ExecType`

```c#
    public partial class ExecType
    {
        public static readonly byte New = Convert.ToByte('0');           // 交易所已接受订单
        public static readonly byte Canceled = Convert.ToByte('4');      // 已撤
        public static readonly byte PendingCancel = Convert.ToByte('6'); // 待撤
        public static readonly byte Rejected = Convert.ToByte('8');      // 已拒绝
        public static readonly byte PendingNew = Convert.ToByte('A');    // 待接受
        public static readonly byte Expired = Convert.ToByte('C');       // 过期
        public static readonly byte Trade = Convert.ToByte('F');         // 交易
        public static readonly byte CancelReject = Convert.ToByte('J');  // 撤单被拒绝
    }
```

- PositionEffect（开平仓类型）, C#引用路径： `class GMSDK.PositionEffect`

```c#
    public partial class PositionEffect
    {
        public static readonly byte Open = Convert.ToByte('0');  // 开仓
        public static readonly byte Close = Convert.ToByte('1'); // 平仓
    }
```


### 交易数据类型

交易数据类型主要包括委托，执行回报，资金，持仓等数据类型。

- Order(委托订单）, C#引用路径： `class GMSDK.Order`

```c#
    public struct Order
    {
        /// 经纪商ID
        public string broker_id;
        /// 交易账号
        public string account_id;
        /// symbol
        public string symbol;
        /// 委托价
        public double price;
        /// 委托量
        public double volume;
        /// 开平标志
        public byte position_effect;
        /// 买卖方向
        public byte side;
        /// 策略ID
        public string strategy_id;
        /// 客户端订单ID
        public string cl_ord_id;
        /// 交易所订单ID
        public string order_id;
        /// 订单类型
        public byte order_type;
        /// 订单状态
        public byte status;
        /// 订单拒绝原因
        public byte ord_rej_reason;
        /// 已成交量
        public double filled_volume;
        /// 已成交均价
        public double filled_vwap;
        /// 已成交额
        public double filled_amount;
        /// 委托下单时间
        public double sending_time;
        /// 最新一次成交时间
        public double transact_time;
    }

```


- Execution(委托执行回报）, C#引用路径： `class GMSDK.Execution`

```c#
    public struct Execution
    {
        /// 策略ID
        public string strategy_id;
        /// 客户端订单ID
        public string cl_ord_id;
        /// 交易所订单ID
        public string order_id;
        /// 订单执行回报ID
        public string exec_id;
        /// 订单执行回报类型
        public byte exec_type;
        /// 订单拒绝原因
        public byte ord_rej_reason;
		/// symbol
        public string symbol;
        /// 成交价
        public double price;
        /// 成交量
        public double volume;
        /// 成交额
        public double amount;
        /// 开平标志
        public byte position_effect;
        /// 买卖方向
        public byte side;
        /// 交易时间
        public double transact_time;
    }
```

- Cash(资金）, C#引用路径： `class GMSDK.Cash`

```c#
    public struct Cash
    {
        /// symbol
         public string symbol;
        /// 币种
        public byte currency;
        /// 资金余额
        public double balance;
        /// 持仓冻结
        public double frozen;
        /// 累计出入金
        public double cum_inout;
        /// 策略ID
        public string strategy_id;
    }
```

- Position(持仓）, C#引用路径： `class GMSDK.Position`

```c#
    public struct Position
    {
        /// symbol
        public string symbol;
        /// 买卖方向
        public byte side;
        /// 开平标志
        public byte position_effect;
        /// 持仓类型
        public byte position_type;
        /// 持仓量
        public double volume;
        /// 持仓均价
        public double vwap;
        /// 持仓额
        public double amount;
        /// 上一笔成交价
        public double last_price;
        /// 上一笔成交量
        public double last_volume;
        /// 上一笔成交时间
        public double transact_time;
        /// 初始建仓时间
        public double init_time;
        /// 策略ID
        public string strategy_id;
    }

```

### 行情数据类型

行情数据类型主要包括Tick，Bar，Trade, DailyBar


- MarketDataEvent(行情数据时间）, C#引用路径： `class GMSDK.MarketDataEvent`

```c#
    public struct MarketDataEvent
    {
        /// double
        public double utc_time;
        /// 事件类型(1:开盘  2:收盘, 3:回放结束)
        public int event_type;
    }
```

- Tick(逐笔行情数据）, C#引用路径： `class GMSDK.Future.Tick`

```c#
    public struct Tick
    {
        /// 交易所
        public string exchange;
        /// 合约ID
        public string sec_id;
        /// symbol
        public string symbol
        {
            get { return string.Format("{0}.{1}", exchange, sec_id); }
        }
        /// 行情时间，精确到秒
        public System.Int64 trade_time;
        /// 行情时间，毫秒部分
        public int milli_sec;
        /// 最新价
        public float last_price;
        /// 开盘价
        public float open;
        /// 最高价
        public float high;
        /// 最低价
        public float low;
        /// 1-5档买价
        public float bid_p1;
        public float bid_p2;
        public float bid_p3;
        public float bid_p4;
        public float bid_p5;
        /// 1-5档买量
        public int bid_v1;
        public int bid_v2;
        public int bid_v3;
        public int bid_v4;
        public int bid_v5;
        /// 1-5档卖价
        public float ask_p1;
        public float ask_p2;
        public float ask_p3;
        public float ask_p4;
        public float ask_p5;
        /// 1-5档卖量
        public int ask_v1;
        public int ask_v2;
        public int ask_v3;
        public int ask_v4;
        public int ask_v5;
        /// <成交总量/最新成交量,累计值
        public double cum_volume;
        /// <成交总金额/最新成交额,累计值
        public double cum_amount;
        /// <合约持仓量(期),累计值
        public System.Int64 cum_position;
        /// <瞬时成交额
        public double last_amount;
        /// <瞬时成交量(中金所提供)
        public int last_volume;
        /// 涨停价
        public float upper_limit;
        /// 跌停价
        public float lower_limit;
        /// 今日结算价
        public float settle_price;
        /// (保留)交易类型,对应多开,多平等类型
        public int trade_type;
        /// <UTC时间戳
        public double utc_time;
    }
```

- Bar(各种周期的Bar数据）, C#引用路径： `class GMSDK.Future.Bar`

```c#
    public struct Bar
    {
        /// bar类型，以秒为单位，比如1分钟bar, bar_type=60
        public int bar_type;
        /// 交易所
        public string exchange;
        /// 合约ID
        public string sec_id;
        /// symbol
        public string symbol
        {
            get { return string.Format("{0}.{1}", exchange, sec_id); }
        }
        /// bar时间
        public System.Int64 bar_time;
        /// 开盘价
        public float open;
        /// 收盘价
        public float close;
        /// 最高价
        public float high;
        /// 最低价
        public float low;
        /// 昨收
        public float pre_close;
        /// 成交量
        public double volume;
        /// 成交额
        public double amount;
        /// utc时间戳
        public double utc_time;
    }
```

- Trade(逐笔成交数据，不包含报价，仅当有成交时推送）, C#引用路径： `class GMSDK.Future.Trade`

```c#
    public struct Trade
    {
        /// <交易所代码
        public string exchange;
        /// <证券ID
        public string sec_id;

        public string symbol
        {
            get { return string.Format("{0}.{1}", exchange, sec_id); }
        }
        /// <整型日期时间[20131108142033],精确到秒   
        public System.Int64 trade_time;
        /// <最后修改毫秒
        public int milli_sec;
        /// <现价|最新成交价|今收盘价
        public float last_price;
        /// <成交总量/最新成交量
        public double cum_volume;
        /// <成交总金额/最新成交额    
        public double cum_amount;
        /// <瞬时成交额
        public double last_amount;
        /// <瞬时成交量(中金所提供)
        public int last_volume;
        /// <交易类型,对应多开,多平等类型
        public int trade_type;
        /// <UTC时间戳
        public double utc_time;
    }
```

- DailyBar(日频数据，在Bar数据的基础上，还包含结算价，涨跌停价等静态数据）, C#引用路径： `class GMSDK.Future.DailyBar`

```c#
    public struct DailyBar
    {
        /// bar类型
        public int bar_type;
        /// 交易所
        public string exchange;
        /// 合约ID
        public string sec_id;
        /// symbol
        public string symbol
        {
            get { return string.Format("{0}.{1}", exchange, sec_id); }
        }
        /// bar时间
        public System.Int64 bar_time;
        /// 开盘价
        public float open;
        /// 收盘价
        public float close;
        /// 最高价
        public float high;
        /// 最低价
        public float low;
        /// 成交量
        public double volume;
        /// 成交额
        public double amount;
        /// 仓位
        public System.Int64 position;
        /// 昨收
        public float pre_close;
        /// 结算价
        public float settle_price;
        /// 涨停价
        public float upper_limit;
        /// 跌停价
        public float lower_limit;
        /// utc时间戳
        public double utc_time;
    }
```