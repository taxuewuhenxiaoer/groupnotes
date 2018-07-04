20180704工作报告

1、英语单词背到26个list。。

2、寻找合适的capsules代码 https://qiankun214.github.io/2018/03/11/CapsNet学习笔记/    仅供参考学习

https://github.com/XifengGuo/CapsNet-Keras下周调试一下

胶囊结构

胶囊可以看成一种向量化的神经元。对于单个神经元而言，目前的深度网络中流动的数据均为标量。例如多层感知机的某一个神经元，其输入为若干个标量，输出为一个标量（不考虑批处理）；而对于胶囊而言，每个神经元输入为若干个向量，输出为一个向量（不考虑批处理）。前向传播如下所示：

<img src="http://ww1.sinaimg.cn/large/9f6c4109ly1fsxr7tn962j20n409hgll.jpg"/>



其中Ii为第i个输入（向量），WiWi为第i个权值（矩阵），UiUi为中间变量（向量），由输入和权值叉乘获得。cici为路由权值（标量），需要注意的是该标量是前向传播过程中决定（使用动态路由算法）的，不是通过反向传播优化的参数。Squash为一种激活函数。前向传播使用公式表示如下所示：

<img src="http://ww1.sinaimg.cn/large/9f6c4109ly1fsxralzqs0j20st0bidg7.jpg"/>

由以上可以看出，胶囊结构中流动的数据类型为向量，其激活函数Squash输入一个向量，输出一个向量。

动态路由算法  

动态路由算法适用于确定胶囊结构中cici的算法，其算法伪代码如下所示：

<img src="http://ww1.sinaimg.cn/large/9f6c4109ly1fsxrcpqksgj213j0bmq4o.jpg"/>

<img src="http://ww1.sinaimg.cn/large/9f6c4109ly1fsxre3uqc8j20rm0c775n.jpg"/>

<img src="http://ww1.sinaimg.cn/large/9f6c4109ly1fsxrewxlwbj20sl0ezjss.jpg"/>

整体架构  

原论文中使举了一个识别MNIST手写数字数据集的例子，网络架构如下图所示：

<img src="http://ww1.sinaimg.cn/large/9f6c4109ly1fsxrgo06lzj213c0bxq3h.jpg"/>

第一层为普通的卷积层，使用9*9卷积，输出通道数为256，输出数据尺寸为20*20*256 *

*第二层为卷积层，该卷积层由平行的32个卷积层组成，每个卷积层对应向量数据中的一个向量。每个卷积层均为9*9*256*8（输入channel为256，输出channel为8）。因此输出为6*6*32*8，即窗口大小为6*6，输出channel为32，每个数据为8个分量的向量。 

第三层为胶囊层，行为类似于全连接层。输入为6*6*32=1152个8分量输入向量，输出为10个16分量的向量，对应的有1152*10个权值，每个权值为8*16的矩阵，

最终输出为10个16分量的向量 最终输出10个16分量的向量，最终的分类结果是向量长度最大的输出。