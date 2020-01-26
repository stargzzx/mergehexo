---
title: 遗传算法 GA  Microbial Genetic Algorithm (精英策略)
date: 2018-10-25 11:41:26
categories:
- 进化算法
tags:
- 进化算法
- GA
---
内容如题目所示。
<!-- more -->
## 轮盘赌算法
{% codeblock %}

import numpy as np
import matplotlib.pyplot as plt

DNA_SIZE = 10           # DNA 的长度 0 和 1 的长度我取十个单位
						# 很多人可能有疑问，为什么我的定义域是 0 - 5 的取值范围，但是我的二进制代码却能达到 10 位，这不是远远超出 5 的范围吗
						# 是的， 10 位二进制代码确实超过了 5 ，但是我们在翻译成是十进制的时候，却是将数值进行了收缩，使得最后所得的数在 0 - 5 ，也就是下面翻译 DNA 的代码段
						# 这个，我在 GA 的原理部分讲的也比较清楚了
POP_SIZE = 100          #    种群的个数
CORSS_RATE = 0.8        # 交叉的概率
MUTATION_RATE = 0.003   # 变异的概率
N_GENERATIONS = 200     # 循环多少代
X_BOUND = [0, 5]        # 横坐标，换句话说就是定义域

def F(x): # 这就是求值函数，我们的目标就是找出让这个函数最大值的 x
    return np.sin(10 * x) * x + np.cos(2 * x) * x
	
def get_fitness(pred): # 获取个体的适应度 物竞
    # 下面这个式子主要是让个体的适应度不是负数，至于是加 1e-3 还是加 1 或者其他的数都是没问题的
    # 只要不破坏他们之间的关系就行
    return pred + 1e-3 - np.min(pred)

def translateDNA(pop): # 翻译DNA 因为 DNA 是在电脑中是一段二进制代码，所以要将其翻译成定义域内的数值
    return X_BOUND[0] + pop.dot(2 ** np.arange(DNA_SIZE)[::-1]) / float(2 ** DNA_SIZE - 1) * (X_BOUND[1] - X_BOUND[0])

def select(pop,fitness): # 选择，天择
    index = np.random.choice(np.arange(POP_SIZE),size=POP_SIZE,replace=True,p=fitness/fitness.sum())
    return pop[index]

def crossover(parent,pop): # 交叉遗传
    # 这段代码很有意思，利用了 numpy 的一个重要的小技巧，这个小技巧我将其发在 numpy 小技巧 1 中
    if np.random.rand() < CORSS_RATE:
        # 选择一个父系，即随机一个数，这个数代表了种群中的一个个体，这个个体在下面就会当作祖先的其中一方
        i = np.random.randint(0,POP_SIZE,size=1)
        # 随机一个基因长度的布尔数据组
        cross_points = np.random.randint(0, 2, size=DNA_SIZE).astype(np.bool)
        # 下面的这段代码非常的关键
        # 当布尔值为 1 的时候保留自己的基因，当布尔值为 0 的时候是获取别人的基因
        # 十分神奇的代码，装逼必备
        parent[cross_points] = pop[i, cross_points]
    return parent


def mutate(child):  # 变异
    for point in range(DNA_SIZE):
        if np.random.rand() < MUTATION_RATE:
            #   这是三元操作符，很有意思的操作方法，装逼必备，你可以去我的 python 详解中的小技巧中的看
            child[point] = 1 if child[point] == 0 else 0
    return child

# 初始化 100 个 长度为 10 的基因，全部是二进制代码
pop = np.random.randint(2,size=(POP_SIZE,DNA_SIZE))

# 这就是初始化 x ，这个代码很装逼，必备，为了画出曲线
x = np.linspace(*X_BOUND,200)
plt.plot(x,F(x))

for i in range(N_GENERATIONS):
    F_values = F(translateDNA(pop))

    if 'sca' in globals(): sca.remove()
    sca = plt.scatter(translateDNA(pop), F_values, s=200, lw=0, c='red', alpha=0.5)
    plt.pause(0.05)

    # 获取每个个体的适应度
    fitness = get_fitness(F_values)
    print("Most fitted DNA: ", pop[np.argmax(fitness), :])
    # 物竞天择
    pop = select(pop, fitness)
    # 备份上一个种群
    pop_copy = pop.copy()
    # 进行交叉
    for parent in pop:
        # 两个个体之间相互交叉遗传
        child = crossover(parent, pop_copy)
        # 单个个体进行变异
        child = mutate(child)
        # 孩子长大
        parent[:] = child

plt.show()

{% endcodeblock %}
## 经营策略
### 原代码
{% codeblock %}

"""
Visualize Microbial Genetic Algorithm to find the maximum point in a graph.
Visit my tutorial website for more: https://morvanzhou.github.io/tutorials/
"""
import numpy as np
import matplotlib.pyplot as plt

DNA_SIZE = 10            # DNA length
POP_SIZE = 20            # population size
CROSS_RATE = 0.6         # mating probability (DNA crossover)
MUTATION_RATE = 0.01     # mutation probability
N_GENERATIONS = 200
X_BOUND = [0, 5]         # x upper and lower bounds


def F(x): return np.sin(10*x)*x + np.cos(2*x)*x     # to find the maximum of this function


class MGA(object):
    def __init__(self, DNA_size, DNA_bound, cross_rate, mutation_rate, pop_size):
        self.DNA_size = DNA_size
        DNA_bound[1] += 1
        self.DNA_bound = DNA_bound
        self.cross_rate = cross_rate
        self.mutate_rate = mutation_rate
        self.pop_size = pop_size

        # initial DNAs for winner and loser
        self.pop = np.random.randint(*DNA_bound, size=(1, self.DNA_size)).repeat(pop_size, axis=0)

    def translateDNA(self, pop):
        # convert binary DNA to decimal and normalize it to a range(0, 5)
        return pop.dot(2 ** np.arange(self.DNA_size)[::-1]) / float(2 ** self.DNA_size - 1) * X_BOUND[1]

    def get_fitness(self, product):
        return product      # it is OK to use product value as fitness in here

    def crossover(self, loser_winner):      # crossover for loser
        cross_idx = np.empty((self.DNA_size,)).astype(np.bool)
        for i in range(self.DNA_size):
            cross_idx[i] = True if np.random.rand() < self.cross_rate else False  # crossover index
        loser_winner[0, cross_idx] = loser_winner[1, cross_idx]  # assign winners genes to loser
        return loser_winner

    def mutate(self, loser_winner):         # mutation for loser
        mutation_idx = np.empty((self.DNA_size,)).astype(np.bool)
        for i in range(self.DNA_size):
            mutation_idx[i] = True if np.random.rand() < self.mutate_rate else False  # mutation index
        # flip values in mutation points
        loser_winner[0, mutation_idx] = ~loser_winner[0, mutation_idx].astype(np.bool)
        return loser_winner

    def evolve(self, n):    # nature selection wrt pop's fitness
        for _ in range(n):  # random pick and compare n times
            sub_pop_idx = np.random.choice(np.arange(0, self.pop_size), size=2, replace=False)
            sub_pop = self.pop[sub_pop_idx]             # pick 2 from pop
            product = F(self.translateDNA(sub_pop))
            fitness = self.get_fitness(product)
            loser_winner_idx = np.argsort(fitness)
            loser_winner = sub_pop[loser_winner_idx]    # the first is loser and second is winner
            loser_winner = self.crossover(loser_winner)
            loser_winner = self.mutate(loser_winner)
            self.pop[sub_pop_idx] = loser_winner

        DNA_prod = self.translateDNA(self.pop)
        pred = F(DNA_prod)
        return DNA_prod, pred


plt.ion()       # something about plotting
x = np.linspace(*X_BOUND, 200)
plt.plot(x, F(x))

ga = MGA(DNA_size=DNA_SIZE, DNA_bound=[0, 1], cross_rate=CROSS_RATE, mutation_rate=MUTATION_RATE, pop_size=POP_SIZE)

for _ in range(N_GENERATIONS):                    # 100 generations
    DNA_prod, pred = ga.evolve(5)          # natural selection, crossover and mutation

    # something about plotting
    if 'sca' in globals(): sca.remove()
    sca = plt.scatter(DNA_prod, pred, s=200, lw=0, c='red', alpha=0.5); plt.pause(0.05)

plt.ioff();plt.show()

{% endcodeblock %}
让人感到奇怪的是，他写的基于经营策略的遗传算法，和我想的不大一样，所以，我不打算重读代码了，但是思想都是一样的，只是和经典遗传做个微小的改动而已。
