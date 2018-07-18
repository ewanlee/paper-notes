通过模仿youtube上的玩家视频来学习atari上比较难的游戏，比如MONTEZUMA’S REVENGE。

难点在于youtube上的视频与ALE平台上的游戏在画面上存在一个domain gap，包括分辨率、颜色等等都不相同。因此作者提出了一个能够
学习到足够general的virtual representation的学习框架。主要思想，首先就是输入一个视频序列中的两帧，预测它们之间的时间差，
由于把时间差可能取值离散化了，因此是个分类问题，文中称为TDC，这样学出来的特征会更加注重图像中物体的移动等等，从而减小domain gap
的影响；第二个思想就是把音频信息也加进去，训练一个输入为音频与视频，输出为两者之间时间差的分类器，出发点是因为atari游戏中比较重要
的行为，例如跳跃、得分等等都会有特别的音效，作者认为这样可以辅助特征的训练，虽然ALE平台没有音频，但是在训练阶段加入会使得学到的
特征更加有效，测试阶段直接用就可以了。

![](http://o7ie0tcjk.bkt.clouddn.com/7j0eqpkbp7ihbghd.jpg)

reward的设计，由于是一个one-shot的imitation learning，因此reward要体现这一点，具体做法是在一个视频中插入多个checkpoint，然后
比较agent探索时的observation与这些checkpoints之间相似度（特征之间的距离）：

![](http://o7ie0tcjk.bkt.clouddn.com/szk8ud54s36866s3.jpg)

强化算法是 [IMPALA](https://arxiv.org/abs/1802.01561)
