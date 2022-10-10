# 常用方法api手册

## Envr类

```python
# 创建环境和模型
env = Envr()
model = env.createModel(name='')
```

## Model类属性和方法

### 基本属性

```python
model.status  # 解的状态
model.objval  # 目标函数值
```

### 添加单个变量

```python
x = m.addVar(lb, ub, vtype, name='')
# lb/ub是上下限，vtype是变量类型，name是变量名
```

### 添加多维变量

原型：

```python
# 添加一组变量到模型中，并返回一个 tupledict类 对象，其键为变量的下标，值为相应的 Var类 对象
model.addVars(*indices, lb=0.0, ub=COPT.INFINITY, obj=0.0, vtype=COPT.CONTINUOUS, nameprefix="")  # nameprefix是变量名
```

根据*indices可以有几种不同的添加形式

##### 形式一

```python
# 添加三维整数变量 x，共计 6 个变量
x = m.addVars(2, 3, vtype=COPT.INTEGER)  # x00, x01, x02, x10,...
```

根据所学的tupledict结构，我们来看看x究竟是什么：

```python
x = model.addVars(2, 3, vtype=cpt.COPT.INTEGER, nameprefix='x')

print(x.select())
====================================
[<coptpy.Var: x(0,0)>, <coptpy.Var: x(0,1)>, <coptpy.Var: x(0,2)>, <coptpy.Var: x(1,0)>, <coptpy.Var: x(1,1)>, <coptpy.Var: x(1,2)>]

for i in range(2):
    for j in range(3):
        print(x[i,j].name, end='  ')
====================================
x(0,0)  x(0,1)  x(0,2)  x(1,0)  x(1,1)  x(1,2)
```

事实上，x就是一个tupledict，x的键是变量下标，x的值就是变量(Var)类。

##### 形式二

```python
# 可以设置非连续下标
t = cpt.tuplelist([(0, 1), (1, 2)])
x =  model.addVars(t, nameprefix="t")

print(x.select())
==================================
[<coptpy.Var: t(0,1)>, <coptpy.Var: t(1,2)>]
```

##### 形式三

```python
# *indices可以是多个列表，生成的下标就是这几个列表的笛卡尔积
t = ['a', 'b']
x =  model.addVars(t, range(2), range(3))

print(x.select())
===============================================
[<coptpy.Var: C(a,0,0)>, <coptpy.Var: C(a,0,1)>, <coptpy.Var: C(a,0,2)>, <coptpy.Var: C(a,1,0)>, <coptpy.Var: C(a,1,1)>, <coptpy.Var: C(a,1,2)>, <coptpy.Var: C(b,0,0)>, <coptpy.Var: C(b,0,1)>, <coptpy.Var: C(b,0,2)>, <coptpy.Var: C(b,1,0)>, <coptpy.Var: C(b,1,1)>, <coptpy.Var: C(b,1,2)>]
```

上面的例子还表明，下标也可以是str类型。

### 添加单个约束

```python
m.addConstr(lhs, sense=None, rhs=None, name="")
# 若添加线性约束，则参数 lhs 可取值为 Var类 对象、LinExpr类 对象或 ConstrBuilder类 对象；若添加半定约束，则参数 lhs 可取值为 PsdExpr类 对象或PsdConstrBuilder类 对象；若添加 Indicator 约束，则参数 lhs为GenConstrBuilder类 对象，并忽略其它参数。
```

### 添加多个约束

```python
# 添加 10 个线性约束，每个约束形如：x[0] + y[0] >= 2.0
m.addConstrs(x[i] + y[i] >= 2.0 for i in range(10))
```

### 添加indicator约束

```python
addGenConstrIndicator(binvar, binval, lhs, sense=None, rhs=None)
# 添加一个 Indicator 约束，当 x 为真时，线性约束 y + 2*z >= 3 成立
m.addGenConstrIndicator(x, True, y + 2*z >= 3)
```

### 设置目标

```python
m.setObjective(expr, sense=None)
```

### 获取全部变量

```python
# 获取模型中的全部变量,返回一个 VarArray类 对象
vars = m.getVars()
```

### 获取线性规划分析

```python
values, slacks, duals, redcosts = m.getLpSolution()
# 获取线性规划模型的变量取值、松弛变量取值、对偶变量取值和变量的Reducedcost，返回一个四元元组对象，元组中每个元素为一个列表对象。
```

### 获取模型信息

```python
model.getInfo(infoname, args)

infoname:待获取信息名。
args:待获取信息的变量或者约束

若参数 args 为 Var类 对象或 Constraint类 对象，则返回指定变量或约束的信息值常数；若参数 args 为列表、VarArray类 对象或 ConstrArray类 对象，则返回指定变量或约束的信息值组成的一个列表对象；若参数 args 为字典或 tupledict类 对象，则返回键为指定变量或约束的下标，值为指定变量或约束的信息值组成的一
个 tupledict类 对象


### 复制模型

```python
# 创建模型的深拷贝
mcopy = m.clone()
```

## Var类

```python
# 属性
var.x  # 变量取值
var.Value  # 同上
var.name  # 变量名
var.Slack  # 松弛变量取值
var.Dual  # 对偶变量取值
var.LB  # 变量上界
var.UB

# 方法
varname = v.getName()  # 获取变量 v 的名字
v.setName('')  # 设置变量 v 的名称
x.remove()  # 删除变量 x
```

## VarArray类

```python
# 方法
var = vararr.getVar(idx)  # 根据变量在 VarArray类 对象中的下标获取相应的变量，返回一个 Var类 对象。

varall = vararr.getAll()  # 获取 VarArray类 对象中的的全部变量，返回一个列表对象。

arrsize = vararr.getSize()  # 获取 vararr 中变量的个数
```

## 重要数据结构

### multidict

```python
multidict(data)  # 将输入的字典对象拆分为键与多个字典对象并返回
```

eg

```python
keys, dict1, dict2 = multidict({
"hello": [0, 1],
"world": [2, 3]})

### output ### 
keys: ['hello', 'world']
dict1: {'hello':0, 'world':2}
```


### tupledict

注意，tupledict和下面的tuplelist分别是dict和list的子类，因此都分别继承了这两者的方法

首先创建一个tupledict

```python
d = tupledict([((1,1),'a'), ((1,2),'b'),((2,1),'c'),((2,2),'d')])
```

可以把d的结构看成这样：[((1,1),'a'), ((1,2),'b'),((2,1),'c'),((2,2),'d')]

也就是说，对于multidict中每个元素(A, B), A是键，B是值。

可以使用下标进行访问：

```python
d = cpt.tupledict([((1,1),'a'), ((1,2),'b'),((2,1),'c'),((2,2),'d')])
for i in range(1, 3):
    for j in range(1, 3):
        print(d[i,j], end=' ')
====================================
a b c d
```

#### select方法

```python
根据指定的模式筛选得到符合条件的项，返回/包含所有符合条件的键/对应的/值/的列表
tupledict.select(pattern)  # pattern:匹配模式，*代表通配符
```

注意，pattern匹配的是键！

eg.

```python
d = cpt.tupledict([((1,1),'a'), ((1,2),'b'),((2,1),'c'),((2,2),'d')])

print(d.select(2,'*'))
=====================================
['c', 'd']

print(d.select(1,2))
=====================================
['b']

print(d.select())
=====================================
['a', 'b', 'c', 'd']
```

#### sum方法

```python
根据指定的模式筛选累加项，返回一个 LinExpr类 对象
expr = tupledict.sum(pattern)
```

这里的pattern用法和select方法完全一样，直接上例子：

```python
d = cpt.tupledict([((1,1),1), ((1,2),2),((2,1),3),((2,2),4)])
print(d.sum('*', 2))
================================
6.0
```

#### prod方法

```python
根据指定的模式筛选，并与乘积系数累乘项，返回一个 LinExpr类 对象。
expr = d.prod(coeff, pattern)  # coef是字典或tupledict类型，要求键和d对应。pattern含义同上
```

```python
d = cpt.tupledict([((1,1),1), ((1,2),2),((2,1),3),((2,2),4)])
coef = cpt.tupledict([((1,1),2), ((1,2),2),((2,1),3),((2,2),3)])
print(d.prod(coef, '*', 2))  # 2*2 + 3*4
========================================
16.0
```

显然，prod就是在做点积。

### tuplelist

```python
都是元组的列表。可以直接用索引访问
tl1 = tuplelist([(0, 1), (1, 2)])
tl2 = tuplelist([('a', 'b'), ('b', 'c')])

print(tl2)
===============================
[(0, 1), (1, 2)]
```

#### add方法

```python
# 添加列表成员，相当于append
tl.add((2, 3))
```

#### select方法

思想同tupledict

```python
tl = cpt.tuplelist([(0, 1), (1, 2)])
print(tl.select(0,'*'))
====================================
[(0, 1)]
```







