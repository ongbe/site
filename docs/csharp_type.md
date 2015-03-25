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

```c#

public partial class ErrorCode
{

    public const int SUCCESS                        = 0;       // "成功"
    
    public const int ERR_CONFIG_FILE_NOT_EXIST      = 1001;    // "策略配置文件不存在"
    public const int ERR_CONFIG_PARSE               = 1002;    // "策略配置文件格式错误"
    public const int ERR_AUTH_CONNECT               = 1003;    // "无法连接掘金认证服务"
    public const int ERR_AUTH_LOGIN                 = 1004;    // "无法登录掘金认证服务"
    public const int ERR_REQUEST_TIMEOUT            = 1005;    // "请求超时"
    public const int ERR_INVALID_PARAMETER          = 1006;    // "非法参数"
    public const int ERR_STRATEGY_INIT              = 1007;    // "策略未初始化"
    public const int ERR_INTERNAL_INIT_ERROR        = 1008;    // "SDK内部初始化错误"
    public const int ERR_API_SERVER_CONNECT         = 1009;    // "无法连接掘金API服务"
    
    /* 业务层面错误码 共有部分， 1500～1999*/
    
    public const int ERR_INVALID_SYMBOL             = 1501;    // "非法证券代码"
    public const int ERR_INVALID_DATE               = 1502;    // "非法日期格式"
    
    /* 交易部分 2000 ~ 2999 */ 
    
    public const int ERR_TD_CONNECT                 = 2000;    // "交易服务连接失败"
    public const int ERR_TD_LOGIN                   = 2001;    // "交易服务登录失败"
    public const int ERR_TD_TIMEOUT                 = 2002;    // "交易命令请求超时" 
    public const int ERR_TD_NO_RESULT               = 2003;    // "该条件没查到数据"
    public const int ERR_TD_INVALID_SESSION         = 2004;    // "交易请求没有登陆"
    public const int ERR_TD_INVALID_PARAMETER       = 2005;    // "交易请求参数非法" 
    public const int ERR_TD_STRATEGY_LOCKED         = 2006;    // "策略被禁止交易" 
    public const int ERR_TD_SERVER_ERROR            = 2007;    // "交易服务内部错误" 
    public const int ERR_TD_CORRUPT_DATA            = 2008;    // "返回数据包错误" 
    
    
    /* 数据服务部分 3000～3999*/
    
    public const int ERR_MD_CONNECT                 = 3000;    // "数据服务连接失败"
    public const int ERR_MD_LOGIN                   = 3001;    // "数据服务登录失败"
    public const int ERR_MD_TIMEOUT                 = 3002;    // "数据服务请求超时"

    public const int ERR_MD_NO_RESULT               = 3003;    // "该条件没查到数据"
    public const int ERR_MD_BUFFER_ALLOC            = 3005;    // "分配缓冲区错误"
    public const int ERR_MD_INVALID_PARAMETER       = 3006;    // "数据请求参数非法" 
    public const int ERR_MD_SERVER_ERROR            = 3007;    // "数据服务内部错误" 
    public const int ERR_MD_CORRUPT_DATA            = 3008;    // "返回数据包错误"
}
```


### 枚举常量定义

掘金SDK中的枚举和常量定义。枚举或常量用于定义各种状态和标识类型，与FIX协议的定义一致，可查阅FIX协议文档作为补充。

- OrderStatus(订单状态）, C#引用路径： `class GMSDK.OrderStatus`

```c#
    public enum OrderStatus
    {
        New = 1;              // 交易所已接受订单
        PartiallyFilled = 2;  // 部分成交
        Filled = 3;           // 全部成交
        Canceled = 5;         // 已撤
        PendingCancel = 6;    // 待撤
        Rejected = 8;         // 已拒绝
        Suspended = 9;        // 已挂起
        PendingNew = 10;      // 待接受
        Expired = 12;         // 过期
    }
```

- OrderRejectReason（订单拒绝原因）, C#引用路径： `class GMSDK.OrderRejectReason`

```c#
    public enum OrderRejectReason
    {
		/// 下单被拒绝原因
        UnknownReason = 1;           // 未知原因
        RiskRuleCheckFailed = 2;     // 风控检查不通过
        NoEnoughCash = 3;            // 资金不足
        NoEnoughPosition = 4;        // 仓位不足
        IllegalSymbol = 5;           // 策略ID错误
        IllegalSymbol = 6;           // 委托symbol错误
        IllegalVolume = 7;           // 委托量错误
        IllegalPrice = 8;            // 委托价错误
        NoMatchedTradingChannel = 9; // 没有交易通道
        AccountForbidTrading = 10;   // 帐户被禁止交易
        TradingChannelNotConnected = 11; // 交易通道未连接
        StrategyForbidTrading = 10;  // 策略被禁止交易
        NotInTradingTimespan = 13;    // 非交易时间
    }
```

- 撤单被拒绝原因

```c#
    public enum CancelOrderRejectReason
    {
		/// 撤单被拒绝原因
        OrderFinalized = 101;          // 订单已是最终状态
        UnknowOrder = 102;             // 未知订单
        BrokerOption = 103;            // 柜台拒绝
        AlreadyInPendingCancel = 104;  // 重复撤单
    }
```

- OrderSide（订单方向）, C#引用路径： `class GMSDK.OrderSide`

```c#
    public enum OrderSide
    {
        Ask = 2;  // 空方向
        Bid = 1;  // 多方向
    }
```

- OrderType（订单类型）, C#引用路径： `class GMSDK.OrderType`

```c#
    public enum OrderType
    {
        Market = 1;  // 市价单
        Limit = 2;   // 限价单
        Stop = 3;   // 停止价单
        StopLimit = 4;   // 停止限价单
        LimitOrBetter = 5;   // 限价或更优单
    }
```

- ExecType（订单执行回报类型）, C#引用路径： `class GMSDK.ExecType`


```c#
    public enum ExecType
    {
        New = 1;           // 交易所已接受订单
        Canceled = 5;      // 已撤
        PendingCancel = 6; // 待撤
        Rejected = 8;      // 已拒绝
        PendingNew = 10;    // 待接受
        Expired = 12;       // 过期
        Trade = 15;         // 交易
        CancelReject = 19;  // 撤单被拒绝
    }
```

- PositionEffect（开平仓类型）, C#引用路径： `class GMSDK.PositionEffect`

```c#
    public enum PositionEffect
    {
        Open = 1;  // 开仓
        Close = 2; // 平仓
        CloseYesterday = 3; //平昨仓
    }
```

### 交易数据类型

交易数据类型主要包括委托，执行回报，资金，持仓等数据类型。

- Order(委托订单）, C#引用路径： `class GMSDK.Order`

```c#
    public class Order
    {
        /// 策略ID
        public string strategy_id;
        /// 交易账号
        public string account_id;
        /// 客户端订单ID
        public string cl_ord_id;
        /// 柜台订单ID
        public string order_id;
        /// 交易所订单ID
        public string ex_ord_id;
        /// 交易所ID
        public string exchange;
        /// 证券ID
        public string sec_id;
        /// 开平标志
        public byte position_effect;
        /// 买卖方向
        public byte side;
        /// 订单类型
        public byte order_type;
        /// 订单来源
        public int order_src;
        /// 订单状态
        public byte status;
        /// 订单拒绝原因
        public byte ord_rej_reason;
        /// 订单拒绝原因描述
        public string ord_rej_reason_detail;
        /// 委托价
        public double price;
        /// 委托量
        public double volume;
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


- ExecRpt(委托执行回报）, C#引用路径： `class GMSDK.ExecRpt`

```c#
    public class ExecRpt
    {
        /// 策略ID
        public string strategy_id;
        /// 客户端订单ID
        public string cl_ord_id;
        /// 交易所订单ID
        public string order_id;
        /// 订单执行回报ID
        public string exec_id;
		/// 交易所ID
        public string exchange;
        /// 证券ID
        public string sec_id;
        /// 开平标志
        public byte position_effect;
        /// 买卖方向
        public byte side;
        /// 订单拒绝原因
        public byte ord_rej_reason;
        /// 订单拒绝原因描述
        public string ord_rej_reason_detail;
        /// 订单执行回报类型
        public byte exec_type;
        /// 成交价
        public double price;
        /// 成交量
        public double volume;
        /// 成交额
        public double amount;
        /// 交易时间
        public double transact_time;
    }
```

- Cash(资金）, C#引用路径： `class GMSDK.Cash`

```c#
    public class Cash
    {
		public string strategy_id;               // 策略ID
		public int currency;                     // 币种

		public double nav;                      // 资金余额
		public double fpnl;                     // 浮动收益
		public double pnl;                      // 净收益
        public double profit_ratio;             // 收益率
		public double frozen;                   // 持仓冻结金额
        public double order_frozen;             // 挂单冻结金额
        public double available;                // 可用资金

		public double cum_inout;                // 累计出入金
		public double cum_trade;                // 累计交易额
		public double cum_pnl;                  // 累计收益
		public double cum_commission;           // 累计手续费

		public double last_trade;               // 最新一笔交易额
		public double last_pnl;                 // 最新一笔交易收益
		public double last_commission;          // 最新一笔交易手续费
		public double last_inout;               // 最新一次出入金
        public int change_reason;               // 变动原因

		public double transact_time;            // 交易时间
    }
```

- Position(持仓）, C#引用路径： `class GMSDK.Position`

```c#
    public class Position
    {
		/// 交易所ID
        public string exchange;
        /// 证券ID
        public string sec_id;
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
        /// 当前行情价格
        public double price;
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
    public class MarketDataEvent
    {
        /// 时间
        public double utc_time;
        /// 事件类型(1:开盘  2:收盘, 3:回放结束)
        public int event_type;
    }
```

- Tick(逐笔行情数据）, C#引用路径： `class GMSDK.Tick`

```c#
    public class Tick
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
        ///  UTC时间戳
        public double utc_time;
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
        ///  成交总量/最新成交量,累计值
        public double cum_volume;
        ///  成交总金额/最新成交额,累计值
        public double cum_amount;
        ///  合约持仓量(期),累计值
        public System.Int64 cum_position;
        ///  瞬时成交额
        public double last_amount;
        ///  瞬时成交量
        public int last_volume;
        /// 涨停价
        public float upper_limit;
        /// 跌停价
        public float lower_limit;
        /// 今日结算价
        public float settle_price;
        /// (保留)交易类型,对应多开,多平等类型
        public int trade_type;
        //  昨收价
        public float pre_close;     
    }
```

- Bar(各种周期的Bar数据）, C#引用路径： `class GMSDK.Bar`

```c#
    public class Bar
    {
        /// bar类型，以秒为单位，比如1分钟bar, bar_type=60
        public int bar_type;
        /// utc时间戳
        public double utc_time;
        /// 交易所
        public string exchange;
        /// 合约ID
        public string sec_id;
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
    }
```

- DailyBar(日频数据，在Bar数据的基础上，还包含结算价，涨跌停价等静态数据）, C#引用路径： `class GMSDK.DailyBar`

```c#
    public class DailyBar
    {
        /// bar类型
        public int bar_type;
        /// 交易所
        public string exchange;
        /// 合约ID
        public string sec_id;
        /// bar时间
        public System.Int64 bar_time;
        /// utc时间戳
        public double utc_time;
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
        /// 结算价
        public float settle_price;
        /// 涨停价
        public float upper_limit;
        /// 跌停价
        public float lower_limit;
    }
```
