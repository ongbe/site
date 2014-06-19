---
layout: docs
title: C#策略示例  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/csharp_examples/
---

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
            s.Init(
                "tcp://211.154.152.181:5106",
                "tcp://192.168.1.10:5050",
                "tcp://211.154.152.181:5104",
                "your username",
                "your password",
                "your strategy id",
                "CFFEX.IF1406.tick,CFFEX.IF1406.bar.60"); // 订阅tick和60s周期bar
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
        public override void OnTick(ref Tick tick)
        {
            Console.WriteLine(
                "tick {0}: time={1} symbol={2} last_price={3}", 
                this.count, 
                tick.trade_time, 
                tick.symbol, 
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
        public override void OnBar(ref Bar bar)
        {
            Console.WriteLine(
                "bar: time={1} symbol={2} close_price={3}", 
                this.count, 
                bar.bar_time, 
                bar.symbol, 
                bar.close);
        }

        /// <summary>
        /// 委托执行回报，订单的任何执行回报都会触发本事件，通过execution可访问回报信息。
        /// </summary>
        /// <param name="execution"></param>
        public override void OnExecution(ref Execution execution)
        {
            Console.WriteLine(
                "execution: cl_ord_id={0} price={1} amount={2} exec_type={3}", 
                execution.cl_ord_id, 
                execution.price, 
                execution.amount, 
                execution.exec_type);
        }

        /// <summary>
        /// 订单被拒绝时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderRejected(ref Order order)
        {
            Console.WriteLine("order rejected: {0} {1}", order.cl_ord_id, order.ord_rej_reason);
        }

        /// <summary>
        /// 当订单已被交易所接受时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderNew(ref Order order)
        {
            Console.WriteLine("order new: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 订单全部成交时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderFilled(ref Order order)
        {
            Console.WriteLine("order filled: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 订单部分成交时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderPartiallyFilled(ref Order order)
        {
            Console.WriteLine("order partially filled: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 订单被停止执行时，触发本事件, 比如限价单到收市仍未成交，作订单过期处理。
		/// order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderStopExecuted(ref Order order)
        {
            Console.WriteLine("order stop executed: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 撤单成功时，触发本事件。order参数包含最新的order状态。
        /// </summary>
        /// <param name="order"></param>
        public override void OnOrderCancelled(ref Order order)
        {
            Console.WriteLine("order cancelled: {0}", order.cl_ord_id);
        }

        /// <summary>
        /// 撤单请求被拒绝时，触发本事件
        /// </summary>
        /// <param name="execution"></param>
        public override void OnOrderCancelRejected(ref Execution execution)
        {
            Console.WriteLine("order cancel failed: {0}", execution.cl_ord_id);
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
