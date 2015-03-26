---
layout: docs
title: C/Cpp策略API介绍  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/c_api/
---

接口API及示例
===========

	# SDK管理接口

	SDK管理接口主要提供了基本的初始化，配置，运行等常用功能接口，以及版本、错误信息等辅助函数。

## SDK初始化

```c
GM_API int gm_login(const char *addr, 
                    const char *username, 
                    const char *password);
```
- 参数说明
    * @param addr       掘金数据服务器地址, <机器名/IP>:<端口号>格式，如 cloud.myquant.cn:8000
    * @param username   掘金账号
    * @param password   掘金密码

## SDK停止并退出

```c
GM_API void gm_logout();
```


## SDK启动

```c
GM_API int gm_run();
```

## 库版本号
```c
gm_version()    返回版本号
```


## 错误代码信息
```c
gm_strerror(int error_code)  查询错误代码对应的文本信息
```
- 参数说明

	error_code  前一函数返回的错误码


# 历史数据接口

API接口函数命名规则： 

- 有品种区别的函数： 产品名_服务名_品种名_业务接口
- 品种无关的函数：  产品名_服务名_业务接口

注意，从版本2.0开始，行情和交易数据接口函数名中不再区别交易品种，实现了统一接口，根据给出的代码symbol参数来区分品种，举例说明： 

- gm_md_get_ticks， 前缀gm_md表示是行情数据服务的接口,  get_ticks表示是用来提取历史的Tick数据
- gm_md_future_表示是行情数据服务中期货数据相关的接口

## 获取历史Tick

### 按时间段提取期货Tick

```c
GM_API  int  gm_md_get_ticks( const char *symbol, 
    	const char  *t_begin, 
    	const char *t_end, 
    	Tick **tick, 
    	int *count);
```

+   参数说明

	    * @param symbol   代码, 如CFFEX.IF1308
	    * @param t_begin  开始时间, 如2013-8-14 00:00:00
	    * @param t_end    结束时间, 如2013-8-15 00:00:00
	    * @param tick     返回的数据缓冲区, 内容Tick 格式的结构数组
	    * @param count    返回结构的数量,即多少个Tick 
        
 +   返回值
    
 	    *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h
 	    

 +   示例：
        1.  提取2013年08月14日的IF1308的Tick数据
            
	```c        
	string t_begin = '2013-08-14 00:00:00';
	string t_end = '2013-08-15 00:00:00';
	Tick **tick = null;
	int count = 0;
	int r = gm_md_get_ticks('CFFEX.IF1308',
	                        t_begin.c_str(),
	                        t_end.c_str(),
	                        tick , // 返回数据的缓冲区，SDK分配
	                        &count, // 返回数据的数目
	                        0);
	if ( r == SUCCESS )　{
	　　// 处理tick数据区
	}
	```


### 提取多个代码的最近一条的Tick

提取快照, 即最新的Tick，支持一次性提取多个代码的最近一条Tick数据
    
```c
GM_API int gm_md_get_last_ticks (
        const char *symbol_list, 
        Tick **tick, 
        int *count
    );
```

+   参数说明：
        * @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
        * @param tick     返回的数据缓冲区, 内容Tick 格式的结构数组
        * @param count   返回结构的数量,即多少个Tick 
    
        
+   返回值 
        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


+   示例：

	​1.  获取IF1502, AG1506过去10个Tick:
       
    ```c
    Tick **tick = null;
    int count = 0;
    int r = gm_md_get_last_ticks('CFFEX.IF1506,SHFE.ag1506',
                                 tick, // 返回数据的缓冲区，SDK分配
                                 &count, // 返回数据的数目
                                 );
                                 
    if ( r == SUCCESS )　{
       // 处理tick数据区
    }
    ```
     
### 提取一个代码的最近n条Tick

```c
GM_API int gm_md_get_last_n_ticks(
        const char *symbol, 
        int n, 
        Tick **tick, 
        int *count);
```

+   参数说明：
    
    	* @param symbol   代码, 如CFFEX.IF1308
    	* @param n        数据个数
    	* @param tick     返回的数据缓冲区, 内容Tick 格式的结构数组
    	* @param count    返回结构的数量,即多少个Tick

+   返回值 
    
        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

+   示例：

	​1.  获取IF1308过去10个Tick:
       
    ```c
    Tick **tick = null;
    int count = 0;
    int data_num = 10;
    int r = gm_md_get_last_n_ticks('CFFEX.IF1308',
                                    data_num,
                                    tick, // 返回数据的缓冲区，SDK分配
                                    &count, // 返回数据的数目
                                    0);
    if ( r == SUCCESS )　{
        // 处理tick数据区
    }
    ```

## 获取历史Bar

### 单个代码按时间段
        
```c
GM_API int gm_md_get_bars(
        const char *symbol,
        int bar_type,
        const char *t_begin,
        const char *t_end,
        Bar **bar,
        int *count
);
```

+   参数说明：

        * @param symbol      代码, 如CFFEX.IF1308
        * @param bar_type    bar周期类型,以秒计算, 1分钟bar用60 
        * @param t_begin     开始时间, 如2013-8-14 00:00:00
        * @param t_end       结束时间, 如2013-8-15 00:00:00
        * @param bar         返回的数据缓冲区, 内容Bar 格式的结构数组
        * @param count       返回结构的数量,即多少个Bar

+   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

+   示例

    1.   提取2013年08月14日的IF1308的60秒Bar数据
    
	```c
	string t_begin = '2013-08-14 00:00:00';
	string t_end = '2013-08-15 00:00:00';
	Bar **bar = null;
	int count = 0;
	int r = gm_md_get_bars('CFFEX.IF1308',
	                            60,
	                            t_begin.c_str(),
	                            t_end.c_str(),
	                            bar , // 返回数据的缓冲区，SDK分配
	                            &count, // 返回数据的数目
	                            );
	if ( r == SUCCESS )　{
	     // 处理bar数据区
	}
	```
            
### 提取最新的Bar
   
支持多个代码同时提取

```c
GM_API int gm_md_get_last_bars(
       const char *symbol_list,
       int bar_type,
       Bar **bar,
       int *count
   );
```

 +   参数说明：
 
        * @param symbol_list 多个品种代码列表, 如CFFEX.IF1308,CFFEX.1401,SHFE.AG1311
        * @param bar_type    bar周期类型,以秒计算, 1分钟bar用60
        * @param bar         返回的数据缓冲区, 内容Bar 格式的结构数组
        * @param count       返回结构的数量,即多少个Bar

 +   返回值：
 
        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

 +   示例
        
	1.  提取2013年08月14日的IF1308的1分钟Bar数据
            
	```c
	string t_begin = '2013-08-14 00:00:00';
	string t_end = '2013-08-15 00:00:00';
	Bar **bar = null;
	int count = 0;
	int r = gm_md_get_bars('CFFEX.IF1308',
	                       60,
	                       bar , // 返回数据的缓冲区，SDK分配
	                       &count, // 返回数据的数目
	                      );
	if ( r == SUCCESS )　{
	    // 处理bar数据区
	}
	```

### 过去n个Bar数据

  提取期货Bar, 即最新的n个Bar
           
```c
GM_API int gm_md_get_last_n_bars(
	    const char *symbol,
	    int    bar_type,
	    int n,
	    Bar **bar,
	    int *count);
```

+   参数说明：

        * @param symbol     代码, 如CFFEX.IF1308
        * @param bar_type   bar周期类型,以秒计算, 1分钟bar用60
        * @param n          数据个数
        * @param bar        返回的数据缓冲区, 内容Bar 格式的结构数组
        * @param count      返回结构的数量,即多少个Bar
        
 +   返回值：
 
        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

 +   示例：

 	1. 获取IF1308过去10根60秒Bar:
         
	```c
      Bar **bar = null;
      int count = 0;
      int data_num = 10;
      int r = gm_md_future_query_last_bar('CFFEX.IF1308',
                                    60,
                                    data_num,
                                    bar , // 返回数据的缓冲区，SDK分配
                                    &count, // 返回数据的数目
                                    0);
      if ( r == SUCCESS )　{
      		// 处理bar数据区
      }
	```
        
## 获取历史日数据

### 按时间段

  支持单个代码
    
```c
GM_API int gm_md_get_dailybars (
	    const char *symbol,
	    const char *t_begin,
	    const char *t_end,
	    DailyBar **dbar,
	    int *count); 
```

+   参数说明：

    	* @param symbol      品种代码, 如CFFEX.IF1308
    	* @param t_begin     开始时间, 如2013-8-14 00:00:00
    	* @param t_end       结束时间, 如2013-8-15 00:00:00
    	* @param dbar        返回的数据缓冲区, 内容DailyBar 格式的结构数组
    	* @param count       返回结构的数量,即多少个DailyBar

+   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


+   示例：

    1. 提取2013年08月14日的IF1308的Day数据
        
	```c
	string t_begin = '2013-08-14 00:00:00';
	string t_end = '2013-08-15 00:00:00';
	DailyBar **day = null;
	int count = 0;
	int r = gm_md_get_dailybars('CFFEX.IF1308',
	       t_begin.c_str(),
	       t_end.c_str(),
	       day, // 返回数据的缓冲区，SDK分配
	       &count, // 返回数据的数目
	       );
	
	if ( r == SUCCESS )　{
	　　// 处理bar数据区
	}
    ```

### 提取最新的DailyBar

  支持多个代码同时提取
    
```c
GM_API int gm_md_get_last_dailybars (
	    const char *symbol_list,
	    DailyBar **dbar,
	    int *count);
```

+   参数说明：

    	* @param symbol_list 多个品种代码列表, 如'CFFEX.IF1308,CFFEX.1401'
    	* @param dbar        返回的数据缓冲区, 内容DailyBar 格式的结构数组
    	* @param count       返回结构的数量,即多少个DailyBar

+   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


+   示例：

    1. 提取2013年08月14日的IF1308, ag1412的DailyBar数据
        
	```c
	DailyBar **day = null;
	int count = 0;
	int r = gm_md_get_dailybars('CFFEX.IF1308,SHFE.ag1412',
	       day, // 返回数据的缓冲区，SDK分配
	       &count, // 返回数据的数目
		       );
	
	if ( r == SUCCESS )　{
	　　// 处理bar数据区
		}
	    ```

### 过去n个DailyBar数据

提取过去n个DailyBar, 即给定代码的最新日数据
    
```c
GM_API int gm_md_get_last_n_dailybars (
	    const char *symbol,
	    int n,
	    DailyBar **day,
	    int *count);
```

+   参数说明

		* @param symbol  品种代码, 如CFFEX.IF1308
    	* @param n       数据个数
    	* @param dbar    返回的数据缓冲区, 内容DailyBar 格式的结构数组
    	* @param count   返回结构的数量,即多少个DailyBar 

+   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

+   示例：

    1. 获取IF1308过去10根DailyBar:
        
	```c
    DailyBar **day = null;
    int count = 0;
    int data_num = 10;
    int r = gm_md_future_query_last_day('CFFEX.IF1308',
                                60,
                                data_num,
                                day , // 返回数据的缓冲区，SDK分配
                                &count, // 返回数据的数目
                                0);
    if ( r == SUCCESS )　{
    　　// 处理day数据
    }
    ```

### 注意事项

-   内存管理

为方便SDK调用和避免内存碎片，对于返回数据缓冲区由SDK统一管理，调用者只需要提供对应返回数据缓冲结构和数目的指针，SDK会把相应地址赋给调用者给出的指针，然后在函数返回后，调用者即可通过指针进行数据访问。

使用者需要注意：

    ​1.  数据处理完成前，不要进行新的查询，避免数据被覆盖；
    ​2.  不要对返回数据区指针进行删除，SDK会复用相关内存区块。

-   代码与时间段组合导致数据过多时的处理

新版本修改了原有接口，不再支持自由组合

如果确实有大量数据需要查询，例如，回测过去一个月的Tick数据，可以逐天调用处理。或者是多个代码，长时间段的查询，可以按代码或时间分别拆解开来进行。

-   返回代码含义及异常处理机制

所有查询接口的返回代码有统一的定义，跟通常的SDK定义一样，返回为0时表示本次查询成功，如果返回值为负，则表示本次查询失败，各返回值有分别对应的意义，可以用gm_strerror()方便地检查错误码。

# 实时行情订阅接口

## 连接初始化

初始化实时行情服务: 连接并登陆实时行情服务
    
```c
GM_API int gm_md_init(
    int mode=MD_MODE_NULL,
    const char *subscribe_symbol_list=NULL,
    const char *start_time=NULL,
    const char *end_time=NULL
);
```

-   参数说明：

		* @param mode  订阅哪一类行情：mode=MD_LIVE/MD_SIMULATED/MD_PLAYBACK
    	* @param start_time      	回放行情的开始时间，仅用于回放模式，
    								格式：yyyy-mm-dd HH:MM:SS 
    	* @param end_time         	回放行情的结束时间，仅用于回放模式，
    								格式：yyyy-mm-dd HH:MM:SS	

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h
    
## 断线重连
    
```c
GM_API int gm_md_reconnect();
```

-   参数说明：
    * 无
    
-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h
    

##  注册Bar, Tick，Trade, MarketEvent, Error事件的处理函数

-   Login回调方法

    ```c
    typedef void (*MDLoginCallback)();
    ```
	+   参数	
		* 无
		
	+   设置行情Event回调方法

	```c
   	GM_API void gm_md_set_login_callback(MDLoginCallback cb);
	```

    
-   Event回调方法

    ```c
    typedef void (*MDEventCallback)(MDEvent *event);
    ```

	+   参数
    	*   event             事件对象

	+   设置行情Event回调方法

	```c
    GM_API void gm_md_set_event_callback(MDEventCallback cb);
	```

-   Tick行情数据回调方法

	```c
    typedef void (*MDTickCallback)(Tick *tick);
	```

	+   参数
    	*   tick       逐笔行情

	+   设置Tick行情回调方法

	```c
    GM_API void gm_md_set_tick_callback(MDTickCallback cb);
	```


-   Bar行情回调方法

	```c
    typedef void (*MDBarCallback)(Bar *bar);
	```

    +   参数 
        *   bar             分时行情      
    +   设置Bar行情回调方法

	```c
    GM_API void gm_md_set_bar_callback(MDBarCallback cb);
	```

- 	行情Error回调方法

	```c
    	typedef void (*MDErrorCallback)(int error_code, const char *error_msg);
	```

	+   参数
        * @param error_code    错误编号
        * @param error_msg     错误描述
	
	+   设置行情error回调方法
	
	```c
	GM_API void gm_md_set_error_callback(MDErrorCallback cb);
	```

## 订阅行情

```c
GM_API int gm_md_subscribe(const char *symbol_list);
```

-  参数
 
        * @param symbol_list 订阅代码表,参考订阅规则
    
    symbol_list 订阅代码表,参数格式如下。
    
    * symbol_list 订阅规则和算法说明:

        交易所exchange: SHSE-上交所 SZSE-深交所 CFFEX-中金所 SHFE-上期所 DCE-大商所 CZCE-郑商所

        订阅串有三节或四节组成, 分别对应 交易所exchange.代码code.数据类型data_type.周期类型bar_type
        只有订阅bar数据时, 才用到第四节, 周期类型才有作用
        支持多种格式的订阅,使用如下:
       
        + SZSE.*                       : 深交所全部代码的tick,  bar数据
        + SZSE.000010.tick      :深交所,000010, tick数据
        + CFFEX.IF1403.*        : 中金所,IF1403,所有数据
        + CFFEX.IF1403.tick     : 中金所,IF1403, tick数据
        + CFFEX.IF1403.bar.60   : 中金所,IF1403, 1分钟(60秒)bar数据
        + CFFEX.IF1403.*,CFFEX.IF1312.*	: 中金所,IF1403和IF1312所有数据(订阅多个代码)

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

## 退订

```c
GM_API int gm_md_subscribe(const char *symbol_list);
```

取消订阅，参数格式同订阅一样。


# 交易接口

## 连接初始化

	初始化交易服务: 连接并登陆交易服务

```c
GM_API int gm_td_init(
        const char *strategy_id, 
        const char *addr=NULL);
```

-   参数说明：

        * @param strategy_id 策略ID
        * @param addr        交易服务器地址, 格式<host name/ip>:<port num>

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


-   示例：

```c
ret = gm_td_init("strategy_1", "192.168.1.10:5050");
```

## 断开重连

```c
GM_API int gm_td_reconnect();
```

重新连接交易服务, 如果交易服务中途断开了连接，可调用此函数重连。


## 委托下单

###  基本下单函数

委托请求, 底层函数, 需要自己填充Order, 不建议使用

```c
GM_API int gm_td_place_order(Order *order);
```

-   参数说明：

        * @param order 委托,输入和输出参数，委托请求发送成功后，将填充order.cl_ord_id并回传。

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h
    
###  开多仓
```c
GM_API int gm_td_open_long(const char *exchange, 
                           const char *sec_id, 
                           double price, 
                           double volume, 
                           Order *order=NULL);
```

-   参数说明：

        * @param exchange 交易所代码
    	* @param sec_id 证券代码
    	* @param price  价格：price==0为市价单，否则为限价单
    	* @param volume 数量
    	* @param order  输出参数，返回委托请求的Order对象，Order对象空间由用户分配并传入

-   返回值：
            
        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

###  开空仓

```c
GM_API int gm_td_open_short(const char *exchange, 
                            const char *sec_id, 
                            double price, 
                            double volume, 
                            Order *order=NULL);
```

-   参数说明：

    	* @param exchange 交易所代码
    	* @param sec_id 证券代码
    	* @param price  价格：price==0为市价单，否则为限价单
    	* @param volume 数量
    	* @param order  输出参数，返回委托请求的Order对象，Order对象空间由用户分配并传入

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

###  平多仓
```c
GM_API int gm_td_close_long(const char *exchange, 
                                const char *sec_id, 
                                double price, 
                                double volume, 
                                Order *order=NULL);
```

-   参数说明：

    	* @param exchange 交易所代码
    	* @param sec_id 证券代码
    	* @param price  价格：price==0为市价单，否则为限价单
    	* @param volume 数量
    	* @param order  输出参数，返回委托请求的Order对象，Order对象空间由用户分配并传入

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

###  平空仓
```c
GM_API int gm_td_close_short(const char *exchange,
                             const char *sec_id, 
                             double price, 
                             double volume, 
                             Order *order=NULL);
```

-   参数说明：

        * @param exchange 交易所代码
        * @param sec_id 证券代码
        * @param price  价格：price==0为市价单，否则为限价单
        * @param volume 数量
        * @param order  输出参数，返回委托请求的Order对象，Order对象空间由用户分配并传入

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h

## 撤单

```c
GM_API int gm_td_cancel_order(const char *cl_ord_id);
```

-   参数说明：
    
        *@param cl_ord_id 客户端委托ID，委托的唯一识别符（client order id)

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h



## 成交回报处理函数

-  成交回报

```c
typedef void (*TDExecRptCallback) (ExecRpt *res);
```

+   参数

    	* res     成交回报  

+   设置成交回报回调方法
	
	1. 设置委托回报 callback函数 （原始委托回报函数，如果注册，会最先被调用）

	```c
GM_API void gm_td_set_execrpt_callback(TDExecRptCallback cb);
```
	
	2. 设置委托撤销拒绝回报 callback函数
    
	```c
GM_API void gm_td_set_order_cancel_rejected_callback(TDExecRptCallback cb);
    ```

## 订单状态变化事件处理函数

   - 订单状态变化

    处理函数定义

    ```c
typedef void (*TDOrderStatusCallback) (Order *res);
    ```

	+   参数
	
    	* res   订单  

	+   设置订单状态变化事件回调方法
        
        * 处理所有的委托状态变化
        
        ```c
GM_API void gm_td_set_order_status_callback(
			TDOrderStatusCallback cb
		);
        ```
        
        * 注册委托被接受的处理函数
        
        ```c
GM_API void gm_td_set_order_new_callback(
			TDOrderNewCallback cb
		);
        ```
        
        * 注册委托被拒绝的处理函数
        
        ```c
GM_API void gm_td_set_order_rejected_callback(
			TDOrderNewCallback cb
	);
        ```

        * 注册委托部分成交处理函数
        
        ```c
GM_API void gm_td_set_order_partially_filled_callback(
			TDOrderStatusCallback cb);
        ```

        * 注册委托完全成交处理函数
        
        ```c
GM_API void gm_td_set_order_filled_callback(
			TDOrderStatusCallback cb
		);
        ```

        * 注册委托已撤销处理函数
        
        ```c
GM_API void gm_td_set_order_cancelled_callback(
			TDOrderStatusCallback cb
		);
        ```

        * 注册委托停止执行处理函数
        
        ```c
GM_API void gm_td_set_order_stop_executed_callback(
			TDOrderStatusCallback cb
		);
        ```

## 交易错误处理

  处理函数定义
    
```c
typedef void (*TDErrorCallback)(int error_code, const char *msg);
```

+ 参数定义

    	* @param error_code		  错误编号
    	* @param error_msg		  错误描述

+ 设置处理函数
    
```c
GM_API void gm_td_set_error_callback(TDErrorCallback cb);
```

## 交易信息查询接口

### 策略资金查询

```c
GM_API int gm_td_get_cash(Cash *res);
```

-   参数说明：

        *@param res 资金查询结果

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


## 策略持仓查询

### 根据代码和方向查询
```c
GM_API int gm_td_get_position(const char *exchange, 
                              const char *sec_id, 
                              int side, 
                              Position **res);
```

-   参数说明

    	* @param symbol 持仓代码
    	* @param side   持仓方向
    	* @param res    持仓查询结果

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


### 查所有持仓
```c
GM_API int gm_td_get_positions(Position **res, int *count);
```

-   参数说明

    	* @param res 持仓查询结果

-   返回值：

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


# 策略简化接口

## 初始化

### 1. 通过程序参数初始化
    
```c
GM_API int strategy_init(
        const char   *md_addr, 
        const char   *td_addr,
        const char   *username,
        const char   *password,
        const char   *strategy_id,
        const char   *subscribe_symbol_list,
        int           mode=MD_MODE_LIVE, 
        const char   *start_time=NULL, 
        const char   *end_time=NULL);
```
    
- 参数说明

    	* @param md_addr			数据服务地址, 比如 cloud.myquant.cn:8000
    	* @param td_addr			交易服务地址, 比如 cloud.myquant.cn:8001
    	* @param username			掘金账号
    	* @param password			掘金密码
    	* @param strategy_id		策略ID
    	* @param subscribe_symbol_list 行情订阅的代码列表
    	* @param mode          		订阅哪一类行情：实时/模拟/回放
    	* @param start_time      	回放行情的开始时间，仅用于回放模式，
    								格式：yyyy-mm-dd HH:MM:SS 
    	* @param end_time         	回放行情的结束时间，仅用于回放模式，
    								格式：yyyy-mm-dd HH:MM:SS
    
- 返回值

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h
    

### 2. 配置文件初始化
    
```c
GM_API int strategy_init_with_config(const char *config_file);
```
    
- 参数说明
    
        *@param config_file 策略配置文件名（全路径）

- 返回值

        *@return    0: success, 其他值: 见SDK错误代码定义文档，或头文件error.h


