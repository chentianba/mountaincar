# MountainCar

MountainCar是OpenAI Gym中的一个经典环境，其具有[离散控制](https://github.com/openai/gym/wiki/MountainCar-v0)和[连续控制](https://github.com/openai/gym/wiki/MountainCarContinuous-v0)两种状态,其中状态是连续的，维度为Box(2,)
| Num | Observation | Min | Max |
| :-: | :-: | :-: | :-: |
| 0 | Position | -1.2 | 0.6 |
| 1 | Velocity | -0.07 | 0.07 |
其中动作分为两种：离散和连续
| Env | Type | Num | Action |
| :-: | :-: | :-: | :-: |
| MountainCar | Discrete(3) | negative | push car to the left |
| | | positive | push cat to the right |
| Continuous | Box(1) | 0 | push left |
| | | 1 | no push |
| | | 2 | push right |


## DQN

DQN适合处理动作离散的控制，是一种基于价值的RL算法

## DDPG

DDPG适合处理控制动作连续的环境，是一种基于策略的RL算法。

[初始代码](https://github.com/ljpzzz/machinelearning/blob/master/reinforcement-learning/ddpg.py)来自[刘建平Pinard的博客配套代码](https://github.com/chentianba/machinelearning)，初始代码中使用Pendulum-v0的环境，经过简单的训练能得到训练成功的结果。但是，当将该算法及`超参配置`运用到MountainCarContinuous-v0环境中，训练结果失败。

于是，在网上找到了基于DDPG的成功MountainCarContinuous-v0实例[DDPG_MountainCar](https://github.com/IgnacioCarlucho/DDPG_MountainCar)，经过实验对比，发现两者关键的不同在于：
1. DDPG_MountainCar使用一种经典的OU噪声
2. DDPG_MountainCar将a_bound设为10，而不是使用默认的1(后经过实验，将a_bound设置为1也能得到训练结果)

这说明对DDPG算法来说，能够得到好的训练结果一个重要因素是**噪声的设置**
> 分析：初始代码中噪声使用简单的随机噪声，导致不能很好的探索环境，于是小车在随机早期的探索过程中，从来没有到达过goal，这导致小车无法从目标开始反向收敛，从而导致最终小车收敛在山的底部。因此，**一个好的噪声设置**能够使得agent充分探索环境的各个部分，从而在找到目标后能够反向收敛。

训练中的一个小技巧是：
> 选择好的评价方式，对当前训练的网络进行评价：
* 在MountainCarContinuous-v0中可以用到达goal的步数，步数越少，结果越好；步数小于指定值，即停止训练
* 在Pendulum-v0中使用周期累积奖励作为评判，累计奖励越大，结果越好；累计奖励大于指定值，即停止训练