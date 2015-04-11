---
layout: news_item
title:  Matlab实盘交易时代来临！
date: "2015-04-11 19:00:00 +0800"
author: 掘金团队
version: 
categories: [产品新闻]
---

各位掘金宽客们，大家好。近期股市风起云涌，掘金团队与时俱进，为大家带来了新的掘金神器：**掘金量化平台 Matlab版Toolbox正式发布，Matlab实盘交易时代真正来临！**

您没看错，Matlab宽客们，从此以后不用再求别人重写您的策略了，您研究的策略直接就可以`实盘交易`！
策略研究->回验->仿真->实盘，通通在Matlab中搞定，您不再需要依赖任何外部力量， 一切都在您的掌控之下。您，就是是量化交易的真正主宰，宽客之王！

掘金toolbox包含了量化云平台的全部功能，您可以订阅全市场实时行情，实时交易股票、期货、期权，轻易完成跨市场套利、量化选股，乃至高频策略；您可以提取历史数据，在线回放历史行情，进行各种分析研究；您可以进行策略研究、模拟交易、仿真交易、真实交易，toolbox的功能涵盖了策略完整的生命周期。

下面，让我们跟随示例一起快速体验一下掘金toolbox各项强大功能吧。相关链接请见：[下载toolbox](/downloads/)，[安装使用](/docs/matlab_tut/)，[详细开发指南](/docs/matlab_api/)。


#### 数据提取示例

```matlab
%初始化平台
gm.Init('cloud.myquant.cn:8000', 'user', 'pwd');

%提取Tick行情
%提取IF1506一个时间段内的tick行情
ticks1 = gm.GetTicks('CFFEX.IF1506', '2015-03-26 09:15:00', '2015-03-26 09:20:00');

%提取IF1506和IF1509最新一笔tick行情
ticks2 = gm.GetLastTicks('CFFEX.IF1506,CFFEX.IF1509');

%提取IF1506最新10笔tick行情
ticks3 = gm.GetLastNTicks('CFFEX.IF1506', 10);

ticks3 =

 exchange     sec_id      utc_time     last_price    open    high     low      bid_p1    bid_p2
    ________    ________    __________    __________    ____    ____    ______    ______    ______

    'CFFEX'     'IF1506'    1.4287e+09      4388        4234    4389    4218.8      4387    0     
    'CFFEX'     'IF1506'    1.4287e+09      4388        4234    4389    4218.8      4387    0     
    'CFFEX'     'IF1506'    1.4287e+09      4388        4234    4389    4218.8      4387    0     
    'CFFEX'     'IF1506'    1.4287e+09      4388        4234    4389    4218.8      4387    0     
    'CFFEX'     'IF1506'    1.4287e+09    4387.6        4234    4389    4218.8    4387.4    0     
    'CFFEX'     'IF1506'    1.4287e+09      4388        4234    4389    4218.8    4387.4    0     
    'CFFEX'     'IF1506'    1.4287e+09    4387.6        4234    4389    4218.8    4387.4    0     
    'CFFEX'     'IF1506'    1.4287e+09      4388        4234    4389    4218.8    4387.4    0     
    'CFFEX'     'IF1506'    1.4287e+09    4387.4        4234    4389    4218.8    4387.4    0     
    'CFFEX'     'IF1506'    1.4287e+09    4387.8        4234    4389    4218.8    4387.4    0     

%提取Bar行情
%提取IF1506一个时间段内的bar行情
bars1 = gm.GetBars('CFFEX.IF1506', 60, '2015-03-26 09:15:00', '2015-03-26 15:20:00'); 

%提取IF1506和IF1509最新一笔bar行情
bars2 = gm.GetLastBars('CFFEX.IF1506,CFFEX.IF1509', 60); 

%提取IF1506最新10笔bar行情
bars3 = gm.GetLastNBars('CFFEX.IF1506', 60, 10); 

bars3 =

    exchange     sec_id        bar_time       bar_type     utc_time      open     close      high 
    ________    ________    ______________    ________    __________    ______    ______    ______

    'CFFEX'     'IF1506'    20150410151400    60          1.4287e+09      4385      4388      4389
    'CFFEX'     'IF1506'    20150410151300    60          1.4287e+09      4385    4385.2    4385.6
    'CFFEX'     'IF1506'    20150410151200    60          1.4286e+09    4380.2    4384.8    4384.8
    'CFFEX'     'IF1506'    20150410151100    60          1.4286e+09      4381    4380.2      4381
    'CFFEX'     'IF1506'    20150410151000    60          1.4286e+09      4383    4380.4    4383.2
    'CFFEX'     'IF1506'    20150410150900    60          1.4286e+09    4376.8    4382.8      4384
    'CFFEX'     'IF1506'    20150410150800    60          1.4286e+09      4377    4377.2    4377.2
    'CFFEX'     'IF1506'    20150410150700    60          1.4286e+09    4376.6    4376.8    4377.2
    'CFFEX'     'IF1506'    20150410150600    60          1.4286e+09      4374    4376.4      4378
    'CFFEX'     'IF1506'    20150410150500    60          1.4286e+09      4373    4373.8      4375

%提取日频行情
%提取IF1506一个时间段内的日频行情
dbars1 = gm.GetDailyBars('CFFEX.IF1506', '2015-01-01 09:15:00', '2015-03-26 15:20:00');

%提取IF1506和IF1509最新一笔日频行情
dbars2 = gm.GetLastDailyBars('CFFEX.IF1506,CFFEX.IF1509');

%提取IF1506最新10笔日频行情
dbars3 = gm.GetLastNDailyBars('CFFEX.IF1506', 10);

dbars3 = 

    exchange     sec_id     bar_time    bar_type     utc_time      open     close      high 
    ________    ________    ________    ________    __________    ______    ______    ______

    'CFFEX'     'IF1506'    0           0           1.4286e+09    4257.8    4234.2    4279.6
    'CFFEX'     'IF1506'    0           0           1.4285e+09      4260    4255.8    4297.2
    'CFFEX'     'IF1506'    0           0           1.4284e+09      4250      4257    4271.4
    'CFFEX'     'IF1506'    0           0            1.428e+09      4118      4224    4229.6
    'CFFEX'     'IF1506'    0           0            1.428e+09    4163.6      4124    4192.6
    'CFFEX'     'IF1506'    0           0           1.4279e+09    4068.2      4140    4188.8
    'CFFEX'     'IF1506'    0           0           1.4278e+09    4180.6    4048.8    4222.2
    'CFFEX'     'IF1506'    0           0           1.4274e+09      3977    3961.8    4020.6
    'CFFEX'     'IF1506'    0           0           1.4268e+09    3900.2      3957      3972
    'CFFEX'     'IF1506'    0           0           1.4268e+09    3910.6    3892.6    3914.2

```

#### 订阅实时行情示例

```matlab
function [  ] = TestMDLive(  )

%订阅中金所所有实时行情
gm.Init('cloud.myquant.cn:8000', 'user', 'pwd', MDMode.MD_MODE_LIVE, 'CFFEX.*');

%设置tick事件处理函数
gm.SetTickHandle(@OnTick);
%设置bar事件处理函数
gm.SetBarHandle(@OnBar);

gm.Run();

end

function [  ] = OnTick( tick )

x = sprintf('逐笔行情: %s.%s %d', char(tick{1,'exchange'}), char(tick{1,'sec_id'}), tick{1,'last_price'});
disp(x);

end

function [  ] = OnBar( bar )

x = sprintf('分时行情: %s.%s %d %d', char(bar{1,'exchange'}), char(bar{1,'sec_id'}), char(bar{1, 'bar_type'}), bar{1,'close'});
disp(x);

end

```

#### 订阅模拟行情示例

```matlab
function [  ] = TestMDSim(  )

%订阅CFFEX.IF1506 Tick数据
gm.Init('cloud.myquant.cn:8000', 'user', 'pwd', MDMode.MD_MODE_SIMULATED, 'CFFEX.IF1506.Tick');

%设置tick事件处理函数
gm.SetTickHandle(@OnTick);

gm.Run();

end

function [  ] = OnTick( tick )

x = sprintf('逐笔行情: %s.%s %d', char(tick{1,'exchange'}), char(tick{1,'sec_id'}), tick{1,'last_price'});
disp(x);

end

```

#### 行情回放示例

```matlab
function [  ] = TestMDPlayBack(  )

%回放CFFEX.IF1506 至2015-04-03 9点至10的tick 数据
gm.Init('cloud.myquant.cn:8000', 'user', 'pwd', MDMode.MD_MODE_PLAYBACK, 'CFFEX.IF1506.tick', '2015-04-03 09:00:00', '2015-04-03 10:00:00');
gm.SetTickHandle(@OnTick);
gm.Run();

end

function [  ] = OnTick( tick )

x = sprintf('逐笔行情: %s.%s %d', char(tick{1,'exchange'}), char(tick{1,'sec_id'}), tick{1,'last_price'});
disp(x);

end
```

#### 一个简单的策略示例

下面是一个matlab策略程序示例，主要展现如何初始化和运行过程，策略逻辑很简单，每个bar开仓或平仓, 每个tick打印最新成交价。这里为了演示，列出了全部的订单和回报事件处理示例供参考，实际编写策略时，未必需要处理这么多事件，请视需要处理。
注：gm.Init中的参数：账号/密码/策略ID(strategy_id)请替换为您自己的值

```matlab

function [  ] = TestStrategy(  )

%初始化策略
%行情服务器为cloud.myquant.cn:8000
%交易服务器为cloud.myquant.cn:8001
%订阅实时行情，IF1506的Tick数据和30秒分时数据
%策略ID为e6208536-d688-11e4-9478-00163e003744
%实时行情模式下，时间段设置为空('')

gm.Init('cloud.myquant.cn:8000', 'pwd', 'user', MDMode.MD_MODE_LIVE, 'CFFEX.IF1506.tick,CFFEX.IF1506.bar.30', '', '', 'cloud.myquant.cn:8001', 'e6208536-d688-11e4-9478-00163e003744');

%设置逐笔行情(Tick)处理函数
gm.SetTickHandle(@OnTick);

%设置分时行情(Bar)处理函数
gm.SetBarHandle(@OnBar);

%设置订单状态变化处理函数
gm.SetOrderHandle(@OnOrder);

%设置执行回报处理函数
gm.SetExecRptHandle(@OnExecRpt);

%设置登录gm服务器成功处理函数
gm.SetLoginHandle(@OnLogin);

%设置系统错误处理函数
gm.SetErrorHandle(@OnError);

%启动运行
gm.Run();

end

function [  ] = OnTick( tick )

%处理逐笔行情事件

x = sprintf('行情报价: %s,%s,%d', char(tick{1,'exchange'}), char(tick{1,'sec_id'}), tick{1,'last_price'});
disp(x);

end

function [  ] = OnBar( bar )

%处理分时行情

global f;
if isempty(f)
   f = 0; 
end

if f == 0
   x = sprintf('开多仓 : %s,%s,%d', char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'});
   disp(x);
   
   %开多仓
   gm.OpenLong(char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'}, 1);
   f = 1;
else
    x = sprintf('平多仓 : %s,%s,%d', char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'});
    disp(x);
    
    %平多仓
    gm.CloseLong(char(bar{1,'exchange'}), char(bar{1,'sec_id'}), bar{1,'close'}, 1);
    f = 0;
end

end

function [  ] = OnOrder( order )

   %处理委托单变化
   x = sprintf('委托 %s.%s 状态：%d', char(order{1, 'exchange'}), char(order{1 ,'sec_id'}), order{1, 'status'});
   disp(x);
   
end


function [  ] = OnExecRpt( rpt )

% 处理执行回报

x = sprintf('执行回报 类型: %d', rpt{1, 'exec_type'});
disp(x);

end

function [  ] = OnLogin( mask )

%服务器登录信息 mask=1 行情服务；mask=2 交易服务
if mask == 1
    disp('登录行情服务成功');
else
    disp('登录交易服务成功');
    
    %查询所有未结的委托
    orders = gm.GetUnfinishedOrdes();
    disp(orders);
    
    %查询资金状况
    cash = gm.GetCash();
    disp(cash);
    
    %查询持仓情况
    positions = gm.GetPositions();
    disp(positions);
end

end

function [  ] = OnError( code, msg )

%处理错误
x = sprintf('error: code = %d, msg = %s', code, msg);
disp(x);

end

```
