# 第三阶段报告

## 功能介绍

- 支持程序自动下棋功能（即AI的实现）。

- 更新了`START_TURN_OP`的格式和行为。

- 玩家回合时限设定为15秒，并用`unix`时间戳消除了时间不统一的问题，玩家超时未行动将被判负。

- 有相应的视角变化，即每个玩家的初始区域在客户端下方。

- 允许玩家查看指定玩家的最后一步行棋路径。

- 展示了连跳的过程（虽然比较生硬）


## 具体实现

### AI的实现

#### 算法

对于AI的算法实现我们最初有多种想法，最开始是采用了min-max树搜索加上alpha-beta剪枝的方法，同时，我们也考虑到了这个算法的一些缺陷——对手的智能程度。这个算法是基于对手会干扰我的行棋得分而运作的，也就是说，如果对手根本不干扰我的行棋，我的行棋得分远远不如贪心来的更高。同时我们小组在实现这个算法的过程中，并没有智能到干扰对手行棋，因为我们的最终判断是走我们可能分数中最小值的最大值。在考虑到其他小组会使用此算法的情形下，我们只好将算法更改为更为简单却有效的贪心算法。并且，以我们的评分函数为基础的情况下，贪心算法确实胜过了前者，于是我们最后选择了胜利的贪心算法。而贪心算法的具体实现就十分简单了，我们对每个棋子的可能行棋方式进行了一个评分，选取最终评分最高的方式行棋。

#### 评分函数

这可以说是AI最重要的一部分，我们小组实现的评分函数只与我们自身棋子的位置有关，具体实现上，我们对每个棋子的位置进行一个评分，然后相加得到这个棋盘的分数。对于每个棋子，我们的分数由“到终点的距离得分”“离中线的偏移罚分”“末子的偏移罚分”三部分组成，值得一提的是，在最后会出现几种特殊情况导致棋子反复横跳，对于这几种情况我们进行了讨论并赋予相应的分数，这样整个评分函数就比较完善了。

##### 两个细节
1. 偏移罚分是用偏移距离`d`乘以`biasFine`来得到。`biasFine`会根据棋子的具体位置改变，如果偏移过多就会相应增加，防止棋子只计算前进分数而忽略了偏移罚分走到棋盘的边缘位置影响最终的胜率。
2. 旋转棋盘这个功能其实很大程度上减少了我们组`AI`的实现难度，因为玩家都是在`D`区域，所以中线竖直，终点线水平，对于分数计算和调错来说十分方便，只需要写`D`区域的`AI`模板，然后在搜索的时候分别搜不同颜色的棋子就可以了。

#### 开关功能

设置一个布尔变量，其真假对应自动下棋模式和人工下棋模式。当轮到当前玩家行棋且布尔变量为真是调用AI函数，且禁用点击函数。其他则与第二阶段相同。

### 其余功能的实现

#### 旋转棋盘（保持玩家在D区域）

需要修改三个部分：色块，棋子初始位置编号，棋盘坐标转换。其中，色块和位置编号保证棋盘呈现的状态改变，棋盘坐标转换保证了其他客户端传送的行棋路径的统一。

#### 路径显示

当服务端发送行棋路径时对应记录每个玩家的路径，调用`update()`函数显示行棋路径。

## 大作业总结

### 小组分工

第一阶段：夏有阳

第二阶段：崔钰锟，夏有阳

第三阶段：刘博宇，夏有阳

注：因为夏有阳参与了第一阶段的设计，所以第二、第三阶段均需要他帮助来实现代码。

### 遇到的问题和相应的解决方法

#### bug
数组越界和循环条件的错误。数组越界是AI在记录可能路径时出错，循环条件是在设计排名展示时出错，通过`qDebug()`输出结果，最后都解决了。

#### 值得一提的东西

对于美工，我们确实是绞尽了脑汁，中途也尝试写了一些比较清奇的东西，不过碍于server端没有相应的信号也只能放弃，最后的美工也是达到了相对满意的地步。

#### 画外音

鉴于此次大作业的提交方式比较正式，以后也可以查看，就谈谈自己的几个观点。

此次大作业的完成确实经历了不少波折，从最开始的满怀兴奋到后来被一堆bug搞的焦头烂额，不过所幸的是，我们最终能相对完整的实现要求的功能。

非常能体会到一个team的重要性，真的要我一个人写的话压力可能会很大，这次大作业毕竟不像上次那么简单。这种有问题就能一起解决的氛围令我十分享受，总的来说，此次的合作还是非常愉快的。

这么长的程序确实看着挺吓人，但确实由我们几个人合力写出来了，还是蛮有成就感的。希望下次一个人写大作业还能写出这么漂亮的程序来吧。