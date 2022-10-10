## 参考

geatpy官网api

## 多目标优化流程

1. 编写问题类基本属性
2. 编写目标函数。实际上是通过计算aimFunc计算种群的目标函数值和种群的违反约束程度
3. 编写计算目标参考值的函数（后面会调用这个函数获取参考值），一般暴力迭代计算n组目标函数值(数量可以自己选择)，返回n*m的矩阵(m代表目标函数个数)
4. 下面开始编写main函数
5. 实例化problem类。要用到前面初始化的一些problem的属性
6. 编写译码矩阵，形参需要编码类型、problem类中的变量类型，变量范围，变量边界是否取到（后两个在problem实例化时会自动组合前面输入的生成）
7. 生成种群实例，形参需要编码类型，译码矩阵，种群中个体数
8. 利用算法模板实例化算法，形参需要问题实例，种群实例
9. 设置算法实例的统一参数。(logtras:打印日志周期  verbose:是否输出日志信息   drawing:是否画图，一般选1   MAXGEN:最大代数)
10. 设置对应算法的参数（见最后）
11. 启动算法，输出相关信息(各种属性见后)

### 案例

```python
import numpy as np
import geatpy as ea

class MyProb(ea.Problem):
    def __init__(self):
        ea.Problem.__init__(
            self,
            name='my_prob',
            M = 2,
            maxormins=[1,1],
            Dim=2,
            varTypes=[0,0],
            lb=[0,0],
            ub=[5,3],
            lbin=[1,1],
            ubin=[1,1]
        )

    def aimFunc(self, pop):
        Vars = pop.Phen
        x1 = Vars[:,[0]]
        x2 = Vars[:,[1]]
        pop.CV = np.hstack([(x1-5)**2 + x2**2 - 25,
                            7.7 - (x1-8)**2 - (x2-3)**2])
        pop.ObjV = np.hstack([4*x1**2 + 4*x2**2,
                              4*(x1-5)**2 + 4*(x2-5)**2])

    def calReferObjV(self):
        N = 10000
        x1 = np.linspace(0,5,N)
        x2 = np.linspace(0,3,N)
        return np.vstack([4*x1**2 + 4*x2**2,
                          4*(x1-5)**2 + 4*(x2-5)**2]).T


if __name__ == '__main__':
    # 建立问题
    myProb = MyProb()
    # 实例化种群
    Field = ea.crtfld(Encoding='RI', varTypes=myProb.varTypes, ranges=myProb.ranges, borders=myProb.borders)
    population = ea.Population(Encoding='RI', Field=Field, NIND=100)
    # 选择算法模板并设置相关参数
    myAlg = ea.moea_NSGA2_templet(problem=myProb, population=population)
    # 算法类统一参数
    myAlg.MAXGEN = 200
    myAlg.logTras = 1
    myAlg.drawing = 1
    myAlg.verbose = False
    # NSAG-2参数，可以自己设置也可以使用默认的
    myAlg.mutOper.Pm = 0.2  # 默认为1
    myAlg.recOper.XOVR = 0.9  # 默认为1
    # 运行
    [NDSet, population] = myAlg.run()  # NDset是整个程序中非支配个体组成的种群，有population类所有属性
    # 输出结果
    print(f'程序运行时间：{myAlg.passTime}s')
    if NDSet.sizes == 0:
        print(f'没有找到可行解！')
    else:
        NDSet.save()
    if myAlg.log is not None and NDSet.sizes != 0:
        print('GD', myAlg.log['gd'][-1])
        print('IGD', myAlg.log['igd'][-1])
        print('HV', myAlg.log['hv'][-1])
        print('Spacing', myAlg.log['spacing'][-1])
    metricName = [['igd'], ['hv']]
    Metrics = np.array([myAlg.log[metricName[i][0]] for i in
                        range(len(metricName))]).T
    # 绘制指标追踪分析图
    ea.trcplot(Metrics, labels=metricName, titles=metricName)
```

### Algorithm父类常用属性

```python
myAlg.logtras  # 打印日志周期  
myAlg.verbose  # 是否输出日志信息   
myAlg.drawing  # 是否画图，一般选1
myAlg.MAXGEN  # 最大代数
myAlg.evalsNum  # 评价次数
myAlg.passTime  # 已用时间（单位：秒）
```

## 多目标优化算法解析

### NSGA-2算法

[遗传算法之NSGA-Ⅱ原理分析和代码解读 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/144807879)

交叉变异算子的含义可以看这个：[(34条消息) NSGA-II算法介绍_不一样的烟火呦的博客-CSDN博客_nsga-ii](https://blog.csdn.net/q15615725386/article/details/119521206#:~:text=标准的NSGA-II算法使用模拟二进制交叉算子，第 k %2B1代个体的计算公式如下 [44%2C 45]： 式中， p 1%2C,p 2%2C k 是被选中的第 k 代个体； β qi是均匀分布因子%2C其计算方式如下：)

相关参数：

```python
alg.mutOper.Pm  # 变异概率
alg.recOper.XOVR  # 交叉概率，0~1

alg.log
```

### NSGA-3算法

[遗传算法之NSGA-III原理分析和代码解读 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146618031)

更详细的讲解：[(34条消息) NSGA3算法及其MATLAB版本实现_sunny落花生的博客-CSDN博客](https://blog.csdn.net/wayjj/article/details/78954506)

python实战（有geatpy实现）：[Python进化算法之多目标优化与代码实战 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/138127772)

