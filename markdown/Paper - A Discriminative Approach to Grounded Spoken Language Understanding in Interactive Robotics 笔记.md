# Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记

## Abstract



## 3 Perceptually-informed Interpretation

#### Semantic Map

$\langle\mathcal{M}, \mathbf{E}\rangle$ 

 $\mathcal{M}$ - 2D 地图，

$\mathbf{E}$ - 环境中实体集合

$p(\mathbf{e})=\langle x_1,y_1\rangle$ - 函数$p(\cdot)$可以计算出实体，$\mathbf{e}\in \mathbf{E}$.

$\mathcal{L} \mathcal{R}(\cdot)$ - 可以算出$w_e$(机器人对实体$\mathbf{e}$的参考名称)

#### Interpretation

$\mathbf{THEME}$ - representing the object that is moved during the action. 代表动作要移动的对象

$s=\langle w_1,...,w_2 \rangle  $ - s是一个$w_i$单词的序列


$$
\begin{aligned}
\mathcal{I}(s)=&\{\langle \mathcal{f^i},\mathcal{Arg^i}
\rangle\}
\end{aligned}
$$
$\mathcal{I}(s)$ - 输入一个$s$输出一对关系元组$\mathcal{f^i}$和$\mathcal{Arg^i}$

$f^i$ -is a frame (each anchored in the text through a LU( $lexical$ $unit$ ), e.g. the verb $take$)
$$
\begin{aligned}
\mathcal{I}(s)=&\{\langle \mathcal{f^i},\{
\langle a s_{j}^{i}, f e_{j}^{i}, s h_{j}^{i}\rangle\}\rangle\}
\end{aligned}
$$

$$
\begin{aligned}
\mathcal{I}(s)=&\{\text { Taking },\{\\
&\langle\langle\text { take }\rangle, \mathrm{LU}, \text { take }\rangle \\
&\langle\langle\text { the }, \text { book, on, the, table }\rangle, \text { THEME, book }\rangle\}\rangle\}
\end{aligned}
$$


### 3.1 The Language Understanding Cascade(语言理解级联)

建模为序列标记任务( modeled as sequence labeling
tasks)

分类模型是结构化支持向量机的马尔可夫链$SVM^{hmm}$

这个过程包含Action Detection和Argument Labeling两大步骤

Argument Labeling可进一步分解为Argument Labeling和Argument Classification两个子步骤

每步都被映射到不同的$SVM^{hmm}$序列标记任务中

在训练阶段，SVM算法致力于将单词与特定步长的标签相关联：即线性核函数应用与不同类型的特征，从语言的特征到基于感知的特征。

在分类时，给定一s个句子,$SVM^{hmm}$利用类似维比特解码算法来有效预测序列标签y。

#### Action Detection(AD)

动作检测的目标是找出句子$s$中的所有frame 且在$\mathcal{I}(s)$中填满$f^i$

。这对应一个函数$f_{AD}(s,PM,SM)$，因为标注过程依赖于语言信息以及来自平台模型($PM$)的知识和来自语言地图($SM$)的感知知识。

在马尔可夫链中，状态反映框架标签，因此通过

来自PM的关于机器人的信息被用来表示它能够执行的动作，这些动作会被映射到frame中





## PS：隐马尔科夫链模型的笔记

### **马尔可夫链模型**

转自：https://zhuanlan.zhihu.com/p/151011287

​		    https://zhuanlan.zhihu.com/p/156811173

#### 01 有限状态机FSM

有限状态机表示有限个状态以及这些状态之间的转移和动作等的数学模型

它有四个概念: **State(状态)、Event(事件)、Acton(动作)、Transition(变换)。**

![image-20220404141828295](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/image-20220404141828295.png)

按以上例子解释，他有：

- 三个状态(**State**)：关闭、半开、开启
- 四个事件(**Event**)：10中失败大于5次、5次中失败失败大于等于3词，5次中失败小于3词、十分钟后
- 3个动作(**Acton**)：允许所用HTTP访问、允许一半的HTTP访问、禁止所有HTTP访问
- 3个变换(**Transition**)：关闭到开启、开启到半开、半开到开启、半开到关闭

#### 02马尔可夫性质

​	马尔可夫性质是指**当前的状态只和上一时间状态有关**，在上一时间状态之前的任何状态都与当前状态无关。我们称其符合马尔可夫性质。也可以说是无记忆性的。

#### **03马尔可夫过程**

**马尔可夫过程（Markov process）是一类随机过程。它的原始模型马尔可夫链，由俄国数学家A.A.马尔可夫于1907年提出。**

满足马尔可夫性质的随机过程，称之为马尔可夫过程。

#### **04马尔可夫链通俗理解**

**马尔可夫链是概率论和数理统计中具有马尔可夫性质且存在于离散的指数集和状态空间内的随机过程。适用于连续指数集的马尔可夫链被称为马尔可夫过程，但有时也被视为马尔可夫链的子集，即连续时间马尔可夫链，与离散时间马尔可夫链相对应，因此马尔可夫链是一个较为宽泛的概念。**

##### 一个简单的例子

放暑假了，小王假期里下午2点的活动只有3种，分别是：**看电视、玩游戏、睡觉**。假设小王当天2点的活动与前一天2点的活动有关，而与之前的活动无关，也就是说符合马尔可夫性质。换言之，他前一天到当前的状态变化如下所示：

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-30b3e5770551a1b6b9f333c694c2ef18_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">状态转移</center>

假设状态的变化是有概率的，称之为**状态转移矩阵：**

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-b62bfd4737c4d81259dbaf36fa106b58_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">状态转移矩阵</center>

假设**初始状态概率分布**为: [0.6,0.3,0.1], 那么可以计算出第二天的状态分布：

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-5225ef34144bee425fe089e0b8e08df8_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">初始状态概率分布</center>							

以此类推，那么就可以列出小王在整个假期里面的下午2点状态的所有**状态分布矩阵**：

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-0a7ad987c79e7773ce2ea17c1dc9794a_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">状态分布矩阵</center>

#### **05马尔可夫链数学定义**

对概率空间$(\Omega,\mathcal{F},\mathbb{P})$内以一维可数集为指数集的随机变量集合

### **隐马尔可夫模型**

#### **01隐马尔可夫模型**

隐马尔可夫模型并非俄罗斯数学家马尔可夫发明，而是美国数学家鲍姆等人在20世纪六七十年代发表的一系列论文中提出。**隐含马尔可夫模型的训练方法**(**鲍姆-韦尔奇算法**)也是用他名字命名的。

隐含马尔可夫模型就是之前将的**马尔可夫链的扩展**：任意时刻t状态St不可见，无法通过观察到状态序列S(S1,S2...St)进行推测状态转移概率等参数，但是它能够在**每个时刻t输出Ot,Ot仅跟St相关(独立输出假设)**，O1-Ot称为隐含状态的**可观测状态矩阵**。又因为隐含的状态s1,s2,s3...sn是马尔科夫链，因此鲍姆将该模型称为隐含马尔科夫模型。

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-ef64b5cfa3bffd437b73834415e1f571_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">隐藏状态与观测状态</center>

**举个例子**：仍然是假期中的小王，现在的他无法得知他是在玩游戏、看电视还是在睡觉了，我们只能够观测到他每天的心情，假设玩游戏会开心或难过或平静、看电视会开心或难过或平静、睡觉会开心或难过或平静：

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-3cff0450b3cef205b8b2880f69d0373e_720w-164905803086113.jpg)

<center style="font-size:14px;color:#C0C0C0">隐藏状态与观测状态</center>

那么：

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-4f2e165e428f2ee16c36ae7a660bbe2d_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">可观察状态矩阵</center>

#### **02隐马尔可夫模型概念**

**隐含状态矩阵 $S$**：例如前文例子中的[玩游戏、看电视、睡觉]

**可观测状态矩阵 $O$**：在模型中与隐含状态相关联，可通过直接观测而得到，如[开心、难过、平静...]

**初始状态概率矩阵 $π$**：$t_1$时刻各个状态的概率,如P(玩游戏)、P(看电视)、P(睡觉)

**隐含状态转移概率矩阵 $A$**: 即前文马尔可夫例子中的状态转移矩阵，$t$时刻状态$S_i$时，$t+1$时刻状态$S_j$的概率

**观测状态转移概率矩阵$B$**: 表示t时刻，状态为$S_i$时，观测状态为$O_i$的概率

#### **03隐马尔可夫模型三大问题**

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-dd63080828f9e2240833231e2ae19c94_720w.jpg)

##### **1.评估问题（概率计算问题）**

给定模型$\lambda=\left( \mathnormal{A},\mathnormal{B},\mathnormal{\pi}\right)$和观测序列$\mathnormal{O}=\left( \mathnormal{o_1},\mathnormal{o_2},...,\mathnormal{o_T}\right)$,计算模型$\lambda$下的观测序列$\mathnormal{O}$

出现的概率$P(O|\lambda)$

对应解决该问题的算法为**向前-向后算法**

##### **2.解码问题（预测问题）**

已知模型$\lambda=\left( \mathnormal{A},\mathnormal{B},\mathnormal{\pi}\right)$和观测序列$\mathnormal{O}=\left( \mathnormal{o_1},\mathnormal{o_2},...,\mathnormal{o_T}\right)$，求给定观测序列条件概率

$P(I|O)$最大的状态序列$\mathnormal{I}=\left( \mathnormal{i_1},\mathnormal{i_2},...,\mathnormal{i_T}\right)$。即给定观测序列，求最优可能对应的状态序列。

对应的解决该问题的算法为**维比特算法**

维特比算法(Viterbi Algorithm)是一种**动态规划算法**。维特比算法由安德鲁·维特比(Andrew Viterbi)于1967年提出，用于在数字通信链路中解卷积以消除噪音。此算法被广泛应用于CDMA和GSM数字蜂窝网络、拨号调制解调器、卫星、深空通信和802.11无线网络中解卷积码。现今也被常常用于语音识别、关键字识别、计算语言学和生物信息学中。例如在语音(语音识别)中，声音信号作为观察到的事件序列，而文本字符串，被看作是隐含的产生声音信号的原因，因此可对声音信号应用维特比算法寻找最有可能的文本字符串。

###### **维特比算法求解HMM问题**

用维特比算法针对HMM三大问题中的解码问题(**给定模型和观测序列，如何找到与此观测序列最匹配的状态序列的问题**)进行求解。**问题描述如下：**首先，我们已经知道状态序列X会产生观测序列O:

![preview](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-9a680a78d81101bd2b535d99d83b0819_r.jpg)

<center style="font-size:14px;color:#C0C0C0">HMM隐藏状态序列和可观测序列</center>

但是状态序列X产生的观测序列有很多种可能，每种都有概率，如下所示，比如wo可能有三种可能：喔、我、沃

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-8aef69867024b3abb1f24ece0ad35209_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">转换概率</center>

那么其实问题就变成了最大概率问题，把概率最大的理解为路径最短，转换为了求解**最短路径问题**：(为了方便标记，标记为了下图中的字母)

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-1286e4f9b6181a88e65808ed9cdea639_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">标记为字母</center>

**算法解决：**接下来就用到了维特比算法求解最短路径，实质上是一种动态规划，把大问题化解为小问题：

**步骤1、A->B：**

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-758504e1936f85434481f2d74565e8c3_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">图解A到B</center>

**步骤2、A->B->C：**

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-5332657ff5555d2fa4d2e961da1f6455_b.webp)

<center style="font-size:14px;color:#C0C0C0">动图A到C</center>

**步骤3、A->B->C->D：**

**(可以确定A->C的3条最短路径)**

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-eff133e93f82e8931ab77d40204ec18f_b.webp)

<center style="font-size:14px;color:#C0C0C0">动图A到D</center>

**步骤4、A->B->C->D->E：**

**(可以确定A->D的2条最短路径)**

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-60b7558abec3cefc0eb39086f5797315_b.webp)

<center style="font-size:14px;color:#C0C0C0">动图A到E</center>

最终就得到了A->E的最短路径A->B1->C2->D2->E1，至此，找到了wo ai zhong guo对应的概率最大的中文汉字组合为：我爱中国。

![img](image/Paper - A Discriminative Approach to Grounded Spoken Language Understanding in Interactive Robotics 笔记.assets/v2-3cecd57f23af68b62c68f1512b78b99c_720w.jpg)

<center style="font-size:14px;color:#C0C0C0">结果</center>

##### **3.学习问题**

已知观测序列$\mathnormal{O}=\left( \mathnormal{o_1},\mathnormal{o_2},...,\mathnormal{o_T}\right)$，估计模型$\lambda=\left( \mathnormal{A},\mathnormal{B},\mathnormal{\pi}\right)$参数，使得在该模型下观测序列概率$P(O|\lambda)$最大，即极大似然估计。

对应解决该问题的算法为**鲍姆-韦尔奇算法（Baum-Welch）**

