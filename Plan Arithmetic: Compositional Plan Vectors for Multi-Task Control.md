这篇论文的观点挺有意思，其目标是学习一个 skill 的 embedding，这个 embedding 具有可组合性。
例如对于一个```把红色积木从盒子中拿出来并把黄色积木放到盒子里```这样的任务，整个任务（skill）的 embedding 是 ```a```, 
把红色积木从盒子中拿出来这个 skill 的 embedding 是 ```b```，那么 ```a - b``` 就表示把黄色积木放到盒子里这个 skill 的 embedding。

本文在 one-shot imitation learning 任务上应用上述想法，但是这个想法显然可以利用到其他任务中，
例如 reinforcement learnin, inverse reinforcement learning 等等。
一个 one-shot imitation learning 任务可以描述如下：
1）首先，在训练阶段，会有多个 demonstration/reference trajectory 的 pairs，目标是基于 reference trajectory 以及 demonstration 中的
某个时刻的 observation 来去预测其对应的 action；
2）其次，在测试阶段，则会输入训练阶段没有见过的 reference trajectory， 然后让 agent 去根据训练好的 policy 在环境中进行决策。

这里有几点需要声明：
1. demonstration/reference trajectory 是完成的同一个任务，可以认为两者是相同的，但是后者不包含 action，只有 observation 的序列，
这样完成的任务才能叫 one-shot imitation learning，意思是只要看一次别人是怎么做的，机器人就可以学会怎么做。
、这里别人是怎么做的就反映在 reference trajectory 上，因为只能看到别人的动作对环境造成的影响，但是不能知道人类做了什么动作。
2. 本文中假设需要完成的任务都会给环境带来改变。诸如```把一个箱子搬起来然后再原样放回原位置```这样的任务不在本文考虑之内。
所以本文中并没有考虑整个序列，而是只需要第一个 observation 以及最后一个 observation。

为了使得学习到的 embedding 具有上述可组合性，需要引入以下几个约束：
1. 在进行训练时，训练的 policy 不是基于 reference trajectory 的 embedding，而是基于 reference trajectory 的 embedding 与当前时刻 observation
序列的 embedding 的差值（注意，这两个 embedding 的输入都只要序列的第一个 observation 以及最后一个 observation）
2. 使用 triplet loss，保证当前时刻之前的 observation 序列的 embedding 与当前时刻之后的 observation 序列的 embedding 之和，与 observation 对应
的 reference trajectory 的 embedding 尽可能相近，与不与其对应的其他 reference trajectory 的 embedding 尽可能较远
3. 使用 triplet loss，保证整个 observation 序列的 embedding 与其对应的 reference trajectory 的 embedding 尽可能相近，
与不与其对应的其他 reference trajectory 的 embedding 尽可能较远

上述三个 loss 具体公式如下：

1. ![](http://latex.codecogs.com/gif.latex?%24%24%20%5Cmathcal%7BL%7D_%7B%5Cmathrm%7BL%7D%7D%28%5Cmathcal%7BD%7D%2C%20%5Ctheta%2C%20%5Cphi%29%3D%5Csum_%7Bi%3D0%7D%5E%7BN%7D%20%5Csum_%7Bt%3D0%7D%5E%7BH%5E%7Bi%7D%7D-%5Clog%20%5Cleft%28%5Cpi_%7B%5Ctheta%7D%5Cleft%28%5Cmathbf%7Ba%7D_%7Bt%7D%5E%7Bi%7D%20%7C%20%5Cmathbf%7Bo%7D_%7Bt%7D%5E%7Bi%7D%2C%20g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D%5E%7B%5Cmathrm%7Bref%7D_%7B0%7D%5E%7Bi%7D%7D%2C%20%5Cmathbf%7Bo%7D%5E%7B%5Cmathrm%7Bref%7D_%7BT%7D%5E%7Bi%7D%7D%5Cright%29-g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7B0%7D%5E%7Bi%7D%2C%20%5Cmathbf%7Bo%7D_%7Bt%7D%5E%7Bi%7D%5Cright%29%5Cright%29%5Cright%29%20%24%24)
2. ![](http://latex.codecogs.com/gif.latex?%24%24%20%5Cbegin%7Baligned%7D%20l_%7B%5Ctext%20%7Btri%20%7D%7D%28a%2C%20p%2C%20n%29%20%26%3D%5Cmax%20%5Cleft%5C%7B%5C%7Ca-p%5C%7C_%7B2%7D-%5C%7Ca-n%5C%7C_%7B2%7D&plus;1.0%2C0%5Cright%5C%7D%20%5C%5C%20%5Cmathcal%7BL%7D_%7B%5Ctext%20%7BHom%20%7D%7D%28%5Cmathcal%7BD%7D%2C%20%5Cphi%29%20%26%3D%20%5Csum_%7Bi%3D0%7D%5E%7BN%7D%20%5Csum_%7Bt%3D0%7D%5E%7BH%5E%7Bi%7D%7D%20l_%7B%5Ctext%20%7Btri%20%7D%7D%5Cleft%28g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7B0%7D%5E%7Bi%7D%2C%20%5Cmathbf%7Bo%7D_%7Bt%7D%5E%7Bi%7D%5Cright%29&plus;g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7Bt%7D%5E%7Bi%7D%2C%20%5Cmathbf%7Bo%7D_%7BT%7D%5E%7Bi%7D%5Cright%29%2C%20g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7B0%7D%5E%7Bi%7D%2C%20%5Cmathbf%7Bo%7D_%7BT%7D%5E%7Bi%7D%5Cright%29%2C%20g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7B0%7D%5E%7Bj%7D%2C%20%5Cmathbf%7Bo%7D_%7BT%7D%5E%7Bj%7D%5Cright%29%5Cright%29%20%5Cend%7Baligned%7D%20%24%24)
3. ![](http://latex.codecogs.com/gif.latex?%5Cmathcal%7BL%7D_%7B%5Ctext%20%7BPair%20%7D%7D%28%5Cmathcal%7BD%7D%2C%20%5Cphi%29%20%5Csum_%7Bi%3D0%7D%5E%7BN%7D%20%5Csum_%7Bt%3D0%7D%5E%7BH%5E%7Bi%7D%7D%20l_%7B%5Ctext%20%7Btri%20%7D%7D%5Cleft%28%20g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7B0%7D%5E%7Bi%7D%2C%20%5Cmathbf%7Bo%7D_%7BT%7D%5E%7Bi%7D%5Cright%29%2C%20g_%7B%5Cphi%7D%5Cleft%28%20%5Cmathbf%7Bo%7D_%7B0%7D%5E%7B%7B%5Ctext%20%7Bref%20%7D%7D%5Ei%7D%2C%20%5Cmathbf%7Bo%7D_%7BT%7D%5E%7B%7B%5Ctext%20%7Bref%20%7D%7D%5E%7Bi%7D%7D%20%5Cright%29%2C%20g_%7B%5Cphi%7D%5Cleft%28%5Cmathbf%7Bo%7D_%7B0%7D%5E%7B%7B%5Ctext%20%7Bref%20%7D%7D%5Ej%7D%2C%20%5Cmathbf%7Bo%7D_%7BT%7D%5E%7B%7B%5Ctext%20%7Bref%20%7D%7D%5Ej%7D%5Cright%29%5Cright%29)
