---
layout: docs
title: 策略风险控制  
prev_section: 
next_section: 
permalink: /docs/risk_control/
---

无庸置疑，交易中风险无处不在，无论是人工交易还是自动化的程序交易，都必须要认真对待风险控制这一重要工作环节。
交易中的风险多种多样，大到市场行情的突变，超出现有策略模型的范畴，小到某部分实现代码的隐藏BUG在特定情况下被触发，都可能随时给系统带来严重的损失甚至是灾难。

单有事后风控显然是不够的，风险必须提前防范。怎么防范呢？笔者认为应该建立一套完整的体系，借助系统的力量来进行。

###系统的力量
通常，成熟的交易者都讲要建立自己的系统，符合个人交易风格的系统千人千面，但往往都有一个共同的点，强调执行的纪律性，其中最最关键的部分其实说的就是风险控制。

###引子

很多人都有自己下厨做菜的经历，个人感觉做饭是一项挺复杂的系统工程，尤其是前期的准备工作，费时费力，大厨的掌勺工作那部分到是考究天赋和技术的，工作量并不太大，但前期的准备工作量非常大，其中的切菜也是个危险活，做过饭的人应该有体会。

没有做过饭的人至少应该都见过“切菜”吧，电视上还偶尔有厨艺比赛，其中一项就是比"刀工"，如何把菜切得又漂亮，又方便烹饪，并且速度还要快，这确实不是一件容易的事，更可怕的是经常还会不小心切到手，我在这里告诉大家一个秘诀，可以保证你以后绝对不会切到手。

秘诀源自小时候奶奶的教导，切菜时，按住菜的手暴露在刀口下，是最危险的，但只要把四个指头自然弯曲起来，指头向下轻按住要切的菜，保持第二指节竖直向下，四个手指的第二指节会共同形成一个立面，然后刀背贴着这个立面上下运动，因为正常人的手指做这个动作后，指头都会缩到这个立面远离刀的一侧，正好避开了刀锋，这样，无论你怎样运刀如飞，都不会切到手指了。

这就是**系统的力量**！不是靠你保持谨慎，专心致志地才能防范，那样一旦忽视它就又会跳出导致破坏，而是通过精妙的设计，形成一个安全的"防火墙"系统，来确保系统整体是安全可控的。

###资金层面的风险控制 
从大的层面来讲，资金层面的风险控制应该是第一位的，无论你用传统的资金分配法构造投资组合也好，还是用Risk Parity方法来构造你的投组也好，都是从资金层面对风险进行宏观的控制。

在掘金量化平台上，我们提供了帐户资金分配层面的控制，一个策略组使用同一个或同一组帐户（如股票＋期货），组内的每个策略是一个虚拟的子帐户，各个策略可以进行独立的资金量的控制，平台会利用风控规则进行检查，避免某个策略因为错误过多地使用了资金，从而避免的风险暴露的增加。

###策略层面的风险控制
具体到策略层面，可以进行一些更为具体的风险控制，策略层面的风险控制可以是在策略内部实现的，也可能是实现在策略外部的，比如掘金量化平台中的风控规则系统，就提供了各类常用的风控规则，这样策略可以根据需要挂载，不同策略间也可以共用，避免了重复开发。

站在基金经理，或公司管理者层面来讲，策略内部逻辑是一个具体而细节的东西，很多时候不太可能逐个去详细核实检查，这样的工作量太大而且很难真正落到实处。
如果能从策略执行的角度，在策略交易时进行事中的实时检查，避免风险会更有现实的意义，掘金量化平台就提供了实时的风控检查机制，通过预先设定的各种风控规则来阻止意外的发生，例如，通过限制交易代码、限制持仓量等措施避免策略因为各种原因产生的错误的交易指令发到柜台，从而确保即使策略逻辑中出现了某种错误，其带来的风险总体上仍然是可控的。

####策略内部的风控
对于一个交易策略而言，其核心逻辑就是要在合适的时候发出“入场信号”和“出场信号”。
但不管怎样调优，策略师也很难保证策略发出的每个信号都是正确的，只能通过一些辅助的手段来修正。
例如，根据历史数据的回测结果设定某个条件下入场信号的赢利概率，根据这些数据对入场信号的交易量进行适当的控制，通过仓位的管理来增加赢面。

此外，入场信号在策略内部往往是一系列敏感信号过滤后的结果，具体来讲，就是通过模型的一些条件边界的设定，各个信号阀值的设定，模型内部经过逻辑处理后才最终发出的交易指令，这实际上就是在最后发出交易信号前进行了风险控制；此外，通常还会在策略内部设置了仓位的管理逻辑，通过逐步加仓、或者是第一个信号时只是试探性加仓，在趋势回调时再趁机补仓，从而更好地控制持仓成本；

另外，策略内部往往还有止赢止损的“出场信号”， 常用的有固定的止赢或止损，例如，赢利10个点或亏损3个点就结束这次交易；也有移动的止赢，例如，当价格从最高点回落3个点后止赢，或者是价格穿过MA均线后再止赢，来争取比固定止赢更多的赢利。

####策略外部的风控
首先，风控逻辑是无法完全实现在策略内部的，策略本身是一个独立完备的实体，但无论策略师怎样努力，都无法避免一些错误，也无法完整地考虑到市场可能出现的所有情况，此外，策略的核心目的是为了赚钱，把所有的可能的情况完整地分析并实现在策略代码逻辑中也基本上是不可能的，这样的策略代码会变得非常臃肿并且难以维护；
其次，很多风控的逻辑是相同的，如果把类似的代码在不同的策略中复制粘贴也不是一个好的管理办法，一方面浪费开发精力，另一方面也可能会导致一些不必要的错误。因此一些通用的、共性的策略风控逻辑应该独立提取出来，以风控规则的形式来实现灵活的使用配置，根据不同的策略模型来选择性地加载一些风控规则，可以灵活地调节风险控制与策略执行效率的平衡。
最后，一些策略模型因为自身的特殊性，需要一些特殊的风险控制逻辑，可以单独进行定制的风控规则开发，然后挂到系统中，这样能够把策略逻辑和风险控制适当地隔离，从而获得更好的赢利稳定性。

与其在策略内部实现复杂的逻辑，不如在策略外部进行限制措施，确保一些对赢利会造成伤害、现有策略逻辑下可能会发生的错误能在最后关口被阻止，以第三方的角度来控制策略风险，从这个意义上来讲，策略外部的风控无论从实现成本还是客观性上都更加地有意义。
