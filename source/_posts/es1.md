---
title: 遗传算法 GA 具体案例
date: 2018-10-16 09:23:30
categories:
- 进化算法
tags:
- GA
- 进化算法
---
那就话怎么说来着，Do not tell me,just show your code.
<!-- more -->
[莫烦python](https://morvanzhou.github.io/)
## GA 解决函数最优解问题
### 原理
这是最基础的问题，所以不再陈述
### 代码
{% codeblock %}

"""
Visualize Genetic Algorithm to find a maximum point in a function.
Visit my tutorial website for more: https://morvanzhou.github.io/tutorials/
"""
import numpy as np
import matplotlib.pyplot as plt

DNA_SIZE = 10            # DNA length
POP_SIZE = 100           # population size
CROSS_RATE = 0.8         # mating probability (DNA crossover)
MUTATION_RATE = 0.003    # mutation probability
N_GENERATIONS = 200
X_BOUND = [0, 5]         # x upper and lower bounds


def F(x): return np.sin(10*x)*x + np.cos(2*x)*x     # to find the maximum of this function


# find non-zero fitness for selection
def get_fitness(pred): return pred + 1e-3 - np.min(pred)


# convert binary DNA to decimal and normalize it to a range(0, 5)
def translateDNA(pop): return pop.dot(2 ** np.arange(DNA_SIZE)[::-1]) / float(2**DNA_SIZE-1) * X_BOUND[1]


def select(pop, fitness):    # nature selection wrt pop's fitness
    idx = np.random.choice(np.arange(POP_SIZE), size=POP_SIZE, replace=True,
                           p=fitness/fitness.sum())
    return pop[idx]


def crossover(parent, pop):     # mating process (genes crossover)
    if np.random.rand() < CROSS_RATE:
        i_ = np.random.randint(0, POP_SIZE, size=1)                             # select another individual from pop
        cross_points = np.random.randint(0, 2, size=DNA_SIZE).astype(np.bool)   # choose crossover points
        parent[cross_points] = pop[i_, cross_points]                            # mating and produce one child
    return parent


def mutate(child):
    for point in range(DNA_SIZE):
        if np.random.rand() < MUTATION_RATE:
            child[point] = 1 if child[point] == 0 else 0
    return child


pop = np.random.randint(2, size=(POP_SIZE, DNA_SIZE))   # initialize the pop DNA

plt.ion()       # something about plotting
x = np.linspace(*X_BOUND, 200)
plt.plot(x, F(x))

for _ in range(N_GENERATIONS):
    F_values = F(translateDNA(pop))    # compute function value by extracting DNA

    # something about plotting
    if 'sca' in globals(): sca.remove()
    sca = plt.scatter(translateDNA(pop), F_values, s=200, lw=0, c='red', alpha=0.5); plt.pause(0.05)

    # GA part (evolution)
    fitness = get_fitness(F_values)
    print("Most fitted DNA: ", pop[np.argmax(fitness), :])
    pop = select(pop, fitness)
    pop_copy = pop.copy()
    for parent in pop:
        child = crossover(parent, pop_copy)
        child = mutate(child)
        parent[:] = child       # parent is replaced by its child

plt.ioff(); plt.show()

{% endcodeblock %}
### 效果图
{% img /images/machinelearningalgorithm/6_0.gif %}
### 解读
第一点首先先写出四大基本框架
{% codeblock %}

def get_fitness(pred): # 获取个体的适应度
    return pred
def translateDNA(pop): # 翻译DNA 因为 DNA 是在电脑中是一段二进制代码，所以要将其翻译成定义域内的数值
    pass
def select(pop,fitness): # 这就是选择代码，根据个体的适应度来选择 
	pass
def corssover(parent,pop): # 交叉遗传
    pass
def mutate(child):  # 变异
    pass
	
{% endcodeblock %}
上面的代码就是遗传算法的主要函数，通过就算每个个体的适应度，推算出他们中基因是否能遗传给下一代，然后存活下来的基因进行交叉，然后产生新的一代。
下面我将所有的代码都重新编排了一下，并写上了很详细的注释，也算是给后来者造福。。。
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
关于这段代码，莫烦的交叉和变异采用的是不同的方法。不过，也很新颖。
在我写的 GA 原理中，交叉是遗传父的前一半代码，然后遗传母的后一半代码。但是，莫烦的交叉是真交叉，也就是某个位置随机选择是父基因还是母基因。
他的变异也是每一个基因点都有可能变异，而我的原理是一段 DNA 中只有一个变异。
当然，这些都无伤大雅，比较那个思想的好坏，可以通过数学公式推导，但是，还是天命比较重要。

## GA 解决句子匹配问题
### 问题描述
给你一段英文，然后随机生出种群，经过繁衍进化后，所得后代可以完全匹配这段英文。
### 原理
我们都知道，英文编码也就是 ASCII 码中。
ASCII 表
{% img /images/machinelearningalgorithm/6_1.JPG %}
{% img /images/machinelearningalgorithm/6_2.JPG %}
从图中我们可以知道，电脑能输出的 ADCII 码的编号就是 32 - 126 。
所以，我们只要把原来的英文语句转化成 ASCII 码的列表，然后，我们在随机和原来英文等长度的 32 - 126 数字的代码，作为初始种群。
比较和原来数组中数字的相似度，来确定是否遗传他的基因。
### 源代码
{% codeblock %}

"""
Visualize Genetic Algorithm to match the target phrase.
Visit my tutorial website for more: https://morvanzhou.github.io/tutorials/
"""
import numpy as np

TARGET_PHRASE = 'You get it!'       # target DNA
POP_SIZE = 300                      # population size
CROSS_RATE = 0.4                    # mating probability (DNA crossover)
MUTATION_RATE = 0.01                # mutation probability
N_GENERATIONS = 1000

DNA_SIZE = len(TARGET_PHRASE)
TARGET_ASCII = np.fromstring(TARGET_PHRASE, dtype=np.uint8)  # convert string to number
ASCII_BOUND = [32, 126]


class GA(object):
    def __init__(self, DNA_size, DNA_bound, cross_rate, mutation_rate, pop_size):
        self.DNA_size = DNA_size
        DNA_bound[1] += 1
        self.DNA_bound = DNA_bound
        self.cross_rate = cross_rate
        self.mutate_rate = mutation_rate
        self.pop_size = pop_size

        self.pop = np.random.randint(*DNA_bound, size=(pop_size, DNA_size)).astype(np.int8)  # int8 for convert to ASCII

    def translateDNA(self, DNA):                 # convert to readable string
        return DNA.tostring().decode('ascii')

    def get_fitness(self):                      # count how many character matches
        match_count = (self.pop == TARGET_ASCII).sum(axis=1)
        return match_count

    def select(self):
        fitness = self.get_fitness() + 1e-4     # add a small amount to avoid all zero fitness
        idx = np.random.choice(np.arange(self.pop_size), size=self.pop_size, replace=True, p=fitness/fitness.sum())
        return self.pop[idx]

    def crossover(self, parent, pop):
        if np.random.rand() < self.cross_rate:
            i_ = np.random.randint(0, self.pop_size, size=1)                        # select another individual from pop
            cross_points = np.random.randint(0, 2, self.DNA_size).astype(np.bool)   # choose crossover points
            parent[cross_points] = pop[i_, cross_points]                            # mating and produce one child
        return parent

    def mutate(self, child):
        for point in range(self.DNA_size):
            if np.random.rand() < self.mutate_rate:
                child[point] = np.random.randint(*self.DNA_bound)  # choose a random ASCII index
        return child

    def evolve(self):
        pop = self.select()
        pop_copy = pop.copy()
        for parent in pop:  # for every parent
            child = self.crossover(parent, pop_copy)
            child = self.mutate(child)
            parent[:] = child
        self.pop = pop

if __name__ == '__main__':
    ga = GA(DNA_size=DNA_SIZE, DNA_bound=ASCII_BOUND, cross_rate=CROSS_RATE,
            mutation_rate=MUTATION_RATE, pop_size=POP_SIZE)

    for generation in range(N_GENERATIONS):
        fitness = ga.get_fitness()
        best_DNA = ga.pop[np.argmax(fitness)]
        best_phrase = ga.translateDNA(best_DNA)
        print('Gen', generation, ': ', best_phrase)
        if best_phrase == TARGET_PHRASE:
            break
        ga.evolve()

{% endcodeblock %}
### 我的注释代码
{% codeblock %}

import numpy as np

TARGET_PHRASE = "i love you"        # 目标语句
POP_SIZE = 300                      # 种群数量
CROSS_RATE = 0.4                    # 交叉概率
MUTATION_RATE = 0.01                # 变异概率
N_GENERATIONS = 1000                # 循环多少代
DNA_SIZE = len(TARGET_PHRASE)       # DNA 长度
TARGET_ASCII = np.fromstring(TARGET_PHRASE,dtype=np.uint8)  # 将目标语句转化为 ASCII 中的数值
ASCII_BOUND = [32,126]              # ASCII 的取值范围


class GA(object):
    def __init__(self,DNA_size,DNA_bound,cross_rate,mutation_rate,pop_size):
        self.DNA_size = DNA_SIZE
        DNA_bound[1] += 1
        self.DNA_bound = DNA_bound
        self.cross_rate = cross_rate
        self.mutate_rate = mutation_rate
        self.pop_size = pop_size
        self.pop = np.random.randint(*DNA_bound,size=(pop_size,DNA_size)).astype(np.int8)

    def translateDNA(self,DNA):
        # 将 np 数据类型的 DNA 翻译成 python 的字符串数据类型
        return DNA.tostring().decode("ascii")

    def get_fitness(self):
        # 通过和原目标进行比对，算出具体的适应度，匹配度越高适应度越高
        # pop 是 300 个种群的 DNA
        # axis = 1 表示行相加
        match_count = (self.pop == TARGET_ASCII).sum(axis = 1)
        return match_count

    def select(self):
        # 让适应度不为 0，后面加几都无所谓，一般装逼代码都会加注释的代码
        # fitness = self.get_fitness() + 1e-4
        fitness = self.get_fitness() + 1
        # 用轮盘赌的方法来选择能够遗传性状的个体
        index = np.random.choice(np.arange(self.pop_size),size=self.pop_size,replace=True,p=fitness/fitness.sum())
        return self.pop[index]

    def crossover(self,parent,pop):
        # 交叉遗传
        if np.random.rand() < self.cross_rate:
            i = np.random.randint(0,self.pop_size,size=1)
            cross_points = np.random.randint(0,2,self.DNA_size).astype(np.bool)
            # 交叉遗传，这句代码的意思是在 cross_points 中，为 True 的下标变为 pop[i] 中的数字，如果为 False ,则保留 parent 的数字
            # 很有意思，这是 numpy 的特有代码形式，我在 numpy 的博客中，有小技巧一节对这个技巧有详细说明
            parent[cross_points] = pop[i,cross_points]
        return parent

    def mutate(self,child):
        # 变异
        # 循环一个 DNA 长度，如果小于变异概率，那么就对那个下标下的 ASCII 码随意在定义域内赋值
        for point in range(self.DNA_size):
            if np.random.rand() < self.mutate_rate:
                child[point] = np.random.randint(*self.DNA_bound)
        return child

    def evolve(self):
        pop = self.select()
        pop_copy = pop.copy()
        for parent in pop:
            child = self.crossover(parent,pop_copy)
            child = self.mutate(child)
            parent[:] = child
        self.pop = pop


if __name__ == '__main__':
    ga = GA(DNA_size=DNA_SIZE, DNA_bound=ASCII_BOUND, cross_rate=CROSS_RATE,mutation_rate=MUTATION_RATE, pop_size=POP_SIZE)

    for generation in range(N_GENERATIONS):
        fitness = ga.get_fitness()
        best_DNA = ga.pop[np.argmax(fitness)]
        best_phrase = ga.translateDNA(best_DNA)
        print('Gen', generation, ': ', best_phrase)
        if best_phrase == TARGET_PHRASE:
            break
        ga.evolve()

{% endcodeblock %}
## TSP 旅行商问题
对于这个问题，遗传算法只能找到相对比较优的路径，而不能 100% 找到最优路径。
{% img /images/machinelearningalgorithm/6_3.gif %}
### 原代码
{% codeblock %}

"""
Visualize Genetic Algorithm to find the shortest path for travel sales problem.
Visit my tutorial website for more: https://morvanzhou.github.io/tutorials/
"""
import matplotlib.pyplot as plt
import numpy as np

N_CITIES = 20  # DNA size
CROSS_RATE = 0.1
MUTATE_RATE = 0.02
POP_SIZE = 500
N_GENERATIONS = 500


class GA(object):
    def __init__(self, DNA_size, cross_rate, mutation_rate, pop_size, ):
        self.DNA_size = DNA_size
        self.cross_rate = cross_rate
        self.mutate_rate = mutation_rate
        self.pop_size = pop_size

        self.pop = np.vstack([np.random.permutation(DNA_size) for _ in range(pop_size)])

    def translateDNA(self, DNA, city_position):     # get cities' coord in order
        line_x = np.empty_like(DNA, dtype=np.float64)
        line_y = np.empty_like(DNA, dtype=np.float64)
        for i, d in enumerate(DNA):
            city_coord = city_position[d]
            line_x[i, :] = city_coord[:, 0]
            line_y[i, :] = city_coord[:, 1]
        return line_x, line_y

    def get_fitness(self, line_x, line_y):
        total_distance = np.empty((line_x.shape[0],), dtype=np.float64)
        for i, (xs, ys) in enumerate(zip(line_x, line_y)):
            total_distance[i] = np.sum(np.sqrt(np.square(np.diff(xs)) + np.square(np.diff(ys))))
        fitness = np.exp(self.DNA_size * 2 / total_distance)
        return fitness, total_distance

    def select(self, fitness):
        idx = np.random.choice(np.arange(self.pop_size), size=self.pop_size, replace=True, p=fitness / fitness.sum())
        return self.pop[idx]

    def crossover(self, parent, pop):
        if np.random.rand() < self.cross_rate:
            i_ = np.random.randint(0, self.pop_size, size=1)                        # select another individual from pop
            cross_points = np.random.randint(0, 2, self.DNA_size).astype(np.bool)   # choose crossover points
            keep_city = parent[~cross_points]                                       # find the city number
            swap_city = pop[i_, np.isin(pop[i_].ravel(), keep_city, invert=True)]
            parent[:] = np.concatenate((keep_city, swap_city))
        return parent

    def mutate(self, child):
        for point in range(self.DNA_size):
            if np.random.rand() < self.mutate_rate:
                swap_point = np.random.randint(0, self.DNA_size)
                swapA, swapB = child[point], child[swap_point]
                child[point], child[swap_point] = swapB, swapA
        return child

    def evolve(self, fitness):
        pop = self.select(fitness)
        pop_copy = pop.copy()
        for parent in pop:  # for every parent
            child = self.crossover(parent, pop_copy)
            child = self.mutate(child)
            parent[:] = child
        self.pop = pop


class TravelSalesPerson(object):
    def __init__(self, n_cities):
        self.city_position = np.random.rand(n_cities, 2)
        plt.ion()

    def plotting(self, lx, ly, total_d):
        plt.cla()
        plt.scatter(self.city_position[:, 0].T, self.city_position[:, 1].T, s=100, c='k')
        plt.plot(lx.T, ly.T, 'r-')
        plt.text(-0.05, -0.05, "Total distance=%.2f" % total_d, fontdict={'size': 20, 'color': 'red'})
        plt.xlim((-0.1, 1.1))
        plt.ylim((-0.1, 1.1))
        plt.pause(0.01)


ga = GA(DNA_size=N_CITIES, cross_rate=CROSS_RATE, mutation_rate=MUTATE_RATE, pop_size=POP_SIZE)

env = TravelSalesPerson(N_CITIES)
for generation in range(N_GENERATIONS):
    lx, ly = ga.translateDNA(ga.pop, env.city_position)
    fitness, total_distance = ga.get_fitness(lx, ly)
    ga.evolve(fitness)
    best_idx = np.argmax(fitness)
    print('Gen:', generation, '| best fit: %.2f' % fitness[best_idx],)

    env.plotting(lx[best_idx], ly[best_idx], total_distance[best_idx])

plt.ioff()
plt.show()

{% endcodeblock %}
### 添加详细注释后的代码
{% codeblock %}

import matplotlib.pyplot as plt
import numpy as np

N_CITIES = 20   # 城市的数量 也是 DNA 的数量
CROSS_RATE = 0.1
MUTATE_RATE = 0.02
POP_SIZE = 500
N_GENERATIONS = 500

class GA(object):
    def __init__(self,DNA_size,cross_rate,mutation_rate,pop_size):
        self.DNA_size = DNA_size
        self.cross_rate = cross_rate
        self.mutate_rate = mutation_rate
        self.pop_size = pop_size

        # 这一步就是先得到 np.arange(DNA_size) 然后打乱它,通过 for 循环生成 pop_size 个 打乱的数据
        # 值得注意的是，每一个打乱的数据都相互独立，也就是每一个打乱的内容都彼此不一样
        # DNA_size 就是城市数量，也就是上面定义的 N_CITIES
        self.pop = np.vstack([np.random.permutation(DNA_size) for i in range(pop_size)])

    # DNA 参数上面那个函数产生的 pop
    # pop 是随机 20 个数，并且随机排列， 20 个随机排列的数处于同一地位，也就是同一纬度
    # 并且后面又随机了 500 个种群，最后经过 vstack 的列排列，所以，最后应该是 500 * 20

    # city_position 就是城市的地址，纬度是 20 * 2
    # 这个方法虽然名为翻译 DNA ，其实和翻译 DNA 一点也不挂钩
    # 这个方法其实就是制造了很多种走城市的路径，是制造了一个种群
    def translateDNA(self,DNA,city_position):

        line_x = np.empty_like(DNA, dtype=np.float64)
        # line_x 的纬度是 500 20
        line_y = np.empty_like(DNA, dtype=np.float64)

        # i 就是下标
        # d 就是一组 DNA ，维度是 20 * 1
        # d 的其中一个示例 [15  3  8 14 12  1 17  7 16  4 18  6  2  9 11 10 19 13  0  5]
        for i, d in enumerate(DNA):
            # DNA 也是 500 * 20
            # city_coord 的维度是 20 * 2
            # 下面的这一句代码的意思是给 city_coord 赋 走的 city 路径
            # 因为 city_position 是 20 行.而 d 也只是随机的 20 个数
            # 所以只是把 city 的路径打乱付给 city__coord 而已
            # 下面是小代码示例：
            # import numpy as np
            # a = np.arange(1,13).reshape(6,2)
            # b = np.array([0,3,2,4,1,5])
            # for i,d in enumerate(a):
            #     c = a[b]
            #     print(c)
            #   [[ 1  2]
            #  [ 7  8]
            #  [ 5  6]
            #  [ 9 10]
            #  [ 3  4]
            #  [11 12]]
            # [[ 1  2]
            #  [ 7  8]
            #  [ 5  6]
            #  [ 9 10]
            #  [ 3  4]
            #  [11 12]]
            # [[ 1  2]
            #  [ 7  8]
            #  [ 5  6]
            #  [ 9 10]
            #  [ 3  4]
            #  [11 12]]
            # [[ 1  2]
            #  [ 7  8]
            #  [ 5  6]
            #  [ 9 10]
            #  [ 3  4]
            #  [11 12]]
            # [[ 1  2]
            #  [ 7  8]
            #  [ 5  6]
            #  [ 9 10]
            #  [ 3  4]
            #  [11 12]]
            # [[ 1  2]
            #  [ 7  8]
            #  [ 5  6]
            #  [ 9 10]
            #  [ 3  4]
            #  [11 12]]
            city_coord = city_position[d]
            # i 是从 0 到 499
            # line_x 和 line_y 是 500 * 20
            # city_coord 是 20 * 2
            # city_coord[:, 0] 是 20 * 1
            # 而 line_x[i, :] 也是 20 * 1
            line_x[i, :] = city_coord[:, 0]
            line_y[i, :] = city_coord[:, 1]
            # 最终我们得到 500 个随机城市的排序样本
            # 而 line_x 中装的是城市 x 的坐标
            # 而 line_y 中装的是城市 y 的坐标
        return line_x, line_y

    # 得到每一条路径的适应度
    def get_fitness(self,line_x,line_y):
        # 城市长度，记录了每一代最佳的城市路径
        total_distance = np.empty((line_x.shape[0],), dtype=np.float64)
        # 下面的循环就是计算每一个路径的长度，原理是勾股定理
        for i, (xs, ys) in enumerate(zip(line_x, line_y)):
            total_distance[i] = np.sum(np.sqrt(np.square(np.diff(xs)) + np.square(np.diff(ys))))
        # 我们都知道如果路程长度分别是 1.1 和 1.13 的话其实，区别是不大的
        # 所以，我们要有意识的的进行扩大，在这里我们用到指数函数 e 进行扩大
        fitness = np.exp(self.DNA_size * 2 / total_distance)
        return fitness, total_distance

    # 选择函数
    def select(self, fitness):
        # 用轮盘赌的方法选择祖先种群
        idx = np.random.choice(np.arange(self.pop_size), size=self.pop_size, replace=True, p=fitness / fitness.sum())
        return self.pop[idx]

    # 交叉遗传
    # parent 是  20 * 1
    # pop 是 500 * 20
    def crossover(self, parent, pop):
        if np.random.rand() < self.cross_rate:
            # 随机一个祖先
            i_ = np.random.randint(0, self.pop_size, size=1)
            # 随机出交叉点
            cross_points = np.random.randint(0, 2, self.DNA_size).astype(np.bool)
            # 这个路径问题不能像其他的交叉函数那样，随意交叉
            # 两个祖先路径的排列城市的顺序不一样，所以，可能会出现，祖先 A 的以 a 城市在下标 1 ，祖先 B 的 a 城市在下标 4 上
            # 两个祖先交叉后，可能会出现两个 a ，所以，为了防止出现这种情况，我们需要做一些改变
            # 假设祖先 A 的城市序列分别是 [ 3,4 1,2,6,5]
            # 选取城市为 [0,1,1,0,0,1] 即 0 代表 False 1 代表 True
            # 这样，祖先 A 的城市选择是 [4，2，5] 因为这个顺序一定是祖先 A 的顺序，所以，我们把这个顺序作为孩子的开头
            # 这样城市就还剩下 [3,1,6] 没有选，如果祖先 B 的城市顺序是 [1,6,2,5,3,4]
            # 那么在祖先 B 中没有排列的城市顺序是 [1,6,3]
            # 将这个顺序和上面的序列进行组合，最后得到
            # [4,2,5,1,6,3] 这就是最后的交叉结果

            # 因为 isin 这个函数存在反而会取 False 所以，我们要将下面的取反
            keep_city = parent[~cross_points]
            # 选出另一部分的序列
            swap_city = pop[i_, np.isin(pop[i_].ravel(), keep_city, invert=True)]
            # 序列组合
            parent[:] = np.concatenate((keep_city, swap_city))
        return parent

        # 变异
        # 变异就是随机选取一个个体，随机交换两个城市的位置
    def mutate(self, child):
        for point in range(self.DNA_size):
            if np.random.rand() < self.mutate_rate:
                swap_point = np.random.randint(0, self.DNA_size)
                swapA, swapB = child[point], child[swap_point]
                child[point], child[swap_point] = swapB, swapA
        return child

        # 进化
    def evolve(self, fitness):
        pop = self.select(fitness)
        pop_copy = pop.copy()
        for parent in pop:  # for every parent
            child = self.crossover(parent, pop_copy)
            child = self.mutate(child)
            parent[:] = child
        self.pop = pop

class TravelSalesPerson(object):
    def __init__(self,n_cities):
        # 随机城市的地址
        self.city_position = np.random.rand(n_cities,2)

    def plotting(self,lx,ly,total_d):
        plt.cla()
        plt.scatter(self.city_position[:,0].T,self.city_position[:,1].T,s = 100,c = 'k')
        plt.plot(lx.T,ly.T,'r-')
        plt.text(-0.05, -0.05, "Total distance=%.2f" % total_d, fontdict={'size': 20, 'color': 'red'})
        plt.xlim((-0.1, 1.1))
        plt.ylim((-0.1, 1.1))
        plt.pause(0.01)


ga = GA(DNA_size=N_CITIES, cross_rate=CROSS_RATE, mutation_rate=MUTATE_RATE, pop_size=POP_SIZE)

env = TravelSalesPerson(N_CITIES)
for generation in range(N_GENERATIONS):
    lx, ly = ga.translateDNA(ga.pop, env.city_position)
    fitness, total_distance = ga.get_fitness(lx, ly)
    ga.evolve(fitness)
    best_idx = np.argmax(fitness)
    print('Gen:', generation, '| best fit: %.2f' % fitness[best_idx],)

    env.plotting(lx[best_idx], ly[best_idx], total_distance[best_idx])

plt.ioff()
plt.show()

{% endcodeblock %}
