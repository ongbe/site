---
layout: docs
title: C/C++策略示例  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/python_examples/
---


### C语言策略


以下是一个简单的C语言策略示例，直接使用SDK的各回调接口,通过行情数据驱动运行。
这个策略主要处理逻辑在on_tick函数，在接收到SDK转发的tick数据后，进行有规律地开、平仓动作；
其他on_trade, on_bar, on_event 方法中没有具体的业务逻辑，可以用这个文件作为模板，根据需要进行扩展。

程序入口函数main中， 首先是对SDK进行初始化操作，接下来是设置各事件的回调函数，依次是成交回报的处理函数、tick行情的处理函数、bar行情的处理函数、trade行情的处理函数、以及事件的处理函数；
最后是策略的启动运行， 通过调用gm_run()进行。

在策略的初始化部分，如果用参数直接配置，就像注释掉的gm_init(...)函数部分一样，不需要额外的配置文件，简单直观，但会导致每次修改参数时都需要重新编译代码，如果需要经常变换执行环境的参数设置，建议采用配置文件的方式，即用gm_init_with_config()函数来处理，SDK初始化过程会有返回值，可以通过返回值了解初始化是否成功，如果失败，返回值会表明失败的原因，示例程序中给出了各个错误代码的解释，并打印在控制台，方便策略程序的调试。

程序源文件如下：

```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <string.h>
	#include <time.h>

	#include "gm_future.h"
	#include "gm_future_api.h"
		
	int g_count;
	
	//处理逐笔行情事件
	void on_tick(FutureTickData *tick)
	{
	    g_count++;
	    int ret;
   
	    printf("symbol: %s price: %s\0", tick->sec_id, tick->last_price);

	    Sleep(20);
	
	    Order o;
	    char symbol[32];
	    if(g_count % 100 == 0)
	    {       
	        sprintf(symbol, "%s.%s\0", tick->exchange, tick->sec_id);
	    
	        ret = gm_tr_future_open_long(symbol, tick->last_price, 3, &o);
	        if(ret == 0)
	        {
	            printf("开多: %s , strategy: %s, symbol: %s price %.2f volume %d \n", o.cl_ord_id, o.strategy_id, o.symbol, o.price, o.volume);
	        }
	        else
                printf("Error code = %d \n", ret);
	    }
	    else if(g_count % 75 == 0)
	    {       
	        sprintf(symbol, "%s.%s\0", tick->exchange, tick->sec_id);
	    
	        ret = gm_tr_future_open_short(symbol, tick->last_price, 4, &o);
	        if(ret == 0)
	        {
                printf("开空: %s , strategy: %s, symbol: %s price %.2f volume %d \n", o.cl_ord_id, o.strategy_id, o.symbol, o.price, o.volume);
	        }
	        else
                printf("Error code = %d \n", ret);
	    }
	    else if(g_count % 50 == 0)
	    {       
	        sprintf(symbol, "%s.%s\0", tick->exchange, tick->sec_id);
	    
	        ret = gm_tr_future_close_long(symbol, tick->last_price, 3, &o);
	        if(ret == 0)
	        {
               printf("平多: %s , strategy: %s, symbol: %s price %.2f volume %d \n", o.cl_ord_id, o.strategy_id, o.symbol, o.price, o.volume);
	        }
	        else
               printf("Error code = %d \n", ret);
	        
	    }
	    else if(g_count % 25 == 0)
	    {       
	        ret = sprintf(symbol, "%s.%s\0", tick->exchange, tick->sec_id);
	    
	        ret = gm_tr_future_close_short(symbol, tick->last_price, 4, &o);
	        if(ret == 0)
	        {
               printf("平空: %s , strategy: %s, symbol: %s price %.2f volume %d \n", o.cl_ord_id, o.strategy_id, o.symbol, o.price, o.volume);
	        }
	        else
               printf("Error code = %d \n", ret);
	    }
	}
	
	//处理分时行情事件
	void on_bar(FutureBarData *bar)
	{
	    printf("Bar symbol= %s open = %.2f close = %.2f \n", bar->sec_id, bar->open, bar->close);
	}
	
	//处理行情中的成交事件
	void on_trade(FutureTradeData *trade)
	{
	}
	
	//处理委托回报事件
	void on_execution(Execution *res)
	{
        printf("成交回报: strategy: %s, symbol: %s price %.2f volume %d \n", 
        res->strategy_id, res->symbol, res->price, res->volume);
	}
	
	//如果是回放行情，行情文件播放完毕时退出策略
	void on_event(MarketDataEvent *event)
	{
	    if (event->event_type == 3)
	    {
	        printf("finished playback\n");
	        gm_future_stop();
	    }
	}
	
	int main(int argc, char *[])
	{
	    int ret;
	
	    //初始化策略,根据参数
	    /*
	    ret = gm_future_init(
	        "tcp://211.154.152.181:5103",
	        "tcp://211.154.152.181:5050",
	        "tcp://211.154.152.181:5104",
	        "username","password",
	        "strategy_1",
	        "CFFEX.IF1403.*"
	        );
	    */
	
	    //初始化策略,根据配置文件
	    ret = gm_future_init_with_config("test_strategy.ini");
	
	    //初始化失败，退出。
	    printf("gm_init return: %s \n", gm_strerror(ret));       
	
	    if(ret )
	    {
	        system("pause");
	        return ret;
	    }
	
	    // 设置事件回调函数
	    gm_tr_future_set_execution_callback(on_execution);
	    gm_md_future_set_tick_callback(on_tick);
	    gm_md_future_set_bar_callback(on_bar);
	    gm_md_future_set_trade_callback(on_trade);
	    gm_md_future_set_event_callback(on_event);
	    
	    printf("策略起动成功!\n"); 
	
	    // 执行并等待策略运行结束
	    ret = gm_run();
	    return ret;
	}
```

策略配置文件用的是直观的ini格式，支持Section, 其中gm部分为SDK所使用，这部分的配置可参考如下示例， 其中以`;`开始的部分表示注释掉的配置，可以是

- 	文件回放测试作为行情源：md_uri=file://SHFE.cu1403.tick，
- 	或者是落地部署在本地的实时行情服务：md_uri=tcp://192.168.1.16:5103。
 	
strategy_id： 

表示策略的识别符，示例中的ID为uuid格式，也可以直接指定自定义的字符串，由于策略运行监控平台中需要保证各策略的ID唯一，会给接入的策略分配ID，接入平台时，这里需要配置成平台分配的策略ID。

subscribe_symbols:

向行情源服务订阅的代码，格式同SDK行情订阅函数gm_md_future_subscribe中的参数格式一样，多个代码间用`,`分隔。示例中订阅了上期所的cu1401,cu1403两个品种的tick数据。

配置文件示例：

```ini
    [gm]    
    md_uri=tcp://211.154.152.181:5103
    tr_uri=tcp://211.154.152.181:5050
    query_uri=tcp://211.154.152.181:5104
    
    username=username
    password=yourpassword
    strategy_id=03883f97-4d60-4843-bbc2-cc8752a04691
    subscribe_symbols=SHFE.cu1401.TICK,SHFE.cu1403.TICK
```


####　策略调试运行：


策略的编程调试跟通常的其他程序类似，在策略师自己熟悉的IDE环境下跟普通程序一样，这里不重复，具体请参考相应的IDE帮助手册。

调试环境下，策略也可以接入到掘金运行监控平台，通过平台的运行监控功能，能有效地通过分析策略的交易行为来加快调试策略实现逻辑中的问题。

通过如下几个步骤进行：

1.  在掘金运行监控平台上新建一个策略，实际上是分配策略所用ID，进行初化资金的设置。调试阶段策略最好是独立运行，不加入某一策略组。风控条件可以先不用设置，在策略调试基本完成后正式运行时再做。这部分操作需要有管理员权限才可以进行，具体设置步骤请参考<掘金量化交易平台操作手册>。

2.  获得平台分配的策略ID后，把相应字串拷贝到策略配置文件中，按照实际情况配置策略的连接URI，检查相关服务运行情况及网络连接，保证策略与平台间能够正常通信。

   
	配置文件示例如下，假定平台服务运行于本地局域网中服务器192.168.1.16上，平台为策略分配的ID为03883f97-4d60-4843-bbc2-cc8752a04691。
	
```ini
    [gm]
    md_uri=tcp://211.154.152.181:5103       ## 使用实时行情服务
    tr_uri=tcp://211.154.152.181:5050       ## 交易服务
    query_uri=tcp://211.154.152.181:5104    ## 历史行情服务
    #md_uri=file://<path>/data_file.tick     ## 使用本地行情数据文件
    #md_uri=playback://211.154.152.181:5109  ## 使用回放行情服务	   
    username=username
	password=yourpassword
	strategy_id=03883f97-4d60-4843-bbc2-cc8752a04691
    subscribe_symbols=SHFE.cu1401.TICK,SHFE.cu1403.TICK
    playback_start_time='2013-04-12 00:00:00'  ## 回放行情服务参数
    playback_end_time='2013-12-12 00:00:00'    ## 回放行情服务参数
    playback_speed=100                         ## 回放行情服务参数   
```
