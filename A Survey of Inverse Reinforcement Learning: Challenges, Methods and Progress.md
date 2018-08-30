> many reward functions (including highly degenerate ones such as a function with all rewards
> values zero) explain the observations. This is because the input is usually a
> finite and small set of trajectories (or a policy) and many reward functions in
> the set of all reward functions can generate policies that realize the observed
> demonstration.

![Template for IRL](http://o7ie0tcjk.bkt.clouddn.com/6uaxtxv6f5472xh2.jpg)

Methods:
- Maximum margin optimization
  - [Apprenticeship learning](https://ai.stanford.edu/~ang/papers/icml04-apprentice.pdf)
      ![](http://o7ie0tcjk.bkt.clouddn.com/j56b52rahzkt4lcq.jpg)
  - [HYBRID-IRL](https://arxiv.org/abs/1206.5264)
  - [Multiplicative Weights for Apprenticeship Learning](https://papers.nips.cc/paper/3293-a-game-theoretic-approach-to-apprenticeship-learning.pdf)
  - [Maximum margin planning](http://martin.zinkevich.org/publications/maximummarginplanning.pdf)
- Entropy optimization
  - [Maximum entropy IRL](https://www.aaai.org/Papers/AAAI/2008/AAAI08-227.pdf)
  - [Relative entropy IRL](http://proceedings.mlr.press/v15/boularias11a/boularias11a.pdf)
  - [Guided Cost Learning](https://arxiv.org/abs/1603.00448)
- Bayesian Update
  - [Bayesian IRL](https://www.aaai.org/Papers/IJCAI/2007/IJCAI07-416.pdf)
  - [Bisimulation](https://flowers.inria.fr/mlopes/myrefs/10-ecml-indmet.pdf)
  - [Bayesian](http://acl.mit.edu/papers/michini-ecml-2012.pdf) [nonparametrics](http://acl.mit.edu/papers/michini-icra-2013.pdf)
- Regression and Classification
  - [Feature construction using regression](https://homes.cs.washington.edu/~zoran/firl.pdf)
  - [Multi-label classification](http://www.lifl.fr/~pietquin/pdf/NIPS_2012_EKBPMGOP.pdf)
- Other Miscellaneous Techniques
  - [Gaussian Process IRL](https://homes.cs.washington.edu/~zoran/gpirl.pdf)
  - [Maximum likelihood estimate](http://www.icml-2011.org/papers/478_icmlpaper.pdf)
  - [Path integral](https://ieeexplore.ieee.org/abstract/document/6630743/)
