---
layout: docs
title: R语言策略的编写  
prev_section: github-pages
next_section: manual-deployment
permalink: /docs/gnu_r_examples/
---

在前面的安装介绍中，我们已经知道了如何使用gmsdk的R语言扩展包来查询历史数据，下面用一个简单的例子，说明如何编写一个R语言的策略。

为了方便，直接在代码中增加注释说明。

```
# 设置系统时区，为了避免某些系统不能识别导致时间显示错误
Sys.setenv(TZ='GMT-8')
# 设置时间显示精度，显示毫秒
op <- options(digits.secs=6)

# 事件响应函数，简单回显
echo_data <- function(data) {
  print(data)
}

# 加载gmsdk
library(gmsdk)

# 分别设置实时行情、交易和历史查询的服务地址
live_uri = 'live://211.154.152.181:5103'
trade_uri = 'tcp://211.154.152.181:5050'
query_uri = 'tcp://211.154.152.181:5104'

# 用户名、密码及代码
username = 'a'
password = 'a'
strategy_id = 'strategy_1'
symbols = 'CFFEX.IF1409.bar.15,CFFEX.IF1409.tick'
# 回放相关参数，暂时不用
playback_start_time = '2014-09-05 09:15:00'
playback_end_time   = '2014-09-05 15:30:00'
playback_speed = 100

# 以下是三个服务的分别初始化
#live_init(live_uri, username, password, strategy_id, symbols)
#query_init(query_uri, username, password)
#trade_init(trade_uri, username, password, strategy_id)

# 一起初始化
gm_init(live_uri,    
		trade_uri,
		query_uri,
		username,
		password,
		strategy_id,
		symbols,
		playback_start_time,
		playback_end_time,
		playback_speed)

# 设置行情数据的处理
set_bar_handler(echo_data)
set_tick_handler(echo_data)
set_trade_handler(echo_data)

# 设置交易数据的处理
trade_set_execution_handler(echo_data)
trade_set_order_new_handler(echo_data)
trade_set_order_rejected_handler(echo_data)
trade_set_order_partially_filled_handler(echo_data)
trade_set_order_filled_handler(echo_data)
trade_set_order_stopped_handler(echo_data)
trade_set_order_cancel_rejected_handler(echo_data)
trade_set_trade_error_handler(echo_data)

# 设置交易中的下单，每5个bar下一次多单
bar_count = 0
interval = 5

on_bar <- function(bar) {
  print(bar)
  bar_count <- bar_count + 1
  if (bar_count %% interval == 0) {
    trade_open_long('CFFEX.IF1409', 0.0, 1)
  }
}

# 重新设置分时数据处理函数
set_bar_handler(on_bar)

# 启动服务
gm_start()

tryCatch({
  run()
},  interrupt = function(interrupt) {
  cat(" -- an interrupt caught.\n");
  ##print(interrupt);
}, finally = {
  gm_stop();
  cat("gmsdk service stopped.\n");
})

```
以上代码在有行情时，会输出对应的行情数据，包括股指IF1409的逐笔行情、15秒分时及下单的各级回应。
