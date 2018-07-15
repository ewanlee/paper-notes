这是一篇采用强化学习做聚类的论文，首先作者给出了一个之前论文提出的方法（伯努利算法）：

1. 随机初始化聚类中心w
2. 从数据集中采样一个点x
3. 计算x属于每一个聚类中心的概率：
![](http://o7ie0tcjk.bkt.clouddn.com/z3eqxu0wczqbh5xa.jpg)
4. 令P_k* = max P_i, k* = argmax P_i, y_k*从P_k*中进行采样，相当于从一个伯努利分布中采样，所以y_k* = {0, 1}
5. 计算r_k*, 如果y_k* = 1, 则r_k* = 1; 否则 = -1
6. 计算w_k*的梯度：
![](http://o7ie0tcjk.bkt.clouddn.com/0ma6ptwr4ntkbtsm.jpg)
7. 重复2到6步直到收敛

根据梯度计算公式可以看出来，y_k* - P_k*这个值，当P_k*非常接近1的时候或者非常接近0的时候都很小；如果P_k*这个值接近0.5的时候，因为y_k*是0
还是1不确定，所以这个值会比较大。也就是说，当最开始一切不确定的时候，参数会更新的会比较快，越到后面越小直到收敛。

作者表明以上算法有个很大的缺陷，因为每次只更新k*，如果最开始聚类中心初始化地很不好的话，那它永远得不到更新：
![](http://o7ie0tcjk.bkt.clouddn.com/0ccmg6mv7yzgseu7.jpg)

所以作者提出了一个改进版（RL1），改了一下r_i的计算方式以及梯度更新公式：
![](http://o7ie0tcjk.bkt.clouddn.com/ln9w4086ck1exdgh.jpg)

因为现在所有的聚类中心都会更新，就不存在初始化好坏的问题。而且某个聚类中心梯度更新的速度与所有其他的都相关，因为k*需要从所有的聚类中心里
选取。并且除离采样点最近的聚类中心之外，离采样点越近的聚类中心同样更新越快，这样离采样点比较远的属于其他聚类的聚类中心就不会受到太大干扰。

![](http://o7ie0tcjk.bkt.clouddn.com/n52ck8btghcxjzte.jpg)

通过上图我们看到RL1很好的避免了初始化对最后结果的影响。然后作者又对RL1魔改了一下，提出了RL2，其实就是改一下reward的计算方式：

![](http://o7ie0tcjk.bkt.clouddn.com/28e1vfhjl98q0dnf.jpg)

我们看到对于k*这个公式和RL1的结果是一样的，我把beta小于1大于0的图像画了出来：

![](http://o7ie0tcjk.bkt.clouddn.com/ejvx1xicyzcqs5s3.jpg)

我们可以看到，在x越大的时候（其实就是RL1的分子），新函数的值会增长的越来越小，我觉得这样修改的目的是为了避免算法初期梯度爆炸的隐患

![](http://o7ie0tcjk.bkt.clouddn.com/sol2rlufgub7xrdz.jpg)

其实效果和RL1差不多，感觉最后的收敛点会更合理一些。然后作者继续魔改，把reward函数改成了这样（RL3）：

![](http://o7ie0tcjk.bkt.clouddn.com/j4uzblx79c3gpto9.jpg)

都到了4次方了，继续分析一下这个reward函数，把分子的分母放到整体的分母上，可以看到现在reward并不是说你离采样点越近就越大了，你不仅要
自己离得近，而且其他聚类中心还要离得远才可以，这样使得梯度的更新更加合理。

最后的篇幅都是在介绍怎样使得找到的聚类中心能够保留原始数据的拓扑结构，就是要达到这样的效果：

![](http://o7ie0tcjk.bkt.clouddn.com/uvlbqhqelrk7dshf.jpg)

这个还找不到具体对应的应用场景，就先不总结了。


