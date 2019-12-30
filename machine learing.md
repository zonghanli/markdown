## FP-Grow树
### 介绍
FP-growth(Frequent Pattern Tree, 频繁模式树),是韩家炜老师提出的挖掘频繁项集的方法，是将数据集存储在一个特定的称作FP树的结构之后发现频繁项集或频繁项对，即常在一块出现的元素项的集合FP树。
FP-growth算法比Apriori算法效率更高，在整个算法执行过程中，只需遍历数据集2次，就能够完成频繁模式发现，其发现频繁项集的基本过程如下：
（1）构建FP树
（2）从FP树中挖掘频繁项集

 FP-growth的一般流程如下：
1：先扫描一遍数据集，得到频繁项为1的项目集，定义最小支持度（项目出现最少次数），删除那些小于最小支持度的项目，然后将原始数据集中的条目按项目集中降序进行排列。
2：第二次扫描，创建项头表（从上往下降序），以及FP树。
3：对于每个项目（可以按照从下往上的顺序）找到其条件模式基（CPB，conditional patten base）,递归调用树结构，删除小于最小支持度的项。如果最终呈现单一路径的树结构，则直接列举所有组合；非单一路径的则继续调用树结构，直到形成单一路径即可。
### 内容
#### 构建FP树

\begin{table}[H]
\begin{tabular}{|l|l|}
\hline
Tid & Items          \\ \hline
1   & I1, I2, I5     \\ \hline
2   & I2, I4         \\ \hline
3   & I2, I3         \\ \hline
4   & I1, I2, I4     \\ \hline
5   & I1, I3         \\ \hline
6   & I2, I3         \\ \hline
7   & I1, I3         \\ \hline
8   & I1, I2, I3, I5 \\ \hline
9   & I1, I2, I3     \\ \hline
\end{tabular}
\end{table}
\noindent 扫描数据集，对每个物品进行计数：
\begin{table}[H]
\begin{tabular}{@{}|l|l|@{}}
\toprule
项集 & 支持度 \\ \midrule
I2 & 7   \\ \midrule
I1 & 6   \\ \midrule
I3 & 6   \\ \midrule
I4 & 2   \\ \midrule
I5 & 2   \\ \bottomrule
\end{tabular}
\end{table}
\noindent 设最小支持度计数为2
扫描数据库，统计支持度计数，得到频繁1-项集，按支持度降序排列
\\将其重新排列
\begin{table}[H]
\begin{tabular}{|l|l|}
\hline
Tid & Items       \\ \hline
1   & I2,I1,I5    \\ \hline
2   & I2, I4      \\ \hline
3   & I2, I3      \\ \hline
4   & I2,I1,I4    \\ \hline
5   & I1, I3      \\ \hline
6   & I2, I3      \\ \hline
7   & I1, I3      \\ \hline
8   & I2,I1,I3,I5 \\ \hline
9   & I2,I1, I3   \\ \hline
\end{tabular}
\end{table}$$
\begin{figure}[H]               
\centering
\includegraphics[width=4 in]{1.png}
\caption{构造FP树}
\label{fig_sim}
\end{figure}
\subsection{挖掘FP树}
得到了FP树和项头表以及节点链表，我们首先要从项头表的底部项依次向上挖掘。对于项头表对应于FP树的每一项，我们要找到它的条件模式基。所谓条件模式基是以我们要挖掘的节点作为叶子节点所对应的FP子树。得到这个FP子树，我们将子树中每个节点的的计数设置为叶子节点的计数，并删除计数低于支持度的节点。从这个条件模式基，我们就可以递归挖掘得到频繁项集了。 \\
从I5开始，对于头表中的每个$I_{i}$，确定自身为频繁模式，再挖掘以$I_{i}$为后缀的频繁模式 \\
将所有的祖先节点计数设置为叶子节点的计数
前缀路径/条件模式基：\\
<I2,I1:1>、<I2,I1,I3:1> \\
频繁模式：
$\{I2,I5:2\}$  ({I2,I5:2}2的含义指的是I2，I5都是2)\\
$\{I1,I5:2\}$\\
$\{I2,I1,I5:2\}$\\
提取以I4的前缀路径/条件模式基：
<I2,I1:1>、<I2:1>(I1被砍掉了)
I4为后缀的频繁模式:$\{I2,I4:2\}$ \\
提取以I3为后缀的频繁模式
有两分支，则：
对于条件FP树头表中的每个Ii，与I3连接确定频繁模式{Ii, I3}，支持度等于Ii的支持度。
递归挖掘条件FP树，提取以{Ii, I3}为后缀的频繁模式
频繁模式：
$\{I1,I3:4\}$
$\{I2,I3:4\}$
$\{I2,I1,I3:2\}$
\\ 以I1为后缀
$\{I2 I1:4\}$
\\ I2只有前缀，没有后缀
\begin{table}[H]
\begin{tabular}{|c|c|l|l|}
\hline
项 & 条件模式基                       &  条件FP树                                                          & 产生的频繁模式                              \\ \hline
I5    & \{I2 I1:1\}, \{I2 I1 I3:1\}     & \textless{}I2:2,I1:2\textgreater{}                                 & \{I2 I5:2\}, \{I1 I5:2\}, \{I2 I1 I5:2\} \\ \hline
I4    & \{I2 I1:1\}, \{I2:1\}           & \textless{}I2:2\textgreater{}                                      & \{I2 I4:2\}                              \\ \hline
I3    & \{I2 I1:2\}, \{I2:2\}, \{I1:2\} & \textless{}I2:4, I1:2\textgreater{}, \textless{}I1:2\textgreater{} & \{I2 I3:4\}, \{I1 I3:4\}, \{I2 I1 I3:2\} \\ \hline
I1    & \{I2:4\}                        & \textless{}I2:4\textgreater{}                                      & \{I2 I1:4\}                              \\ \hline
\end{tabular}
\end{table}
\chapter{朴素贝叶斯}
\section{算法}
\noindent 假设有n个类别C1,C2...Cn，给定一个实例的特征向量w，则此实例属于类Ci的概率为
$$P\left(C_{i} | w\right)=\frac{P\left(w | C_{i}\right) P\left(C_{i}\right)}{P(w)}$$
P(Ci)的计算：
将训练样本中属于类Ci的实例数量除以训练样本数量即P(Ci)，
例如动物图片识别中，假设有100个训练实例，其中有15张为猫，则
P(猫) = 15 / 100 = 0.15
\\ P(w)的计算：
因为利用贝叶斯进行分类时，我们只要比较概率的大小即可，
而P(w)对于所有的类别都是一样的，因此无须计算
\\P(w|Ci)的计算：
$$
\mathrm{P}\left(\mathrm{w}_{0}, \mathrm{w}_{1}, \mathrm{w}_{2} \cdot \cdots \mathrm{w}_{\mathrm{n}} | \mathrm{C}_{\mathrm{i}}\right)
$$
朴素贝叶斯假设实例的各个属性互相独立，互不影响，因此，上式等价于:$\mathrm{P}\left(\mathrm{w}_{0} | \mathrm{C}_{\mathrm{i}}\right) \mathrm{P}\left(\mathrm{w}_{1} | \mathrm{C}_{\mathrm{i}}\right) \mathrm{P}\left(\mathrm{w}_{2} | \mathrm{C}_{\mathrm{i}}\right) \ldots \mathrm{P}\left(\mathrm{w}_{\mathrm{n}} | \mathrm{C}_{\mathrm{i}}\right)$
\begin{example}
假设一个实例的特征向量为 (有四条腿, 会飞)，即$w_0$=有四条腿，$w_1$为会飞，共有三个类别分别是鸟、狗、鱼，\\ 则
$P(w_0|C_0)$=P(有四条腿|鸟) = 训练样本中有四条腿的鸟(实例)的数量除以样本中鸟(实例)的数量
\\ $P(w_1|C_0)$=P(会飞|鸟) = 训练样本中会飞的鸟(实例)的数量除以样本中鸟(实例)的数量
$$P(w_0, w_1 |C_0)= P(w_0|C_0) \times P(w1|C0)$$
 P(有四条腿,会飞|鸟)= P(有四条腿|鸟)* P(会飞|鸟) 

\end{example}

共14个训练实例。
共两个类别，“会买电脑”和不会买电脑。
每个训练实例有4个属性
计算P(Ci)
本例中C0为未买电脑，C1为买了电脑

试由表的训练数据学习一个朴素贝叶斯分类器并确定$x = (2,S)^T$的类标记y
表中X(1)，X(2)为特征，取值的集合分别为A1＝{1,2,3}，A2＝{S，M,L}，Y为类标记，Y$\in C$
＝{1,-1}

\begin{table}[H]
\begin{tabular}{|l|l|l|l|l|l|l|l|l|l|l|l|l|l|l|l|}
\hline
                       & 1  & 2  & 3 & 4 & 5  & 6  & 7  & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 \\ \hline
X(1) & 1  & 1  & 1 & 1 & 1  & 2  & 2  & 2 & 2 & 2  & 3  & 3  & 3  & 3  & 3  \\ \hline
X(2) & S  & M  & M & S & S  & S  & M  & M & L & L  & L  & M  & M  & L  & L  \\ \hline
Y                      & -1 & -1 & 1 & 1 & -1 & -1 & -1 & 1 & 1 & 1  & 1  & 1  & 1  & 1  & -1 \\ \hline
\end{tabular}
\end{table}
$$P(Y=1)=\frac{9}{15}, \quad P(Y=-1)=\frac{6}{15}$$
$$P\left(X^{(1)}=1 | Y=1\right)=\frac{2}{9}, \quad P\left(X^{(1)}=2 | Y=1\right)=\frac{3}{9}, \quad P\left(X^{(1)}=3 | Y=1\right)=\frac{4}{9}$$
$$
P\left(X^{(2)}=S | Y=1\right)=\frac{1}{9}, \quad P\left(X^{(2)}=M | Y=1\right)=\frac{4}{9}, \quad P\left(X^{(2)}=L | Y=1\right)=\frac{4}{9}
$$
$$P\left(X^{(1)}=1 | Y=-1\right)=\frac{3}{6}, \quad P\left(X^{(1)}=2 | Y=-1\right)=\frac{2}{6}, \quad P\left(X^{(1)}=3 | Y=-1\right)=\frac{1}{6}$$
$$P\left(X^{(2)}=S | Y=-1\right)=\frac{3}{6}, \quad P\left(X^{(2)}=M | Y=-1\right)=\frac{2}{6}, \quad P\left(X^{(2)}=L | Y=-1\right)=\frac{1}{6}$$
$P(Y=1) P\left(X^{(1)}=2 | Y=1\right) P\left(X^{(2)}=S | Y=1\right)=\frac{9}{15} \cdot \frac{3}{9} \cdot \frac{1}{9}=\frac{1}{45}$ \\
$P(Y=-1) P\left(X^{(1)}=2 | Y=-1\right) P\left(X^{(2)}=S | Y=-1\right)=\frac{6}{15} \cdot \frac{2}{6} \cdot \frac{3}{6}=\frac{1}{15}$
$P(Y=-1) P\left(X^{(1)}=2 | Y=-1\right) P\left(X^{(2)}=S | Y=-1\right)$最大
\chapter{决策树}
\begin{figure}[H]               
\centering
\includegraphics[width=4 in]{2.png}
\caption{决策树}
\label{fig_sim}
\end{figure}
设数据集D中有m个不同的类C1, C2, C3, ..., Cm
设 Ci,D是数据集D中Ci类的样本的集合, |D|和 |Ci,D|分别是D和 Ci,D中的样本个数
数据集D的信息熵:
$$
\operatorname{Info}(D)=-\sum_{i=1}^{m} p_{i} \log _{2} p_{i}
$$
其中pi是数据集D中任意样本属于类Ci的概率
\begin{table}[H]
\begin{tabular}{|l|l|l|l|l|}
\hline
年龄               & 收入 & 学生 & 信用 & 买了电脑 \\ \hline
\textless{}30    & 高  & 否  & 一般 & 否    \\ \hline
\textless{}30    & 高  & 否  & 好  & 否    \\ \hline
30-40            & 高  & 否  & 一般 & 是    \\ \hline
\textgreater{}40 & 中等 & 否  & 一般 & 是    \\ \hline
\textgreater{}40 & 低  & 是  & 一般 & 是    \\ \hline
\textgreater{}40 & 低  & 是  & 好  & 否    \\ \hline
30-40            & 低  & 是  & 好  & 是    \\ \hline
\textless{}30    & 中  & 否  & 一般 & 否    \\ \hline
\textless{}30    & 低  & 是  & 一般 & 是    \\ \hline
\textgreater{}40 & 中  & 是  & 一般 & 是    \\ \hline
\textless{}30    & 中  & 是  & 好  & 是    \\ \hline
30-40            & 中  & 否  & 好  & 是    \\ \hline
30-40            & 高  & 是  & 一般 & 是    \\ \hline
\textgreater{}40 & 中  & 否  & 好  & 否    \\ \hline
\end{tabular}
\end{table}
\noindent $|D|=14$ \\
$\left|C_{1, D}\right|=5$ \\
$\left|C_{2, D}\right|=9$ \\
$$
Info(D)=-\frac{5}{14} \log _{2} \frac{5}{14}-\frac{9}{14} \log _{2} \frac{9}{14}=0.940
$$
信息增益：
$$
\operatorname{Gain}(A)=\operatorname{Info}(D)-\operatorname{Info}_{A}(D)
$$
确定第一次分裂的属性：按年龄划分：\\
年龄<30的有5个, 其中3个为“否” \\
年龄30-40的有4个, 其中0个为“否”
\\ 年龄>40的有5个, 其中2个为“否” \\
$Info_\text{年龄}D=\frac{5}{14}\left(-\frac{3}{5} \log \frac{3}{5}-\frac{2}{5} \log \frac{2}{5}\right)
+\frac{4}{14}\left(-\frac{4}{4} \log \frac{4}{4}-\frac{0}{4} \log \frac{0}{4}\right)+\frac{5}{14}\left(-\frac{2}{5} \log \frac{2}{5}-\frac{3}{5} \log \frac{3}{5}\right)$ 
\\ Gain(年龄) 
= Info(D) - Info年龄(D)
= 0.940 - 0.694 = 0.246 \\
确定第一次分裂的属性：按收入划分：\\
收入=高的有4个, 其中2个为“否” \\
收入=中的有6个, 其中2个为“否” \\
收入=低的有4个, 其中1个为“否” \\
$Info_\text{收入}D=\frac{4}{14}\left(-\frac{2}{4} \log \frac{2}{4}-\frac{2}{4} \log \frac{2}{4}\right)+\frac{6}{14}\left(-\frac{2}{6} \log \frac{2}{6}-\frac{4}{6} \log \frac{4}{6}\right)$ \\
确定第一次分裂的属性：按信用划分：\\
信用好的有6个, 其中3个为“否” \\
信用一般的有8个, 其中2个为“否” \\
$Info_\text{信用}D=\frac{6}{14}\left(-\frac{3}{6} \log \frac{3}{6}-\frac{3}{6} \log \frac{3}{6}\right)$
\\ 确定第一次分裂的属性：按学生划分 \\
是学生的有7个, 其中1个为“否” \\
不是学生的有7个, 其中4个为“否"
\\$Info_\text{学生}D=\frac{7}{14}\left(-\frac{1}{7} \log \frac{1}{7}-\frac{6}{7} \log \frac{6}{7}\right)$\\
“年龄”属性具体最高
信息增益，成为分裂属性
\begin{figure}[H]               
\centering
\includegraphics[width=5 in]{5.png}
\caption{第二次分类}
\label{fig_sim}
\end{figure}
观察年龄<30的人群：
\begin{table}[H]
\begin{tabular}{|l|l|l|l|}
\hline
收入 & 学生 & 信用 & 买了电脑 \\ \hline
高  & 否  & 一般 & 否    \\ \hline
高  & 否  & 好  & 否    \\ \hline
中  & 否  & 一般 & 否    \\ \hline
低  & 是  & 一般 & 是    \\ \hline
中  & 是  & 好  & 是    \\ \hline
\end{tabular}
\end{table}
\begin{figure}[H]               
\centering
\includegraphics[width=5 in]{33.png}
\caption{第二次分类}
\label{fig_sim}
\end{figure}、

\chapter{BP神经网络}
BP(back propagation)神经网络是1986年由Rumelhart和McClelland为首的科学家提出的概念，是一种按照误差逆向传播算法训练的多层前馈神经网络，是应用最广泛的神经网络
\begin{figure}[H]               
\centering
\includegraphics[width=4 in]{1.pdf}
\caption{神经元}
\label{fig_sim}
\end{figure}
注意这里有三件事发生了,红色代表权重，
$$
\begin{array}{l}{x_{1} \rightarrow x_{1} * w_{1}} \\ {x_{2} \rightarrow x_{2} * w_{2}}\end{array}
$$
绿色代表偏差(bias)
$$
\left(x_{1} * w_{1}\right)+\left(x_{2} * w_{2}\right)+b
$$
黄色代表激活函数
$$
y=f\left(x_{1} * w_{1}+x_{2} * w_{2}+b\right)
$$
激活函数常用的是Sigmod函数
\begin{figure}[H]               
\centering
\includegraphics[width=4 in]{sigmoid.png}
\caption{Sigmod}
\label{fig_sim}
\end{figure}
\begin{figure}[H]               
\centering
\includegraphics[width=4 in]{network3.pdf}
\caption{network}
\label{fig_sim}
\end{figure}
$$
L\left(w_{1}, w_{2}, w_{3}, w_{4}, w_{5}, w_{6}, b_{1}, b_{2}, b_{3}\right)
$$
 \begin{algorithm}  
        \caption{神经网络基本算法}  
      $a_{1}^{(2)}=g(\Theta_{10}^{1}x_{0}+\Theta_{11}^{1}x_{1}+\Theta_{12}^{1}x_{2}+\Theta_{13}^{1}x_{3})$\\
$a_{2}^{(2)}=g(\Theta_{20}^{1}x_{0}+\Theta_{21}^{1}x_{1}+\Theta_{22}^{1}x_{2}+\Theta_{23}^{1}x_{3})$\\
$a_{3}^{(2)}=g(\Theta_{30}^{1}x_{0}+\Theta_{31}^{1}x_{1}+\Theta_{32}^{1}x_{2}+\Theta_{33}^{1}x_{3})$
            
    \end{algorithm}  
\begin{algorithm}  
        \caption{反向传播算法}  
$\begin{aligned} z_{i}^{(l+1)} &=b_{i}^{(l)}+\sum_{j=1}^{S_{l}} w_{i j}^{(l)} a_{j}^{(l)} \\ g(x) &=\frac{1}{1+e^{-x}} \\ a_{i}^{(l)} &=g\left(z_{i}^{(l)}\right) \\ J(\theta) &=\frac{1}{2} \sum_{j=1}^{S_{n_{l}}}\left(y_{j}-a_{j}^{\left(n_{l}\right)}\right)^{2} \\ \delta_{i}^{(l)} &=\frac{\partial J(\theta)}{\partial z_{i}^{(l)}} \end{aligned}$
            

    \end{algorithm}  