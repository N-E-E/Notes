## 参考

COPT规划求解器官方使用手册

## 预备知识

线性规划、python基础知识

杉数求解器的python接口与gurobi基本一致，已经用过gurobi的朋友可以直接看官方手册

## 入门案例

这里使用的是官方手册上的案例：

最大化：
$$
max(1.2x + 1.8y + 2.1z)
$$


$$
约束:
$$
    1.5x + 1.2y + 1.8z ≤ 2.6\\
    0.8x + 0.6y + 0.9z ≥ 1.2
$$
变量范围：
$$
0.1 ≤ x ≤ 0.6\\
0.2 ≤ y ≤ 1.5\\
0.3 ≤ z ≤ 2.8
$$


先放上官方手册上的代码，后续再讲解：

```python
import coptpy as cpt

# 创建一个求解环境
env = cpt.Envr()

# 创建模型
model = env.createModel("lp_ex1")

# 添加变量
x = model.addVar(lb=0.1, ub=0.6, name="x")
y = model.addVar(lb=0.2, ub=1.5, name="y")
z = model.addVar(lb=0.3, ub=2.8, name="z")

# 添加约束条件
model.addConstr(1.5*x + 1.2*y + 1.8*z <= 2.6)
model.addConstr(0.8*x + 0.6*y + 0.9*z >= 1.2)

# 设置目标函数
model.setObjective(1.2*x + 1.8*y + 2.1*z, sense=cpt.COPT.MAXIMIZE)

# 设置求解器参数
model.setParam(cpt.COPT.Param.TimeLimit, 10.0)

# 求解
model.solve()

# 结果分析
if model.status == cpt.COPT.OPTIMAL:
    print("Objective value: {}".format(model.objval))
    allvars = model.getVars()
    print("Variable solution:")
    for var in allvars:
        print(" x[{0}]: {1}".format(var.index, var.x))
    print("Variable basis status:")
    for var in allvars:
        print(" x[{0}]: {1}".format(var.index, var.basis))
```

