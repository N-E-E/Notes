## 安装

1. 安装pyomo
2. 复制杉数求解器文件夹下copt50/lib/pyomo/copt_pyomo.py到anaconda/Lib/site-packages下
3. 使用pyomo求解时导入包、设置求解器

- ```python
  from copt_pyomo import *
  
  solver = SolverFactory('copt_direct')

## 入门级别使用--解决简单问题

[python运筹优化（二）：Pyomo使用简介 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/125179633)

下面这个只要看例一

[python运筹优化（五）：多变量规划问题pyomo实践 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/134950216)

## 更优秀的求解方法

