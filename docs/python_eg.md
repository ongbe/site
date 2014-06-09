---
layout: docs
title: Python策略示例  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/python_example/
---

## 一个简单的策略示例

下面是一个完整的Python策略程序示例，主要展现如何初始化和运行过程，策略逻辑很简单，每10个tick开仓或平仓。这里为了演示，列出了全部的订单和回报事件处理示例供参考，实际编写策略时，未必需要处理这么多事件，请视需要处理。

```python

#!/usr/bin/env python
# encoding: utf-8


from gmsdk.future import StrategyBase
from gmsdk import errors


class StrategySimple(StrategyBase):

    def __init__(self, *args, **kwargs):
        super(StrategySimple, self).__init__(*args, **kwargs)
        self.flag = True
        self.count = 0

    def on_tick(self, tick):
        ''' 收到tick事件，在这里添加策略逻辑。我们简单的每10个tick开仓/平仓，以最新价下单。'''
        print 'tick: time=%s symbol=%s price=%s' % (tick.transact_time, tick.symbol, tick.last_price, )
        if not (self.count % 10):
            if self.flag:
                self.open_long(tick.symbol, tick.last_price, 1)    # 最新价开仓一手
            else:                                                   
                self.close_long(tick.symbol, tick.last_price, 1)   # 最新价平仓一手
                                                                    
        self.flag = not self.flag                                   
        self.count += 1

    def on_bar(self, bar):
        ''' 收到bar事件。这里仅作演示输出，没策略逻辑。'''
        print ('bar: time=%s symbol=%s close_price=%s bar_type=%s' % 
            (bar.bar_time, bar.symbol, bar.close, bar.bar_type))

    def on_trade(self, trade):
        ''' 收到trade事件。这里仅作演示输出，没策略逻辑。'''
        print 'trade: time=%s symbol=%s price=%s' % (trade.transact_time, trade.symbol, trade.close)

    def on_execution(self, execution):
        ''' 委托执行回报，订单的任何执行回报都会触发本事件，通过execution可访问回报信息。'''
        print ('execution: time=%s cl_ord_id=%s exec_type=%s' % 
            (execution.transact_time, execution.cl_ord_id, execution.exec_type))

    def on_order_rejected(self, order):
        ''' 订单被拒绝时，触发本事件。order参数包含最新的order状态。'''
        print 'order rejected: cl_ord_id=%s' % order.cl_ord_id

    def on_order_new(self, order):
        ''' 当订单已被交易所接受时，触发本事件。order参数包含最新的order状态。'''
        print 'order new: cl_ord_id=%s' % order.cl_ord_id

    def on_order_filled(self, order):
        ''' 订单全部成交时，触发本事件。order参数包含最新的order状态。'''
        print 'order filled: cl_ord_id=%s' % order.cl_ord_id

    def on_order_partially_filled(self, order):
        ''' 订单部分成交时，触发本事件。order参数包含最新的order状态。'''
        print 'order partially filled: cl_ord_id=%s' % order.cl_ord_id

    def on_order_stop_executed(self, order):
        ''' 订单被停止执行时，触发本事件, 比如限价单到收市仍未成交，作订单过期处理。
        order参数包含最新的order状态。'''
        print 'order stop executed: cl_ord_id=%s' % order.cl_ord_id

    def on_order_cancelled(self, order):
        ''' 撤单成功时，触发本事件。order参数包含最新的order状态。'''
        print 'order cancelled: cl_ord_id=%s' % order.cl_ord_id

    def on_order_cancel_rejected(self, execution):
        ''' 撤单请求被拒绝时，触发本事件'''
        print 'order cancel rejected: cl_ord_id=%s' % execution.cl_ord_id

    def on_error(self, code):
        ''' 错误处理，收到错误事件时，可以根据error_code判断是什么错误，然后进行处理。
        以下示例实时行情/交易断线后，重新连接的过程。'''
        print 'error: code=%s' % code
        if code == errors.ERR_MD_LIVE_CONNECT:
            self.md_live.reconnect()
        elif code == errors.ERR_TRADE_CONNECT:
            self.tr_live.reconnect()


if __name__ == '__main__':
    # init with config file
    #ret = StrategySimple(config_file='test_strategy.ini').run()

    # init by arguments
    strategy = StrategySimple(
        md_uri='tcp://211.154.152.181:5106',
        tr_uri='tcp://211.154.152.181:5050',
        query_uri='tcp://211.154.152.181:5104',
        username='your username',
        password='your password',
        strategy_id='your strategy_id',
        subscribe_symbols='CFFEX.IF1406.tick,CFFEX.IF1406.bar.60',  # 订阅tick和60s周期bar
    )
    ret = strategy.run()

    print 'strategy exit: ', ret

```

推荐使用配置文件方式, 例如，以下是行情回放情况下的配置文件test_strategy.ini的内容示例, 相对实时行情而言，更换了md_uri,增加了回放需要的几个参数，回放开始时间，结束时间，回放速度:
```
        md_uri='playback://211.154.152.181:5109'
        tr_uri='tcp://211.154.152.181:5050'
        query_uri='tcp://211.154.152.181:5104'
        username='your username'
        password='your password'
        strategy_id='your strategy_id'
        subscribe_symbols='CFFEX.IF1406.tick,CFFEX.IF1406.bar.60'         playback_start_time='2014-05-08 09:00:00'
        playback_end_time='2014-05-09 16:00:00'
        playback_speed=10
```
