## 参考

geatpy官网api

## 单目标优化流程

1. 编写问题类基本属性
2. 编写目标函数。实际上是通过计算aimFunc计算种群的目标函数值和种群的违反约束程度
3. （可选）编写计算目标参考值的函数（后面会调用这个函数获取参考值），一般暴力迭代计算几个目标函数值(数量可以自己选择)，返回一个列向量
4. 下面开始编写main函数
5. 实例化problem类。后面要用到前面初始化的一些problem的属性
6. 编写译码矩阵，形参需要编码类型、problem类中的变量类型，变量范围，变量边界是否取到（后两个在problem实例化时会自动组合前面输入的生成）
7. 生成种群实例，形参需要编码类型，译码矩阵，种群中个体数
8. 利用算法模板实例化算法，形参需要问题实例，种群实例
9. 设置算法实例的统一参数。(logtras:打印日志周期  verbose:是否输出日志信息   drawing:是否画图，一般选1)
10. 设置对应算法的参数（见最后）
11. 启动算法，输出相关信息(各种属性见后)

### 案例

```python
# 模板：DE/best/1/L
import numpy as np
import geatpy as ea

# 编写问题类
class MyProb(ea.Problem):
    def __init__(self):

        ea.Problem.__init__(self,
            name = 'MyProb',
            M = 1,  # 目标维数
            maxormins = [-1],
            Dim = 3,
            varTypes = [0] * 3,
            lb = [0, 0, 0],
            ub = [1, 1, 2],
            lbin = [1, 1, 0],
            ubin = [1, 1, 0]
        )

    def aimFunc(self, pop):
        Vars = pop.Phen  # 获取决策变量矩阵
        x1 = Vars[:, [0]]
        x2 = Vars[:, [1]]
        x3 = Vars[:, [2]]
        # 计算目标函数
        pop.ObjV = 4*x1 + 2*x2 + x3
        # 违反约束程度
        pop.CV = np.hstack([2*x1 + x2 - 1,
                            x1 + 2*x3 - 2,
                            np.abs(x1 + x2 + x3 -1)])

if __name__ == '__main__':
    problem = MyProb()
    Encoding = 'RI'
    Nind = 50  # 种群规模
    Field = ea.crtfld(Encoding, problem.varTypes, problem.ranges, problem.borders)  # 译码器
    population = ea.Population(Encoding, Field, Nind)  # 生成种群
    # 通过算法模板实例化算法
    myAlg = ea.soea_DE_best_1_L_templet(problem, population)
    # alg类统一参数设置
    myAlg.logTras = 1 # 打印日志间隔代数
    myAlg.verbose = True
    myAlg.drawing = 1
    myAlg.MAXGEN = 1000
    # 差分算法参数
    myAlg.mutOper.F = 0.5  # 差分进化算法的参数F，默认也是0.5
    myAlg.recOper.XOVR = 0.7  # 交叉概率，默认为0.5
    # 调用算法
    [BestInd, final_pop] = myAlg.run()  # BestInd是以往所有种群里面最好的个体。种群有的属性它也有(可以看作只有一个个体的种群)
    BestInd.save()
    print('评价次数：%s' % myAlg.evalsNum)
    print('时间已过 %s 秒' % myAlg.passTime)
    if BestInd.sizes != 0:
        print('最优的目标函数值为：%s' % BestInd.ObjV[0][0])
        print('最优的控制变量值为：')
        for i in range(BestInd.Phen.shape[1]):
            print(BestInd.Phen[0, i])
    else:
        print('没找到可行解。')
```

### Algorithm实例常用属性

```python
myAlg.logtras  # 打印日志周期  
myAlg.verbose  # 是否输出日志信息   
myAlg.drawing  # 是否画图，一般选1
myAlg.MAXGEN  # 最大代数

myAlg.evalsNum  # 评价次数
myAlg.passTime  # 已用时间（单位：秒）
```

## 单目标优化算法解析

### 差分进化DE/best/1/L算法

1. 基向量的选择方式：采用精英复制选择
2. 将当前种群和变异个体合并，采用指数交叉方法得到试验种群
3. 在当前种群和实验种群之间采用一对一生存者选择方法得到新一代种群

相关参数：

```python
alg.MAXGEN  # 最大代数
alg.mutOper.F  # 差分变异过程中的参数F(0<f<2)
alg.recOper.XOVR  # 交叉概率，0~1
```

