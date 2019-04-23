---
layout:     post                    # 使用的布局
title:      决策树           # 标题 
subtitle:   ID3、C4.5、CART决策树以及剪枝处理 
date:       2019-04-19              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-heidong.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Mechine Learning
---

## 1. 介绍

**结构：**

一个根节点：属性测试

若干内部结点：属性测试

若干叶子节点：对应于决策结果

**目的：**

产生一颗泛化能力强的树，即能正确处理从没见过的样本或物体

## 2. 决策树基本流程（伪代码）

**思想：**分而治之

> Tree(样本，属性)
>
> 生成 node
> if 样本中的类别全属于统一类别 $C$ then
> 	标记node的类别为$C$；return
> end if
> if 样本中的属性相同 or 属性集为空 then
> 	标记node的类别为样本中数量最多的类；return
> end if
> 从属性集中选取最优化分属性$a_*$；
>
> for $a_*^v$ in  $a_*$  do
>
> ​	生成一个分支node；
>
> ​	$D_v$表示$a_*^v$对应的样本子集；
>
> ​	if $D_v$为空 then
>
> ​		分支node为叶子节点，类别标记为样本中数量最多的类；return
>
> ​	else
>
> ​		递归调用Tree($D_v$，A\\{$a_*$ })为分支节点
>
> ​	end if
>
> end for

**关键问题：**如何寻找最优划分属性$a_*$

## 3. ID3算法

**信息熵：**度量样本纯度的指标

**假设：**样本集$D$，在第k类样本中所占比例为$p_k(k=1,2,…,|Y|)$


$$
Ent(D)=-\sum_{k=1}^{|Y|}p_klog_2p_k
$$


信息熵的值越小，则样本集$D$纯度越大。

```python
# 计算信息熵information entropy
def entropyCal(dfdata):
    dataSize = dfdata.shape[0]  # 数据集样本数
    colSize = dfdata.shape[1]  # 数据集属性个数（包括最后一列分类）
    typeCount = dict(dfdata.iloc[:,
                     colSize - 1].value_counts())  # 统计数据集样本各个类别及其数目
    entropy = 0.0
    for key in typeCount:
        p = float(typeCount[key]) / dataSize
        entropy = entropy - p * math.log(p, 2)  # 以2为底求对数
    return entropy
```

---

**信息增益：**度量使用某个属性进行划分的纯度提升能力

**假设：**

属性$a$有$V$个值${a^1,a^1,…,a^V}$

$D^v$表示在属性$a$上取值为$a^v$的样本


$$
Gain(D,a)=End(D)-\sum_{v=1}^{V}\frac{|D^v|}{|D|}Ent(D^v)
$$


信息增益越大，使用属性$a$进行划分所获得纯度提升大。

因此，可以使用信息增益进行属性划分，即$a_*=\mathop{\arg\max}_{a}Gain(D^v)$。

**ID3算以信息增益进行属性划分**

```python
# ID3算法：以信息增益为准则选择划分属性
def chooseBestFeatureToSplit(dfdata):
    dataSize = dfdata.shape[0]  # 数据集样本个数
    numFeature = dfdata.shape[1] - 1  # 数据集属性个数
    entropyBase = entropyCal(dfdata)  # 划分前样本集的信息熵
    infoGainMax = 0.0  # 初始化最大信息熵
    bestFeature = ''  # 初始化最佳划分属性
    for col in range(numFeature):
        featureValueCount = dict(dfdata.iloc[:,
                                 col].value_counts())  # 统计该属性下各个值及其数目
        entropyNew = 0.0
        for key, value in featureValueCount.items():
            # 计算该属性划分下各样本集的信息熵加权和
            entropyNew += entropyCal(
                splitDataset(dfdata, dfdata.columns[col],
                             key)) * float(value / dataSize)
        infoGain = entropyBase - entropyNew  # 计算该属性下的信息增益
        if infoGain > infoGainMax:
            infoGainMax = infoGain
            bestFeature = dfdata.columns[col]  # 寻找最佳划分属性
    return bestFeature
```



## 4. C4.5算法

**信息增益缺陷：**对可取值数目较多的属性有所偏好，即某个属性的值越多，这个值被选中几率越大，可能带来不利影响，例如编号属性。

**信息率：**对可取值数目较少的属性有所偏好，与信息增益相反。


$$
Gain\_ratio(D,a)=\frac{Gain(D,a)}{IV(a)},其中 IV(a)=-\sum_{v=1}^{V}\frac{|D^v|}{|D|}\log_2\frac{|D^v|}{|D|}
$$


$IV(a)$称为属性$a$的固有值，与信息增益类似，属性$a$的可能取值越多，固有值越大。

因此，固有值最为分母，信息率越大，属性a的可能取值越小。

**C4.5算法：先通过信息增益选择高于平均水平的增益，再从中选取信息率最大的。**

```python
# C4.5算法：以增益率为准则选择划分属性
def chooseGainRatioToSplit(dfdata):
    dataSize = dfdata.shape[0]  # 数据集样本个数
    numFeature = dfdata.shape[1] - 1  # 数据集属性个数
    entropyBase = entropyCal(dfdata)  # 划分前样本集的信息熵
    infoGains = list()
    # 信息增益总和
    infoGainTotal = 0.0
    infoGainRatios = list()
    bestFeature = ''  # 初始化最佳划分属性
    for col in range(numFeature):
        featureValueCount = dict(dfdata.iloc[:,
                                 col].value_counts())  # 统计该属性下各个值及其数目
        entropyNew = 0.0
        # 计算该属性固有值
        iv = 0.0
        for key, value in featureValueCount.items():
            # 计算该属性划分下各样本集的信息熵加权和
            entropyNew += entropyCal(
                splitDataset(dfdata, dfdata.columns[col],
                             key)) * float(value / dataSize)
            # 计算该属性的固有值
            iv -= float(value / dataSize) * math.log2(
                value / dataSize)
        infoGain = entropyBase - entropyNew  # 计算该属性下的信息增益
        # 计算该属性增益率
        if iv == 0 and infoGain == 0:
            gain_ratio = 0.0
        else:
            gain_ratio = infoGain / iv
        infoGains.append(infoGain)
        infoGainRatios.append(gain_ratio)
        infoGainTotal += infoGain
    # 计算信息增益平均值
    infoGainAve = infoGainTotal / numFeature
    infoGainRatioMax = 0.0
    for i, infoGain in enumerate(infoGains):
        if infoGain < infoGainAve:
            continue
        if infoGainRatios[i] > infoGainRatioMax:
            infoGainRatioMax = infoGainRatios[i]
            bestFeature = dfdata.columns[i]
    return bestFeature
```



## 5. CART算法

**基尼值：**与信息熵类似，度量样本纯度的指标。


$$
\begin{align}
Gini(D)&=\sum_{k=1}^{|Y|}\sum_{k^`\neq{k}}p_kp_{k^`}\\
&=1-\sum_{k=1}^{|Y|}p_k^2
\end{align}
$$


基尼值反映了随机抽取两个样本，类别标记不同的概率，基尼值越小，样本纯度越高。

```python
# 计算基尼值
def giniCal(dfdata):
    gini = 1.0
    dataSize = dfdata.shape[0]  # 数据集样本数
    colSize = dfdata.shape[1]  # 数据集属性个数（包括最后一列分类）
    typeCount = dict(dfdata.iloc[:,
                     colSize - 1].value_counts())  # 统计数据集样本各个类别及其数目
    for key in typeCount:
        p = float(typeCount[key]) / dataSize
        gini -= p ** 2
    return gini
```



---

**基尼指数：**指数越小，属性越优


$$
Gini\_index(D,a)=\sum_{v=1}^V\frac{|D^v|}{|D|}Gini(D^v)
$$


CART算法以基尼指数作为属性筛选指标，即$a_*={\arg\min}_{a \in A}Gini\_index(D,a)$。

```python
# CART算法：以基尼指数为准则选择划分属性
def chooseBestFeatureToSplit(dfdata):
    dataSize = dfdata.shape[0]  # 数据集样本个数
    numFeature = dfdata.shape[1] - 1  # 数据集属性个数
    infoGiniMax = 0.0  # 初始化最大基尼指数
    bestFeature = ''  # 初始化最佳划分属性
    for col in range(numFeature):
        featureValueCount = dict(dfdata.iloc[:,
                                 col].value_counts())  # 统计该属性下各个值及其数目
        giniNew = 0.0
        for key, value in featureValueCount.items():
            # 计算该属性划分下各样本集的信息熵加权和
            giniNew += giniCal(
                splitDataset(dfdata, dfdata.columns[col],
                             key)) * float(value / dataSize)
        if col == 0:
            infoGiniMax = giniNew
        if giniNew <= infoGiniMax:
            infoGiniMax = giniNew
            bestFeature = dfdata.columns[col]  # 寻找最佳划分属性
    return bestFeature
```



## 6. 剪枝处理

**剪枝主要用来解决“过拟合”问题**

#### 预剪枝

**时间点：**在决策树生成的过程中

**做法：**估计当前节点的划分能否带来性能的提升，若不能提升，则停止划分，并将当前节点作为叶子节点

**估计方法：**使用训练集判断划分前后的精度

**优点：**降低过拟合的风险，减少训练时间和测试时间

**缺点：**基于贪心策略，禁止当前不能提高泛化性能的分支展开（在后续划分中可能提高泛化性能），有欠拟合的风险

#### 后剪枝

**时间点：**在决策树生成之后

**做法：**自底向上对非叶子节点进行考察，若将该节点子树划分为叶子节点能提高泛化性能，则将子树作为叶子节点

**估计方法：**使用训练集判断划分前后的精度

**优点：**欠拟合风险小，泛化性能由于预剪枝

**缺点：**训练时间长（每次判断是否剪枝都要使用验证集测试）



## 7. 连续值处理

**问题：**连续属性的取值个数不再有限，直接根据连续值进行划分将不再有意义

**方案：**连续属性离散化

**策略之一：**二分法，即将一个连续属性的值划分为两个，正（+）和负（-）

二分法需要选取一个划分点，即判别属性值被分成正还是负，所有可能划分点的集合为：


$$
T_a=\lbrace\frac{a^i+a^{i+1}}{2}|1\leq i \leq n-1\rbrace
$$


即将集合$[a^i,a^{i+1})$的中点作为划分点，则信息增益的计算公式为：


$$
\begin{align}
Gain(D,a)&=\max_{t\in T_a}Gain(D,a,t)\\
&=\max_{t\in T_a}Ent(D)-\sum_{\lambda\in\{-,+\}}\frac{|D^\lambda_t|}{|D|}Ent(D^\lambda_t)
\end{align}
$$


$Gain(D,a,t)$是根据$t$划分后的信息增益，我们选取最大的信息增益所代表的$a，t$。

**PS：**与离散属性不同，连续属性可作为后代节点的划分属性（即子节点仍可使用此连续属性值进行划分）



## 8. 缺失值处理

**问题：**当属性数目较多时，往往存在大量的样本存在缺失值（即某些样本在某些属性下的值为$-$）

如果仅处理无缺失值样本，会浪费掉大量的数据，因此，如何处理缺失值是很有必要的。

**处理缺失值需要考虑的问题：**

1. 当属性值缺失时，如何进行属性划分（即信息增益、信息率、基尼指数如何求解）？
2. 问题1解决后，在进行划分时，缺失值的样本如何划分？

---

##### 问题1解决方案

**假设：**

训练集$D$；属性$a$；

无缺失属性集$\widetilde D$；$\widetilde D$中属性$a$取值为$v$的样本子集$\widetilde D^v$;

$\widetilde D$中第K类样本子集$\widetilde D_k$

定义无缺失值样本所占比例$\rho$：$\rho=\frac{\sum_{x\in\widetilde D}\cal w_x}{\sum_{x\in D}\cal w_x}$

无缺失值样本中第$k$类所占的比例$\widetilde\rho_k$：$\widetilde\rho_k=\frac{\sum_{x\in\widetilde D_k}\cal w_x}{\sum_{x\in \widetilde D}\cal w_x}$

无缺失值样本中属性$a$上取值$a^v$的样本所占比例$\widetilde r_v$：$\widetilde r_k=\frac{\sum_{x\in\widetilde D^v}\cal w_x}{\sum_{x\in \widetilde D}\cal w_x}$

缺失值上的信息增益为：


$$
\begin{align}
Gain(D,a)&=\rho\times Gain(\widetilde D,a)\\
&=\rho \times (Ent(\widetilde D)-\sum_{v=1}^{V}\widetilde r_vEnt(\widetilde D^v))
\end{align}
$$


信息熵为：


$$
Ent(\widetilde D)=-\sum_{k=1}^{|Y|}\widetilde p_klog_2\widetilde p_k
$$


##### 问题2解决方案

在选取属性以后，对于无缺失的样本，按照正常的方案归入到子节点中；对于属性缺失的方案，则将样本归到所有的子节点中，但是样本的权值变为调整为子节点的$\widetilde r_v*w_x$。（无缺失样本权值不变）



## 9. 多变量决策树

以上所介绍的决策树有一个**特性**：轴平行，即分类边界由若干个与坐标轴平行的分段组成。

![](https://ws2.sinaimg.cn/large/006tNc79ly1g2aqaqojqaj30j60e4mxz.jpg)

**问题：**分类边界复杂时，决策树非常复杂，预测时间成本高。

![](https://ws4.sinaimg.cn/large/006tNc79ly1g2aqbkx20sj30j60dwdhh.jpg)

**方案：**多变量决策树，使用斜的划分边界，简化决策树模型。

飞叶节点不是对某个属性的判断，而是对属性的线性组合进行判断。

每个非叶节点的目的不是寻找最好的划分属性，而是试图建立一个合适的线性分类器。



## 10. 代码实现（CART改进版，取自sklearn库）

*导入需要用到的python库*

```python
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
```

*导入数据集*

```python
dataset = pd.read_csv('datasets/Social_Network_Ads.csv')
X = dataset.iloc[:, [2, 3]].values
y = dataset.iloc[:, 4].values
print(dataset)
```

```
      User ID  Gender  Age  EstimatedSalary  Purchased
0    15624510    Male   19            19000          0
1    15810944    Male   35            20000          0
2    15668575  Female   26            43000          0
3    15603246  Female   27            57000          0
4    15804002    Male   19            76000          0
5    15728773    Male   27            58000          0
6    15598044  Female   27            84000          0
7    15694829  Female   32           150000          1
8    15600575    Male   25            33000          0
9    15727311  Female   35            65000          0
10   15570769  Female   26            80000          0
11   15606274  Female   26            52000          0
12   15746139    Male   20            86000          0
13   15704987    Male   32            18000          0
..        ...     ...  ...              ...        ...
388  15672330    Male   47            34000          1
389  15668521  Female   48            35000          1
390  15807837    Male   48            33000          1
391  15592570    Male   47            23000          1
392  15748589  Female   45            45000          1
393  15635893    Male   60            42000          1
394  15757632  Female   39            59000          0
395  15691863  Female   46            41000          1
396  15706071    Male   51            23000          1
397  15654296  Female   50            20000          1
398  15755018    Male   36            33000          0
399  15594041  Female   49            36000          1

[400 rows x 5 columns]
```

*将数据集拆分为训练集和测试集*

```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.25, random_state = 0)
```

*特征缩放*

```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
X_train = sc.fit_transform(X_train)
X_test = sc.transform(X_test)
```

*对训练集进行决策树分类拟合*

```python
from sklearn.tree import DecisionTreeClassifier
classifier = DecisionTreeClassifier(criterion = 'entropy', random_state = 0)
classifier.fit(X_train, y_train)
```

```
DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=None,
            max_features=None, max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, presort=False, random_state=0,
            splitter='best')
```

*预测测试集的结果*

```python
y_pred = classifier.predict(X_test)
print('y_pred:'+str(y_pred))
```

```
y_pred:[0 0 0 0 0 0 0 1 0 0 0 0 0 1 0 1 1 0 1 0 0 1 0 1 0 0 0 0 0 0 0 0 1 0 0 0 0
 0 0 1 0 0 0 0 1 0 0 1 0 1 1 0 0 1 1 1 0 0 1 0 0 1 0 1 0 1 0 0 0 1 1 0 0 1
 0 0 0 0 1 1 1 1 0 0 1 0 0 1 1 0 0 1 0 0 0 1 0 1 1 1]
```

*制作混淆矩阵*

```python
from sklearn.metrics import confusion_matrix
cm = confusion_matrix(y_test, y_pred)
print(cm)
```

```
[[62  6]
 [ 3 29]]
```

*将训练集结果进行可视化*

```python
from matplotlib.colors import ListedColormap
X_set, y_set = X_train, y_train
X1, X2 = np.meshgrid(np.arange(start = X_set[:, 0].min() - 1, stop = X_set[:, 0].max() + 1, step = 0.01),
                     np.arange(start = X_set[:, 1].min() - 1, stop = X_set[:, 1].max() + 1, step = 0.01))
plt.contourf(X1, X2, classifier.predict(np.array([X1.ravel(), X2.ravel()]).T).reshape(X1.shape),
             alpha = 0.75, cmap = ListedColormap(('red', 'green')))
plt.xlim(X1.min(), X1.max())
plt.ylim(X2.min(), X2.max())
for i, j in enumerate(np.unique(y_set)):
    plt.scatter(X_set[y_set == j, 0], X_set[y_set == j, 1],
                c = ListedColormap(('red', 'green'))(i), label = j)
plt.title('Decision Tree Classification (Training set)')
plt.xlabel('Age')
plt.ylabel('Estimated Salary')
plt.legend()
plt.show()
```

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2buzm3y33j30as07qgls.jpg)

*将测试集结果进行可视化*

```python
from matplotlib.colors import ListedColormap
X_set, y_set = X_test, y_test
X1, X2 = np.meshgrid(np.arange(start = X_set[:, 0].min() - 1, stop = X_set[:, 0].max() + 1, step = 0.01),
                     np.arange(start = X_set[:, 1].min() - 1, stop = X_set[:, 1].max() + 1, step = 0.01))
plt.contourf(X1, X2, classifier.predict(np.array([X1.ravel(), X2.ravel()]).T).reshape(X1.shape),
             alpha = 0.75, cmap = ListedColormap(('red', 'green')))
plt.xlim(X1.min(), X1.max())
plt.ylim(X2.min(), X2.max())
for i, j in enumerate(np.unique(y_set)):
    plt.scatter(X_set[y_set == j, 0], X_set[y_set == j, 1],
                c = ListedColormap(('red', 'green'))(i), label = j)
plt.title('Decision Tree Classification (Test set)')
plt.xlabel('Age')
plt.ylabel('Estimated Salary')
plt.legend()
plt.show()
```

![](https://ws2.sinaimg.cn/large/006tNc79ly1g2bv0fcffyj30as07q3yk.jpg)

> 参考：西瓜书