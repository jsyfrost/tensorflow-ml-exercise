# 基于TensorFlow 的支持向量机

逻辑回归算法和大部分支持向量机算法都是二值预测。逻辑回归算法试图找到回归值 线来最大化距离（概率）；而支持向量机算法也试图最小化误差，最大化两类之间的间隔。一般来说，如果一个问题的训练集中有大量特征，则建议用逻辑回归或者线性支持向量机算法；如果训练集的数量更大，或者数据集是非线性可分的，则建议使用带高斯核的支持向量机算法。

# 4.1 支持向量机简介

支持向量机算法是一种二值分类器方法。基本的观点是，找到两类之间的一个线性可分的直线（或者超平面）。首先假设二分类目标是-1或者1，代替前面章节中的0或者1目标值。有许多条直线可以分割两类目标，但是我们定义分割两类目标有最大距离的直线为最佳线性分类器。
得到一个超平面，公式如下：

```textmate
A*x - b =0
```

其中，A是斜率向量，x是输入向量。最大间隔的宽度为2除以A的L2范数。对于这个公式有很多种证明方式，但是，从几何观点来看，这是二维数据点到一条直线的垂直距离。
对于线性可分的二值分类数据集，为了最大化间隔，我们最小化A的L2范数||A||。最小化也必须服从以下约束：

```textmate
y_i(A*x_i - b) >= 1*∀i
```

上述的约束确保所有相关分类的数据点都在分割线的同一侧。
因为不是所有的数据集都是线性可分的，我们引入跨分割线的数据点的损失函数。对于n个数据点，引入soft margin损失函数，公式如下

```textmate
1/n * sum(i:=1->n, max(0, 1-y_i*(A*x_i-b))+a*||A||**2)
```

```textmate
注意，如果数据点分割正确，乘积y_i*(A*x_i - b)总是大于1。这意味着损失函数左边项等于0，这时对损失函数有影响的仅仅只有间隔大小。
上述损失函数寻求一个线性可分的直线，但是也允许有些点跨越间隔直线，这取决于a值，当a值很大，模型会倾向于尽量将样本分割开；a值越小，会有更多的跨越边界的点存在。
建立一个soft margin支持向量机，展示如何将其扩展应用到非线性的场景和多分类目标。
```

## [4.2 线性支持向量机的使用](./demo_4.2.py)

1. 导入必要的编程库，包括导入scikit learn的datasets库来访问iris数据集
2. 创建一个计算图会话，加载需要的数据集。注意，加载iris数据集的第一列和第四列特征变量，其为花萼长度和花萼宽度。加载目标变量时，山鸢尾花为1，否则为-1
3. 分割数据集为训练集和测试集
4. 设置批量大小、占位符和模型变量
5. 声明模型输出
6. 声明最大间隔损失函数
7. 声明预测函数和准确函数，用来评估训练集和测试训练的准确度
8. 声明优化器函数，并初始化模型变量
9. 开始遍历迭代训练模型，记录训练集和测试集的损失和准确度
10. 训练过程中前面脚本的输出结果如下
11. 为了绘制输出结果，需要抽取系数，分割x_vals为山鸢尾花(I. setosa)和非山鸢尾花(non-I. setosa)
12. 下面是缎绘制数据的线性分类器、准度度和损失图

## 4.3 [弱化为线性回归](./demo_4.3.py)

1. 导入必要的编程库，创建一个计算图会话，加载iris数据集。然后分割数据集为训练集和测试集，并且可视化相应的损失函数
2. 声明批量大小、占位符和变量，创建线性模型
3. 声明损失函数。该损失函数如前所述，实现时ε = 0.5。注意，ε 是损失函数的一部分，其允许soft margin代替为hard margin
4. 创建一个优化器，初始化变量
5. 现在开始200次迭代训练，保存训练集和测试集损失函数，后续用来绘图
6. 下面是迭代训练输出结果
7. 现在抽取系数，获取最佳拟合直线的截距。为了后续画图，这里也获取间隔宽度值
8. 最后，绘制数据点和拟合直线，以及训练集和测试集损失

## 4.4 [TensorFlow上核函数的使用](./demo_4.4.py)

1. 导入必要编程库，创建一个计算图会话
2. 生成模拟数。生成的数据是一两个同心圆数据，每个不同的环代表不同的类，确保只有类-1或者1。为了让绘图方便，这里将每类数据分成x值和y值
3. 声明批量大小、占位符，创建模型变量b。对于SVM算法，为了让每次迭代训练不波动，得到一个稳定的训练模型，这时批量大小得取更大。注意，本例为预测数据点声明有额外的占位符。最后创建彩色的网格来可视化不同的区域代表不同的类别
4. 创建高斯核函数。该核函数用矩阵操作来表示
5. 声明对偶问题。为了最大化，这里采用最小化损失函数的负数: tf.negative()
6. 创建预测函数和准确度函数。先创建一个预测核函数，但用预测数据点的核函数用模拟数据点的核函数。预测值是模型输出的符号函数值
7. 创建优化器函数，初始化所有的变量
8. 开始迭代训练
9. 输出结果如下
10. 为了能够在整个数据空间可视化分类返回结果，我们将创建预测数据点的网格，在其上进行预测
11. 下面绘制预测结果、批量准确度和损失函数
12. 简单扼要，这里只显示训练结果图，不过也可以分开运行绘图代码展示其他效果

## 4.5 [用TensorFlow 实绩非线性支持向量机](./demo_4.5.py)

1. 导入必要的编程库，包括scikit learn的datasets模型。scikit learn的datasets模块可以加载iris数据集。然后建立一个计算图会话
2. 加载iris数据集，抽取花萼长度和花萼宽度，分割每类的x_vals值和y_vals值
3. 声明批量大小（偏向于更大批量大小）、占位符和模型变量b
4. 声明高斯核函数。该核函数依赖gamma值，下面将展示不同的gamma值对分类器的影响
5. 为了使用SVM进行预测，创建一个预测核函数。然后声明一个准确度函数，其为正确分类的数据点的百分比
6. 声明优化器函数，初始化变量
7. 现在开始训练
8. 为了绘制决策边界(Decision Boundary)，我们创建一个数据点(x,y)的网格，评估预测函数
9. 为了简明扼要，这里仅仅显示如何绘制决策边界

## [4.6 用TensorFlow 实现多类支持向量机](./demo_4.6.py)

1. 导入必要的编程库，创建一个计算图
2. 加载iris数据集并为每类分离目标值
3. 与之前不同的是，数据集的维度在变化，从单类目标分类到三类目标分类。我们将利用矩阵传播和reshape技术一次性计算所有的三类SVM。注意，由于一次性计算所有分类，y_target占位符的维度是[3, None], 模型变量b初始化大小为[3,batch_size]
4. 计算高斯核函数
5. 最大的变化是矩阵乘法。最终的结果是三维矩阵，并且需要传播矩阵乘法。所以数据矩阵和目标矩阵需要预处理，比如x.T*x操作需额外增加一个维度。这里创建一个函数来扩展矩阵维度，然后进行矩阵转置，接着调用TensorFlow 的tf.batch_matmul()函数
6. 计算对偶损失函数
7. 现在创建预测核函数
8. 实现预测核函数后，我们创建预测函数
9. 准备好核函数、损失函数和预测函数后，该声明优化器函数和初始化变量了
10. 该算法收敛得相当快，所以迭代训练的次数不要超过100次
11. 创建数据点的预测网格，运行预测函数
12. 绘制训练结果、批量准确度和损失函数