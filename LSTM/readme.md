LSTM原理及实现
https://blog.csdn.net/gzj_1101/article/details/79376798
LSTM网络

long short term memory，即我们所称呼的LSTM，是为了解决长期以来问题而专门设计出来的，所有的RNN都具有一种重复神经网络模块的链式形式。在标准RNN中，这个重复的结构模块只有一个非常简单的结构，例如一个tanh层。

LSTM 同样是这样的结构，但是重复的模块拥有一个不同的结构。不同于单一神经网络层，这里是有四个，以一种非常特殊的方式进行交互。

不必担心这里的细节。我们会一步一步地剖析 LSTM 解析图。现在，我们先来熟悉一下图中使用的各种元素的图标。

在上面的图例中，每一条黑线传输着一整个向量，从一个节点的输出到其他节点的输入。粉色的圈代表 pointwise 的操作，诸如向量的和，而黄色的矩阵就是学习到的神经网络层。合在一起的线表示向量的连接，分开的线表示内容被复制，然后分发到不同的位置。
LSTM核心思想

LSTM的关键在于细胞的状态整个(绿色的图表示的是一个cell)，和穿过细胞的那条水平线。

细胞状态类似于传送带。直接在整个链上运行，只有一些少量的线性交互。信息在上面流传保持不变会很容易。

若只有上面的那条水平线是没办法实现添加或者删除信息的。而是通过一种叫做 门（gates） 的结构来实现的。

门 可以实现选择性地让信息通过，主要是通过一个 sigmoid 的神经层 和一个逐点相乘的操作来实现的。

sigmoid 层输出（是一个向量）的每个元素都是一个在 0 和 1 之间的实数，表示让对应信息通过的权重（或者占比）。比如， 0 表示“不让任何信息通过”， 1 表示“让所有信息通过”。

LSTM通过三个这样的本结构来实现信息的保护和控制。这三个门分别输入门、遗忘门和输出门。
逐步理解LSTM

现在我们就开始通过三个门逐步的了解LSTM的原理
遗忘门

在我们 LSTM 中的第一步是决定我们会从细胞状态中丢弃什么信息。这个决定通过一个称为忘记门层完成。该门会读取ht−1
和xt，输出一个在 0到 1之间的数值给每个在细胞状态 Ct−1

中的数字。1 表示“完全保留”，0 表示“完全舍弃”。

让我们回到语言模型的例子中来基于已经看到的预测下一个词。在这个问题中，细胞状态可能包含当前主语的性别，因此正确的代词可以被选择出来。当我们看到新的主语，我们希望忘记旧的主语。

其中ht−1
表示的是上一个cell的输出，xt表示的是当前细胞的输入。σ

表示sigmod函数。
输入门

下一步是决定让多少新的信息加入到 cell 状态 中来。实现这个需要包括两个 步骤：首先，一个叫做“input gate layer ”的 sigmoid 层决定哪些信息需要更新；一个 tanh 层生成一个向量，也就是备选的用来更新的内容，Ĉ t

。在下一步，我们把这两部分联合起来，对 cell 的状态进行一个更新。

现在是更新旧细胞状态的时间了，Ct−1
更新为Ct

。前面的步骤已经决定了将会做什么，我们现在就是实际去完成。

我们把旧状态与ft
相乘，丢弃掉我们确定需要丢弃的信息。接着加上it∗C̃ t

。这就是新的候选值，根据我们决定更新每个状态的程度进行变化。

在语言模型的例子中，这就是我们实际根据前面确定的目标，丢弃旧代词的性别信息并添加新的信息的地方。

输出门

最终，我们需要确定输出什么值。这个输出将会基于我们的细胞状态，但是也是一个过滤后的版本。首先，我们运行一个 sigmoid 层来确定细胞状态的哪个部分将输出出去。接着，我们把细胞状态通过 tanh 进行处理（得到一个在 -1 到 1 之间的值）并将它和 sigmoid 门的输出相乘，最终我们仅仅会输出我们确定输出的那部分。

在语言模型的例子中，因为他就看到了一个 代词，可能需要输出与一个 动词 相关的信息。例如，可能输出是否代词是单数还是负数，这样如果是动词的话，我们也知道动词需要进行的词形变化。

LSTM变体

原文这部分介绍了 LSTM 的几个变种，还有这些变形的作用。在这里我就不再写了。有兴趣的可以直接阅读原文。

下面主要讲一下其中比较著名的变种 GRU（Gated Recurrent Unit ），这是由 Cho, et al. (2014) 提出。在 GRU 中，如下图所示，只有两个门：重置门（reset gate）和更新门（update gate）。同时在这个结构中，把细胞状态和隐藏状态进行了合并。最后模型比标准的 LSTM 结构要简单，而且这个结构后来也非常流行。

其中， rt
表示重置门，zt表示更新门。重置门决定是否将之前的状态忘记。(作用相当于合并了 LSTM 中的遗忘门和传入门）当rt趋于0的时候，前一个时刻的状态信息ht−1会被忘掉，隐藏状态ĥ t会被重置为当前输入的信息。更新门决定是否要将隐藏状态更新为新的状态ĥ t

（作用相当于 LSTM 中的输出门） 。

和 LSTM 比较一下：
- GRU 少一个门，同时少了细胞状态Ct
。
- 在 LSTM 中，通过遗忘门和传入门控制信息的保留和传入；GRU 则通过重置门来控制是否要保留原来隐藏状态的信息，但是不再限制当前信息的传入。
- 在 LSTM 中，虽然得到了新的细胞状态 Ct，但是还不能直接输出，而是需要经过一个过滤的处理:ht=ot∗tanh(Ct)；同样，在 GRU 中, 虽然我们也得到了新的隐藏状态ĥ t， 但是还不能直接输出，而是通过更新门来控制最后的输出：ht=(1−zt)∗ht−1+zt∗ĥ t

多层LSTM

多层LSTM是将LSTM进行叠加，其优点是能够在高层更抽象的表达特征，并且减少神经元的个数，增加识别准确率并且降低训练时间。具体信息参考[3]
LSTM实现手写数字

这里我们利用的数据集是tensorflow提供的一个手写数字数据集。该数据集是一个包含n张28*28的数据集。
设置LSTM参数

# -*- coding: utf-8 -*-
import tensorflow as tf
from tensorflow.contrib import rnn

import numpy as np
import input_data

# configuration
#                        O * W + b -> 10 labels for each image, O[? 28], W[28 10], B[10]
#                       ^ (O: output 28 vec from 28 vec input)
#                       |
#      +-+  +-+       +--+
#      |1|->|2|-> ... |28| time_step_size = 28
#      +-+  +-+       +--+
#       ^    ^    ...  ^
#       |    |         |
# img1:[28] [28]  ... [28]
# img2:[28] [28]  ... [28]
# img3:[28] [28]  ... [28]
# ...
# img128 or img256 (batch_size or test_size 256)
#      each input size = input_vec_size=lstm_size=28

# configuration variables
input_vec_size = lstm_size = 28 # 输入向量的维度
time_step_size = 28 # 循环层长度

batch_size = 128
test_size = 256

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29

这里设置将batch_size设置为128，time_step_size表示的是lstm神经元的个数，这里设置为28个(和图片的尺寸有关？)，input_vec_size表示一次输入的像素数。
初始化权值参数

def init_weights(shape):
    return tf.Variable(tf.random_normal(shape, stddev=0.01))

def model(X, W, B, lstm_size):
    # X, input shape: (batch_size, time_step_size, input_vec_size)
    # XT shape: (time_step_size, batch_size, input_vec_size)
    #对这一步操作还不是太理解，为什么需要将第一行和第二行置换
    XT = tf.transpose(X, [1, 0, 2])  # permute time_step_size and batch_size,[28, 128, 28]
    # XR shape: (time_step_size * batch_size, input_vec_size)
    XR = tf.reshape(XT, [-1, lstm_size]) # each row has input for each lstm cell (lstm_size=input_vec_size)

    # Each array shape: (batch_size, input_vec_size)
    X_split = tf.split(XR, time_step_size, 0) # split them to time_step_size (28 arrays),shape = [(128, 28),(128, 28)...]
    # Make lstm with lstm_size (each input vector size). num_units=lstm_size; forget_bias=1.0
    lstm = rnn.BasicLSTMCell(lstm_size, forget_bias=1.0, state_is_tuple=True)

    # Get lstm cell output, time_step_size (28) arrays with lstm_size output: (batch_size, lstm_size)
    # rnn..static_rnn()的输出对应于每一个timestep，如果只关心最后一步的输出，取outputs[-1]即可
    outputs, _states = rnn.static_rnn(lstm, X_split, dtype=tf.float32)  # 时间序列上每个Cell的输出:[... shape=(128, 28)..]
    # tanh activation
    # Get the last output
    return tf.matmul(outputs[-1], W) + B, lstm.state_size # State size to initialize the state

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22

init_weigths函数利用正态分布随机生成参数的初始值，model的四个参数分别为：X为输入的数据，W表示的是28*10的权值(标签为0-9)，B表示的是偏置，维度和W一样。这里首先将一批128*（28*28）的图片放进神经网络。然后进行相关的操作(注释已经写得很明白了，这里就不再赘述)，然后利用WX+B求出预测结果，同时返回lstm的尺寸
训练

py_x, state_size = model(X, W, B, lstm_size)

cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logits=py_x, labels=Y))
train_op = tf.train.RMSPropOptimizer(0.001, 0.9).minimize(cost)

    1
    2
    3
    4

然后通过交叉熵计算误差，反复训练得到最优值。

源代码：
https://github.com/geroge-gao/deeplearning/tree/master/LSTM
参考资料

[1].https://www.jianshu.com/p/9dc9f41f0b29

[2].http://blog.csdn.net/Jerr__y/article/details/58598296
[3].Stacked Long Short-Term Memory Networks 
--------------------- 
作者：gzj_1101 
来源：CSDN 
原文：https://blog.csdn.net/gzj_1101/article/details/79376798 
版权声明：本文为博主原创文章，转载请附上博文链接！
