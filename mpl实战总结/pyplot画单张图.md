### plot画单张图用法----以最简单的折线图为例(内容：pyplot教程)

#### step1 画出基本图

与matlab一样，可以在一张图中绘制多条线。常用形式：

`plot(x1,y1,args,x2,y2,args,...)`

#### step2 参数控制

##### 线条参数控制

法一：直接在plot的args中控制，好像一次画多条线时只能控制线型和颜色

```python
plt.plot(x,x,'b-',x,x**2,'g^',x,x**3,'rs')
```

法二：函数式方法（类似于matlab）

```python
line1,line2,line3 = plt.plot(x,x,'b-',x,x**2,'g^',x,x**3,'rs')
plt.setp(line1,'linewidth',2)
plt.setp(line2,'linewidth',2)
plt.setp(line3,'linewidth',2)
```

法三：oop方法

```python
line1,line2,line3 = plt.plot(x,x,'b-',x,x**2,'g^',x,x**3,'rs')
line1.set_linewidth(5)
```

常用属性键值对列出：

```python
color	                任何 matplotlib 颜色
linestyle               [ '-' / '--' / '-.' / ':' / 'steps' / ...]
linewidth               以点为单位的浮点值
alpha	                浮点值
marker                  数据对应的点的形状
```

##### figure设置

```python
plt.figure()  # 不仅可以设置编号，还可以设置边框，背景色等
```

##### 坐标轴参数控制

先进入当前的figure或者subplot，再使用plt中的相关设置函数

```python
# 省略掉的之前的代码已经建立了一张figure
plt.figure(1)
plt.subplot(2,1,1)
plt.title('figure1')
plt.xlabel('x') 
plt.ylabel('y')
plt.axis([-1,6,-1,1])
```

常用设置

```python
plt.axis([a,b,c,d])  # 坐标轴刻度范围,下面两个是分别设置
plt.xlim(a,b)
plt.ylim(c,d)
plt.xlabel()
plt.ylabel()
plt.xticks(locs,labels:list)  # locs控制数值间隔范围，labels控制显示的标签，y同理
plt.rcParams['xtick.direction'] = 'in'  # 设置刻度线显示方向，y同理
plt.tick_params()  # 设置是否显示刻度线
plt.grid()  # 网格线,也可以在后面加键值对设置color，linewidth等
plt.xscale('log'/'symlog')  # 设置对数坐标轴，y同理
```

##### 标签设置

```python
plt.text(x,y,str,fontdict,args)  # str可以用格式化输出，args常用的是fontsize(一般直接用fontdict囊括所有设置)
```

##### 标题设置

```python
plt.title(str, fontdict, loc:str, pad:float, args)  # loc设置标题是居中还是左对齐等，pad控制标题距离图的距离
```

##### 设置注释

```python
plt.annotate()
```



#### step3 展示

使用`plt.show()`作为画图的结尾

