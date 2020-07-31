## Graph

graph 不是 image

图是数据结构和算法学里最强大的框架之一，可以用来表示 所有类型的结构或系统。

我们可以把图视为一种由“顶点”组成的抽象网络，各顶点 之间通过“边”实现彼此的连接，表示两个顶点之间有关联。

Computation Graph 是由一系列边（data）和计算节点 （Operation）组成的有向数据流图。 

边代表数据的流向，节点代表对数据的操作和计算。

TensorFlow是基于Computation Graph的框架，在TensorFlow中， 所有的运算被定义为数据流图（data flow graph） 

TensorFlow里面使用 tf.Graph 来表示可计算的图。图由操作 tf.Operation 和张量 Tensor 来构成

Graph 常规操作：

1. 默认图里创建操作/函数
2. 在不同的图里创建操作/函数

```python
import tensorflow as tf
print(tf.get_default_graph())
c = tf.constant(1)
print(c.graph)
// c会在默认图中
g1 = tf.Graph()
with g1.as_default():
  d = tf.constant(2)
  print(d.graph) // d就被分配到了图g1中了
```

## Session

TensorFlow要实现一个应用程序，一共包括两个步骤：

1. 建立计算图
2. 执行计算图

tf.Session 在TensorFlow里用来执行操作,为Operation和Tensor提 供运行的环境和资源。 

Tensor作为一种数据结构，只有在Session中运行的时候才会被赋值。

Tensorflow中有两种Session：tf.Session()和tf.InteractiveSession() 

tf.InteractiveSession():是一种可交互式的Session，能够在运行图的过程 中插入一些图，这些图是由一些操作构成的。对于某些使用IPython这种交互式 环境的人来说是非常便利的。 

tf.Session()需要在启动Session之前构建整个计算图，然后再执行计算图。

我们在使用tf.InteractiveSession()时可以先构建一个Session，再往其中添加操作。 

如果我们使用tf.Session()就需要我们将整个计算图中所有的操作都预先 定义好再来构建会话。

```python
import tensorflow as tf
a = tf.constant(1)
sess = tf.Session()
b = sess.run(a) // 1
print(b)
sess.close()

sess2 = tf.InteractiveSession()
c = sess2.run(a) // 1
print(c)
sess2.close() // 短时间如果不会去调用它就应该把它关闭

with tf.Session() as sess3:
  print(sess3.run(a)) // 1
  
sess3.run(a) // 在上下文管理器之外调用这个 sess3 就会报错，这个session已经关闭了
```



## Tensor

源自物理学

简单来说，计算机体系里用于表示多维数组

常见数据类型： 

- tf.float32/64 浮点型 
- tf.int8/16/32/64 有符号整型 
- tf.unit8 无符号整型 
- tf.string 字节数组
- tf.bool 布尔型

常见 Tensor：

1. Constant（常量） 
2. Varieble（变量） 
3. Placeholder（占位符）

```python
import tensorflow as tf
a = tf.constant(1) // 常量直接使用就可以了
print(a)

var1 = tf.Variable(tf.ones([3,3])) // 变量使用之前要先进行初始化

with tf.Session() as sess:
  sess.run(tf.global_variables_initializer())
  print(sess.run(var1))

  
p1 = tf.placeholder(tf.float32)
p2 = tf.placeholder(tf.float32)
result = tf.multiply(p1, p2) // 现在就建立好了一个乘法运算了
print(p1, p2, result)

with tf.Session() as sess:
  c = sess.run(result, feed_dict = {p1:[13.5, 2.9],p2:[1.1, 2.2]})
  print(c) // [14.85 6.38]
```

## TensorFlow 常用函数

| 操作                        | 作用       |
| --------------------------- | ---------- |
| tf.add(x,y,name=None)       | 求和       |
| tf.substract(x,y,name=None) | 求差       |
| tf.muliply(x,y,name=None)   | 求乘积     |
| tf.div(x,y,name=None)       | 求商       |
| tf.mod(x,y,name=None)       | 取模       |
| tf.abs(x,name=None)         | 求绝对值   |
| tf.negative(x,name=None)    | 取相反数   |
| tf.sign(x,name=None)        | 取符号     |
| tf.square(x,name=None)      | 求平方     |
| tf.round(x,name=None)       | 取整       |
| tf.exp(x,name=None)         | e的x次方   |
| tf.log(x,name=None)         | 求对数     |
| tf.maximum(x,y,name=None)   | 返回最大值 |
| tf.minimum(x,y,name=None)   | 返回最小值 |
| tf.cos(x,name=None)         | 求余弦     |
| tf.sin(x,name=None)         | 求正弦     |
| tf.tan(x,name=None)         | 求正切     |
| tf.atan(x,name=None)        | 求反正切   |
| tf.pow(x,y,name=None)       | 求x的y次幂 |
| tf.sqrt(x,name=None)        | 求开根     |

```python
import tensorflow as tf
a = 3
b = -8
sum = tf.add(a, b)
with tf.Session() as sess:
  print(sess.run(sum)) // -5
```

## 张量数据类型变换

| 操作                                                       | 作用              |
| ---------------------------------------------------------- | ----------------- |
| tf.cast(imput,dtype,name=None)                             | 将输入转换为dtype |
| tf.string_to_number(string_tensor,out_type=None,name=None) | 字符串转数字      |
| tf.to_double(x,name='ToDouble')                            | 转64位浮点        |
| tf.to_float(x,name='ToFloat')                              | 转32位浮点        |
| tf.to_int32(x,name='ToInt32')                              | 转32位整型        |
| tf.to_int64(x,name='ToInt64')                              | 转64位整型        |

```python
import tensorflow as tf
sess = tf.InteractiveSession()
x = tf.constant([3.9, 1.2])
y = tf.cast(x, tf.int32)
print(sess.run(y)) // [3 1]
```

## Shape 操作

TensorFlow从三个层面上来描述张量的数据结构：阶/秩 （Rank），形状（Shape），数据类型（dtype）

### rank( ) 

我们都知道矩阵的秩描述的是矩阵里最大线性无关组的数 量。秩在张量中可以被看做是数据的维度。 

通过tf.rank(input)获得输入张量的阶/秩。返回格式为正整 型。

例子：学校->年级->班级->个人编号，如果以这种形式把所有学生的数据作为一个张量，它的 rank 就是4

```python
import tensorflos as tf
sess = tf.InteractiveSession()
y0 = tf.constant(3)
rank0 = tf.rank(y0)
print(sess.run(rank0)) // 0
// 标量是0维的张量

y1 = [3, 3] // 3和3是同一纬度的，所以rank是1
rank1 = tf.rank(y1)
print(sess.run(rank1)) // 1

y2 = [[3,2], [3,3]]
rank1 = tf.rank(y2)
print(sess.run(rank2)) // 2
```

 

### shape( ) 

刚刚提到了张量的秩是描述的数据的维度，shape描述的是张量每 个维度上各有多少个元素。 

tf.shape(input)获得输入张量的形状。返回格式python中整数列表。

例子：假设有两个学校，每个学校有3个年级，每个年级有3个班 级，每个班级有50人，如果把这些学生的信息作为一个张 量，那这个张量的shape是多少呢？

每个班的数据可以被看做是50个标量的堆叠，一个（50,）的一维张量

 每个年级的数据可以被看做是3个班级的堆叠，是一个（3,50）的二维张量

每个学校的数据可以被看做是3个年级的堆叠，是一个（3,3,50）的三维张量

那么两个学校的数据，就是shape为（2,3,3,50）的四维张量

```python
//获取张量的 shape
t = [  [[1,1,1], [2,2,2]], [[1,2,3,],[4,5,6]]  ]
shape = tf.shape(t)
sess.run(shape) // array([2,2,3])
```

### reshape( )

当张量的shape不符合我们操作的需求时，我们可以通过 reshape操作将它调整为我们想要的形状 

tf.reshape(input,shape)将原始张量input转化为参数 shape指定的形状。

例子：假设有两个学校，每个学校有 3 个年级，每个年级有 3 个班级，每个班级有 50 人，现在进行班级拆分，每个班限额 25 人，那么现在所有学生数据的 shape 应该是多少？

每个班限额25人，所以以前一个年级3个班一共150人，现在学生数不变的情况下，每个年级需要6个班才能满足要求，所以班级拆分后 shape = （2,3,6,25）

我们在保证标量元素数量（学生数）不变的情况下，进行了张量结构 的调整，这个操作就是reshape

```python
t1 = tf.reshape(t,[3,2,2])
shape1 = tf.shape(t1)
sess.run(shape1) // array([3,2,2])
```

### size( ) 

tf.size( input )获得输入张量的标量元素个数。返回格式 python中整数。

假设有两个学校，每个学校有3个年级，每个年级有3个班级，每个班级有50人，所有学生的数据作为一个张量时，size是多少呢？ 

size描述的是标量元素个数，也就是这里的学生人数，所以 size=2*3*3*50 = 900

```python
size = tf.size(t)
sess.run(size) // 12
```

## 切片与合并

### slice( )

通过tf.slice(input, begin, size)对张量input进行切片， 返回值是（shape=size）的张量

```python
slice_source = tf.Variable([[[1,2,3],[4,5,6]], [[7,8,9],[10,11,12]], [[13,14,15],[16,17,18]]])
slice1 = tf.slice(slice_source, [0,0,0]/*从0,0,0的位置开始切*/,[3,1,2])
init = tf.global_variable_initializer()
sess.run(init)
sess.run(slice1) // [ [[1,2]], [[7,8]], [[13,14]]]
```

### split( )

通过tf.split( input,size, axis)对张量input沿着axis维度分离， 分离后的张量集大小由参数size决定

### concat( )

使用tf.concat([input_1,input_2], axis)将input_1和 input_2沿axis维度连接为一个张量

### one_hot( )

tf.one_hot(input, depth, on_value=None, off_value=None)对input进行onehot编码， 类别数目为参数depth

```python
labels = [0,1,2,3,4,5,6,7,8,9]
one_hot_labels = tf.one_hot(labels, depth=10, on_value=1.0, off_value=0.0)
sess.run(one_hot_labels)//会输出一个10*10的稀疏矩阵，对应的就是原来 labels 里的内容
```



## 矩阵操作

| 操作                                                  | 作用                     |
| ----------------------------------------------------- | ------------------------ |
| tf.diag(diagonal)                                     | 返回一个给定对角值的对角 |
| tensor tf.diag_part(input)                            | 获取矩阵对角线上的值     |
| tf.transpose(a)                                       | 矩阵的转职               |
| tf.matmul(a, b, transpose_a=False, transpose_b=False) | 矩阵相乘                 |

## 规约计算

| 操作                 | 作用                                 |
| -------------------- | ------------------------------------ |
| tf.reduce_sum        | 对张量规约求和                       |
| tf.reduce_prod       | 对张量规约求积                       |
| tf.reduce_max/argmax | 求张量中最大元素/最大元素 所在的索引 |
| tf.reduce_min/argmin | 求张量中最小元素/最小元素 所在的索引 |
| tf.reduce_mean       | 求张量中元素的平均值                 |

# 神经网络常用组件

构建神经网络的步骤：

1. 定义网络结构
   - 神经元种类
   - 神经元数量
   - 网络层数
   - 特殊网络结构
2. 定义优化目标
   - 预测准，与真实值偏差小，这就需要合适的损失函数
3. 训练
   - 使损失函数尽可能小，更新参数，梯度下降

### 神经元种类

对于一般表示数值的神经元，我们可以用占位符 placeholder，在训练时通过feed加载数据 对于特殊神经元（卷积、 池化等），tf.nn里面有专门的函 数表示，如tf.nn.conv2d( )，就是一个2维的卷积神经元

## 常用激活函数

通过前面课程对神经网络的学习，我们知道之所以能解决非线性问题（如语音、图像识别），本质上就是在神经元里面加入了非线性单元，也就是激活函数。

激活函数运行时激活神经网络的某一部分的神经元，将激活信息向后传入下一层神经网络。

### Sigmoid

$$
f(x)=\frac{1}{1+e^{-x}}
$$

tf.nn.sigmoid( input ) sigmoid函数的优缺点： 

优点：它的输出映射在（0, 1）内，单调连续，非常适合用作输出层。 

缺点：梯度消失；非0均值

### tanh

$$
tanh(x)=\frac{e^x-e^{-x}}{e^x+e^{-x}}
$$

tf.nn.sigmoid( input ) sigmoid函数的优缺点： 

优点：它的输出映射在（0, 1）内，单调连续，非常适合用作输出层。 

缺点：梯度消失；非0均值

### ReLU

全名：Rectified Linear Unit

f(x) = max(0,x)

tf.nn.relu( input ) relu函数的优缺点： 

优点：避免了梯度消失，快速收敛 

缺点：非0均值；Dead ReLU Problem 

是目前应用最广的激活函数

## 常见损失函数

损失函数是用来描述预测值与真实值之间的差异。损失函 数越小，可以认为我们的预测越准确。 

常用的损失函数有两种：交叉熵（cross entropy）和 MSE（Mean squared error）

### MSE

$$
J(w_0,w_1)=\frac{1}{2n}\sum^n_{i=1}(\bar{y}^{(i)}-y^{(i)})^2=\frac{1}{2n}\sum^n_{i=1}(f_w(x^{(i)})-y^{(i)})^2
$$

MSE作为损失函数非常直观，所有样本预测值与 真实值的差值平方的平均值。通常被用于回归模型 中

### Cross Entropy

$$
H(p,q)=-\sum p(x)log(q(x))
$$

交叉熵刻画的是两个概率分布之间的距离，或可以说它刻画的是通过概率分布q来表达概率分布p的困难程度， p代表正确答案，q代表的是预测值，交叉熵越小，两个 概率的分布约接近。 通常运用在分类问题中。

## 常见分类函数

### Softmax

$$
S_i=\frac{e^i}{\sum_j e^j}
$$

使用Softmax作为输出层，能将上一层神经元的输出映射到（0,1） 的区间内，可以当成概率来理解

Softmax能将输出值之间的差距拉大，使之更加差距明显，便于 分类

对于多分类问题，我们希望得到的是什么？ 

和长度为label数量的one-hot向量 

Softmax得到是什么？ 

和长度为label数量的的形似概率分布的向量 

选取“概率最大”（输出最大）对应的神经元对应的真实类别作为预测值

### 选择合适的分类函数与损失函数

二分类问题：分类函数sigmoid，损失函数cross entropy

多分类问题：分类函数softmax，损失函数cross entropy

## 常见优化器

BGD( Batch Gradient Descent) 批梯度下降

优点：使用所有样本进行梯度下降，避免了重复抽取少数样本引起的误差 

缺点：计算量较大，速度慢

SGD( Stohastic Gradient Descent) 随机梯度下降

优点：使用单一样本进行梯度下降，计算量小，快速计算误差进行梯度下降 

缺点：不可避免地出现重复抽取少量样本情况，导致模型对于整体样本的估计不足 产生误差

MBGD( MiniBatch Gradient Descent) 小批量梯度下降

综合了BGD和SGD采取折中的方法，既保证了运算速度，又避免了重复抽取少数样 本引起的误差

mbgd不是正确率最高的也不是速度最快的，确实速度快的里面正确率最高的，且正确率高的里面速度最快的

## MNIST

MNIST (Mixed National Institute of Standards and Technology (NIST) )，是一个入门级的计算机视觉数据集。

训练集 (training set) 由来自 250 个不同人手写的数字构成，其中 50% 是高中学生，50% 来自人口普查局 (the Census Bureau) 的工作人员，测试集也有是同样比例的手写数字 