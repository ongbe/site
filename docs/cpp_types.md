---
layout: docs
title: C/C++ 数据类型  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/cpp_types/
---

掘金量化策略SDK的主要数据类型

### 基本数据结构(C语言)

- Tick数据

```c
typedef struct Tick
{
	    char           exchange[LEN_EXCHANGE];   //交易所代码
	    char           sec_id[LEN_SECID];        //证券ID
	    double         utc_time;        		 //utc时间到毫秒

	    //以下为基础字段
	    float          last_price;               //最新价
	    float          open;                     //开盘价
	    float          high;                     //最高价
	    float          low;                      //最低价

	    float          bid_p1;                   //买价一
	    float          bid_p2;                   //买价二
	    float          bid_p3;                   //买价三
	    float          bid_p4;                   //买价四
	    float          bid_p5;                   //买价五
	    int            bid_v1;                   //买量一
	    int            bid_v2;                   //买量二
	    int            bid_v3;                   //买量三
	    int            bid_v4;                   //买量四
	    int            bid_v5;                   //买量五
	    float          ask_p1;                   //卖价一
	    float          ask_p2;                   //卖价二
	    float          ask_p3;                   //卖价三
	    float          ask_p4;                   //卖价四
	    float          ask_p5;                   //卖价五
	    int            ask_v1;                   //卖量一
	    int            ask_v2;                   //卖量二
	    int            ask_v3;                   //卖量三
	    int            ask_v4;                   //卖量四
	    int            ask_v5;                   //卖量五

	    double         cum_volume;            //成交总量
	    double         cum_amount;            //累计成交总金额
	    long long      cum_position;          //合约持仓量(期),累计值
	    double         last_amount;           //瞬时成交额
	    int            last_volume;           //瞬时成交量(中金所提供)
	    float          upper_limit;           //涨停价;
	    float          lower_limit;           //跌停价;
	    float          settle_price;          //今日结算价;
	    int            trade_type;            //(保留)交易类型,多开,多平等
        float          pre_close;             //昨收价
}Tick;
```

- Bar数据
	
```c
typedef struct Bar
{
    char        exchange[LEN_EXCHANGE]; //交易所代码
    char        sec_id[LEN_SECID];      //证券ID
    long long   bar_time;               //日期时间[20131108142000],精确到秒

    int         bar_type;               //分时周期类型(enum BarType)
    double      utc_time;               //UTC时间戳
    float       open;                   //开盘价
    float       close;                  //收盘价
    float       high;                   //最高价
    float       low;                    //最低价
    double      volume;                 //成交量
    double      amount;                 //成交金额
}Bar;
```

- 日线结算数据

```c
typedef struct DailyBar
{
    char        exchange[LEN_EXCHANGE]; //交易所代码
    char        sec_id[LEN_SECID];      //证券ID
    int         bar_type;               //分时周期类型(enum BarType)
    long long	bar_time;               //整型日期时间[20131108142000],精确到秒
    double      utc_time;               //UTC时间戳
                                        
    float       open;                   //开盘价
    float       close;                  //收盘价
    float       high;                   //最高价
    float       low;                    //最低价
    double      volume;                 //成交量
    double      amount;                 //成交金额
                                        
    long long	position;			    //持仓量
    float		settle_price;	 	    //结算价
    float		upper_limit;		    //涨停价
    float		lower_limit;		    //跌停价
}DailyBar;
```
    
- MarketEvent数据
	
```c
typedef struct MDEvent
{
    double    utc_time;      //UTC时间戳[带毫秒]
    int       event_type;    //事件类型(1:开盘  2:收盘, 3:回放结束)
}MDEvent;
```

- Position数据
	
```c
typedef struct Position
{
    char strategy_id[LEN_ID];       //策略id

    char exchange[LEN_EXCHANGE];    //交易所代码
    char sec_id[LEN_SECID];         //交易标的代码，本地代码如IF1502
    int side;                       //买卖方向
    double volume;                  //持仓数量
    double amount;                  //持仓市值
    double vwap;                    //持仓成本

    double price;                   //当前价格
    double fpnl;                    //浮动赢动
    double cost;                    //持仓成本
    double order_frozen;            //挂单冻结仓位量
    double available;               //可平仓位量

    double last_price;              //上一次成交价
    double last_volume;             //上一次成交量

    double init_time;               //建仓时间
    double transact_time;           //仓位变更时间
}Position;
```

- Order数据

```c
typedef struct Order
{
    char strategy_id[LEN_ID];       //策略id
    char account_id[LEN_ID];        //帐号id, 系统自动根据配置补充

    char cl_ord_id[LEN_ID];         //委托订单的客户方id
    char order_id[LEN_ID];          //委托订单的柜台生成id
    char ex_ord_id[LEN_ID];         //委托订单的交易所接受后生成id

    char exchange[LEN_EXCHANGE];    //交易所代码
    char sec_id[LEN_SECID];         //交易标的代码，本地代码如IF1502

    int position_effect;            //开平标志，取值参考enum PositionEffect
    int side;                       //买卖方向，取值参考enum OrderSide
    int order_type;                 //委托类型，取值参考enum OrderType
    int order_src;                  //委托来源，取值参考enum OrderSrc
    int status;                     //委托状态，取值参考enum OrderStatus
    int ord_rej_reason;             //委托拒绝原因，取值参考enum OrderRejectReason
    char ord_rej_reason_detail[LEN_INFO];   //委托被拒绝原因描述

    double price;                   //委托价格
    double volume;                  //委托量
    double filled_volume;           //已成量
    double filled_vwap;             //已成均价
    double filled_amount;           //已成金额

    double sending_time;            //委托发送时间
    double transact_time;           //委托处理时间
 }Order;
```

- 成交回报数据
    
```c
typedef struct ExecRpt
{
    char strategy_id[LEN_ID];       //策略id

    char cl_ord_id[LEN_ID];         //委托订单的客户方id
    char order_id[LEN_ID];          //委托订单的柜台生成id
    char exec_id[LEN_ID];           //委托订单的交易所生成id

    char exchange[LEN_EXCHANGE];    //交易所代码
    char sec_id[LEN_SECID];         //交易标的代码，本地代码如IF1502

    int position_effect;            //开平仓标识
    int side;                       //买卖方向
    int ord_rej_reason;             //委托被拒绝原因类型
    char ord_rej_reason_detail[LEN_INFO];  //委托被拒绝原因描述
    int exec_type;

    double price;                   //成交价格
    double volume;                  //成交数量
    double amount;                  //成交金额

    double transact_time;           //成交时间
    }ExecRpt;
```

- Cash资金数据
    
```c
typedef struct Cash
{
    char strategy_id[LEN_ID];       //策略id
    int currency;                   //货币类别

    double nav;                     //帐户净值
    double fpnl;                    //浮动赢亏
    double pnl;                     //总赢亏
    double profit_ratio;            //收益率
    double frozen;                  //持仓冻结金额
    double order_frozen;            //挂单冻结金额
    double available;               //可用资金

    double cum_inout;               //累计出入金总额
    double cum_trade;               //累计交易额
    double cum_pnl;                 //累计赢亏
    double cum_commission;          //累计交易费

    double last_trade;              //上次交易金额
    double last_pnl;                //上次总赢亏
    double last_commission;         //上次交易费
    double last_inout;              //上次出入金
    int change_reason;              //帐户变动类别

    double transact_time;           //帐户变动时间戳
}Cash;
```

### 其他辅助数据结构

-   OrderStatus  委托状态定义
    
```c
enum OrderStatus {
    OrderStatus_New = 1,                        //已报
    OrderStatus_PartiallyFilled = 2,            //部成
    OrderStatus_Filled = 3,                     //已成
    OrderStatus_DoneForDay = 4,                 //
    OrderStatus_Canceled = 5,                   //已撤
    OrderStatus_PendingCancel = 6,              //待撤
    OrderStatus_Stopped = 7,                    //停止
    OrderStatus_Rejected = 8,                   //已拒绝
    OrderStatus_Suspended = 9,                  //挂起
    OrderStatus_PendingNew = 10,                //待报
    OrderStatus_Calculated = 11,                //计算
    OrderStatus_Expired = 12,                   //已过期
    OrderStatus_AcceptedForBidding = 13,        //接受竞价
    OrderStatus_PendingReplace = 14             //待修改
};
```
    
-   PositionEffect 开平标识
    
```c
enum PositionEffect {
    PositionEffect_Open = 1,                     //开仓
    PositionEffect_Close = 2,                     //平仓
    PositionEffect_CloseYesterday = 3      //平昨仓，上期所区分平今/平昨
};
```

	-   ExecRptType 成交回报类型
    
```c
enum ExecType {
    ExecType_New = 1,                   //已报
    ExecType_DoneForDay = 4,            //当日已完成
    ExecType_Canceled = 5,              //已撤销
    ExecType_PendingCancel = 6,         //待撤销
    ExecType_Stopped = 7,               //已停止
    ExecType_Rejected = 8,              //已拒绝
    ExecType_Suspended = 9,             //挂起
    ExecType_PendingNew = 10,           //待报
    ExecType_Calculated = 11,           //已计算
    ExecType_Expired = 12,              //过期
    ExecType_Restated = 13,             //重置
    ExecType_PendingReplace = 14,       //待修改
    ExecType_Trade = 15,                //成交
    ExecType_TradeCorrect = 16,         //成交更正
    ExecType_TradeCancel = 17,          //撤销
    ExecType_OrderStatus = 18,          //委托状态
    ExecType_CancelRejected = 19        //撤单被拒绝
};
```

	-   Side 买卖方向
    
```c
enum OrderSide {
	OrderSide_Bid = 1,          	//多方向
	OrderSide_Ask = 2           	//空方向
};
```

-   OrderRejectReason 委托拒绝原因
   
```c
enum OrderRejectReason {
    OrderRejectReason_UnknownReason = 1,                //未知原因
    OrderRejectReason_RiskRuleCheckFailed = 2,          //不符合风控规则
    OrderRejectReason_NoEnoughCash = 3,                 //资金不足
    OrderRejectReason_NoEnoughPosition = 4,             //仓位不足
    OrderRejectReason_IllegalStrategyID = 5,            //非法策略ID
    OrderRejectReason_IllegalSymbol = 6,                //非法交易标的
    OrderRejectReason_IllegalVolume = 7,                //非法委托量
    OrderRejectReason_IllegalPrice = 8,                 //非法委托价
    OrderRejectReason_NoMatchedTradingChannel = 9,      //没有匹配的交易通道
    OrderRejectReason_AccountForbidTrading = 10,        //交易账号被禁止交易
    OrderRejectReason_TradingChannelNotConnected = 11,  //交易通道未连接
    OrderRejectReason_StrategyForbidTrading = 12,       //策略不允许交易
    OrderRejectReason_NotInTradingSession = 13          //非交易时段
};
```
