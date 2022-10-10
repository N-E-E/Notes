# matlab绘图快速入门

### 一个绘图风格--跟踪当前

在绘图之前，先进入某个图窗或轴域(matlab会自动跟踪离当前绘图命令最近的一个fig或者ax，这个叫 ’当前‘ )，然后再直接用函数plot：

```matlab
f1 = figure;  % f1是一个对象，可以设置属性
f2 = figure;
plot([1 2 3],[2 4 6]);  % 当前处于f2
figure(f1);  % 进入f1
scatter((1:20),rand(1,20));
```

紧接着，我们可以继续创建axes。注意，它也具有当前性,因此先进入f2

```matlab
figure(f2)
ax1 = axes('Position',[0.65 0.65 0.28 0.28]);  % ax1是对象，可以设置属性
ax2 = axes('Position',[0.1 0.65 0.28 0.28]);  % 参数依次是左下角的坐标(相对于figure)和宽高

axes(ax1)  % 进入ax1域
x = linspace(0,10);
y = sin(x);
plot(x,y)
```

最后是用较为底层的辅助方法画图，它会返回图形对象，可以用面向对象的方法修改(似乎不是matlab的本意)，也可以直接在plot中设置键值对参数(推荐)

```matlab
x = linspace(-2*pi,2*pi);
y1 = sin(x);
y2 = cos(x);
p = plot(x,y1,x,y2);
p(1).LineWidth = 2;
p(2).Marker = '*';
```

执行上述代码结果如下：

![image-20220603235655207](C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220603235655207.png)

### 面向对象：资料比较少，只能到官方文档学习

[(30条消息) matlab关于画图的深层理解（图形对象）_勇敢的小刀的博客-CSDN博客_matlab绘图原理](https://blog.csdn.net/weixin_44871341/article/details/107593633)

[Matlab画图小指南 - icydengyw - 博客园 (cnblogs.com)](https://www.cnblogs.com/icydengyw/p/12606255.html)
