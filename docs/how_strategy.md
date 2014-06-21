---
layout: docs
title: 如何开发策略  
prev_section: 
next_section: 
permalink: /docs/how_to_do/
---

# 策略是怎么开发出来的？

开发一个策略，让它自动地发现机会，并自动执行交易，然后开始挣钱，听起来很牛很酷，非常地令人神往，不是吗？

但牛的策略是怎么开发出来的呢？

具体地去讲如何发现规律、找到相关性、建模、回测、模拟交易、仿真、实盘、优化这一系列过程太枯燥太繁琐了，估计也没多少人感兴趣，懂行的人不屑于看，没啥新意，行业外的人还是看不明白：到底说的啥?

所以呢，我想讲个通俗版的策略开发过程，也叫"开发一个策略的十个步骤"，方便大家了解一下量化策略师是干什么的。

那就从大家都熟悉的冰箱说起吧，我们的故事跟冰箱有关。

故事主人公： A， 职业：量化策略师

时间： 某个晚饭时间
地点： 家中厨房

场景： 下班回到家，发现没人做饭了，为什么？我也不知道，留给大家想像吧......

故事梗概： A拉开冰箱门，准备找点吃的，发现一个苹果，运气不坏！可是仔细看了下苹果，已经是皱巴巴的了，不知道哪天的。摇摇头，顺手扔垃圾桶了；算了吧，下午还吃了点，不是太饿，先看看书吧，一会出去宵夜，刚坐下，想想还是泡杯茶先，可是坏运气还在，失手把水倒在书上了，这可是最珍爱的一本书，怎么办？

真倒霉！洗洗睡了。 － The End －

---

一般人的故事到这里就完了，但策略师的故事是这样的:

策略师嘛，思维是跟别人有点不一样的，他的脑袋闲不住了，开始想啊想......

1. 苹果放在冰箱会变皱，＃＠％＄＄＄＄＄！＆……  －－发现了一个现象；
2. 对苹果变皱这一现象进行分析，提出一个假说，冰箱会慢慢让里面的物体失去水分－－提出假说，并建立了一个简单的模型。
3. 等等，这个苹果是放进去变皱的呢？还是放进去前就是皱的呢？时间久远，无法回忆起来了 －－ 这是个普遍规律呢，还是只是个特例？

	需要验证下，再去买个苹果来做试验吗？太麻烦了，先弄张面巾纸湿点水测试一下 －－ 注意开发成本，还是先搞个简单的模型验证下吧！

	十分钟后，拿出来看看，纸变干了，模型有效！ 开始思考了，如何利用这个规律？首先，这个规律有通用性吗？其次，模型可以优化不？比如，如何让纸干得更快一点？能不能用来制苹果干？  －－ 关注模型的泛化能力

4. 仔细思考，好一会，＠％＄＄＄＄＄！＆……  觉得应该可以 －－ 这个假说有实用价值，值得做进一步的测试。

	好吧，开始精细些的试验了，先拿出两张面巾纸，面积一样，滴上一样多的水，5滴，分别放入冷藏和冷冻室。隔一分钟检查下，看看谁干得快 －－ 注意，开始量化了哦

	结果发现，冷冻室的纸干得更快，并且变形更少。－－ 参数选取成功！

5. 苹果跟纸巾还是不一样的，还是去买点苹果试一下吧 －－ 仿真
6. 仔细思考下，为什么能行得通？ －－ 模型的可解释性，无法解释的模型千万别用，无法解释的参数调整结果更加要小心，要抵制住诱惑
7. 仔细思考，此处省略8千字，结论：逻辑成立、效果确定，可以用！开始自制苹果干、用来干书 －－ 上线
8. 书湿了，放进冰箱冷冻室就可以快速弄干，有效，下次可用； 开心果在外面放潮了，放冰箱冷藏一会，口感变脆了，模型依然有效 －－ 模型扩展应用范围
9. 回顾一下过往操作，怎么才能干得更快，效果更好呢？ 开始探索：调整冰箱温度、预先把外面的水甩开  －－ 参数优化
10. 这个经验不错，值得向朋友推广 －－ 包装发行，产品上市

好吧，我承认故事有点扯，但量化策略师开发一个策略基本上就是这样的:

>发现规律－提出假说－建立模型验证－思考泛化能力，防止过度拟合－模型精化－实现策略－仿真交易－上线真实交易 － 不断优化，循环以上步骤。

