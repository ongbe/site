---
layout: docs
title: C#策略示例  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/csharp_examples/
---

### 一个简单的策略示例

下面是一个完整的c#策略程序示例，主要展现如何初始化和运行过程，策略逻辑很简单，每10个tick开仓或平仓。这里为了演示，列出了全部的订单和回报事件处理示例供参考，实际编写策略时，未必需要处理这么多事件，请视需要处理。

## 掘金SDK使用示例

### 如何使用SDK

1. 下载sdk并解压，sdk包含两个dll：`libgmnet.dll，libgm.dll`。策略运行依赖于这两个库文件，需要放在策略的可执行文件目录。

2. 在visual studio中创建c#项目， 项目引用中添加对`libgmnet.dll`的引用。

3. 编写自己的策略。策略继承自StrategyBase，添加自己的策略逻辑代码。配置好策略ID（在掘金终端中创建策略时，会分配策略ID，复制过来即可），服务地址等参数。

4. 编译，运行，搞定！策略运行起来后，在掘金终端中可以看到策略的运行的实时状态。

### 一个简单的策略示例

下面是一个完整的c#策略程序示例，主要展现如何初始化和运行过程，策略逻辑很简单，每10个tick开仓或平仓。这里为了演示，列出了全部的订单和回报事件处理示例供参考，实际编写策略时，未必需要处理这么多事件，请视需要处理。

```c#
using System;
using GMSDK.Future;
using GMSDK;

namespace test_strategy_simple
{
    class Program
    {
        static void Main(string[] args)
        {
            StrategySimple s = new StrategySimple();
            int ret = s.Init(
                "120.24.228.187:8000",
                "120.24.228.187:8001",
                "demo",
                "demo",
                "strategy_2",
                "*",
                MDMode.MD_MODE_SIMULATED
                ); // 订阅tick和60s周期bar
            s.Run();
        }
    }

    public class StrategySimple : StrategyBase
    {
        private bool flag = true;
        private int count = 0;

        /// <summary>
        /// 收到tick事件，在这里添加策略逻辑。我们简单的每10个tick开仓/平仓，以最新价下单。
        /// </summary>
        /// <param name="tick"></param>
        public override void OnTick(Tick tick)
        {
            Console.WriteLine(
                "tick {0}: time={1} exchange={2} sec_id={3} last_price={4}", 
                this.count, 
                tick.trade_time, 
                tick.exchange,
                tick.sec_id,
                tick.last_price);

            if (this.count % 10 == 0)
            {
                if (this.flag)
                {
                    OpenLong(tick.symbol, tick.last_price, 1);  //最新价开仓一手
                }
                else
                {
                    CloseLong(tick.symbol, tick.last_price, 1); //最新价平仓一手
                }
            }

            this.count++;
            this.flag = !this.flag;
        }

        /// <summary>
        /// 收到bar事件。这里仅作演示输出，没策略逻辑。
        /// </summary>
        /// <param name="bar"></param>
        public override void OnBar(Bar bar)
        {
            Console.WriteLine(
                "bar: time={1} exchange={2} sec_id={3} last_price={4}", 
                this.count, 
                bar.bar_time, 
                bar.exchange,
                bar.sec_id,
                bar.close);
        }

        /// <summary>
        /// 委托执行回报，订单的任何执行回报都会触发本事件，通过rpt可访问回报信息。
        /// </summary>
        /// <param name="rpt"></param>
        public override void OnExecRpt(ExecRpt rpt)
        {
            Console.WriteLine(
                "rpt: cl_ord_id={0} price={1} amount={2} exec_type={3}", 
                rpt.cl_ord_id, 
                rpt.price, 
                rpt.amount, 
                rpt.exec_type);
        }

        /// <summary>
        /// 订单被拒绝时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderRejected(Order order)
        {
            Console.WriteLine("order rejected: {0} {1}", order.cl_ord_id, order.ord_rej_reason);
        }

        /// <summary>
        /// 当订单已被交易所接受时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderNew(Order order)
        {
            Console.WriteLine("order new: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 订单全部成交时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderFilled(Order order)
        {
            Console.WriteLine("order filled: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 订单部分成交时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderPartiallyFilled(Order order)
        {
            Console.WriteLine("order partially filled: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 订单被停止执行时，触发本事件, 比如限价单到收市仍未成交，作订单过期处理。
		/// order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderStopExecuted(Order order)
        {
            Console.WriteLine("order stop executed: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 撤单成功时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderCancelled(Order order)
        {
            Console.WriteLine("order cancelled: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 撤单请求被拒绝时，触发本事件
        /// </summary>
        /// <param name="rpt"></param>
        public override void OnOrderCancelRejected(ExecRpt rpt)
        {
            Console.WriteLine("order cancel failed: {0}", rpt.cl_ord_id);
        }

        /// <summary>
        /// 错误处理，收到错误事件时，可以根据error_code判断是什么错误，然后进行处理。
        /// 以下示例实时行情/交易断线后，重新连接的过程。
        /// </summary>
        /// <param name="error_code"></param>
        public override void OnError(int error_code)
        {
            Console.WriteLine("on_error: {0}", error_code);
            if (error_code == ErrorCode.ERR_MD_LIVE_CONNECT)
            {
                MdLive.Instance.Reconnect();
            }
            else if (error_code == ErrorCode.ERR_TRADE_CONNECT)
            {
                TradeLive.Instance.Reconnect();
            }
        }
    }
}

```


### 更多示例

我们将陆续在github提供更丰富的策略示例程序，请猛击： `https://github.com/myquant/gmsdk`
