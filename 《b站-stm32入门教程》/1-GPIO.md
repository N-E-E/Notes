# GPIO

## 输入

<img src="C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220923133939189.png" alt="image-20220923133939189" style="zoom:50%;" />

- 寄存器是32位的，但是只用到低16位，因为只有16个引脚

### 内部具体结构

<img src="C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220923134028304.png" alt="image-20220923134028304" style="zoom:50%;" />

#### 输入状态：

上拉输入：默认高电平输入

下拉输入：默认低电平输入

浮空状态：上下开关都断开

#### 输出状态

推挽输出：寄存器输出的高低电平有较强的驱动能力，寄存器输出啥引脚就输出啥(强推输出模式)

开漏输出：只有Nmos在工作，只有低电平有驱动模式，寄存器输出的高电平被阻断

关闭输出：两个mos均关闭，即输入状态

#### 库封装好的八种模式

<img src="C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220923153614722.png" alt="image-20220923153614722" style="zoom:50%;" />

## 输出

### 传感器

<img src="C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220925230932654.png" alt="image-20220925230932654" style="zoom:50%;" />

- 上面是传感器的电路图

- 电容在经典分压电路中的滤波作用（保持电流稳定）
- 数字电压的产生：两个分压电路通过运算放大器得到数字电压

<img src="C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220925231442647.png" alt="image-20220925231442647" style="zoom:50%;" />

- 开关的常见电路
- 下面两种是下拉模式，不是很常见
- 左边两种要求stm内部是上拉模式

### c语言常见数据类型

<img src="C:\Users\Mr.K\AppData\Roaming\Typora\typora-user-images\image-20220925232028537.png" alt="image-20220925232028537" style="zoom:50%;" />

- 类型声明推荐uint8_t这种

### 代码实现

- 模块化思想

- 条件编译

- 上拉输入模式

  ```c
  // 初始化按键
      GPIO_InitTypeDef GPIO_InitStruct;
      // 上拉输入模式，没有输入就是默认输入了高电平,按下按键变成低电平
      GPIO_InitStruct.GPIO_Mode = GPIO_Mode_IPU;
      GPIO_InitStruct.GPIO_Pin = GPIO_Pin_6 | GPIO_Pin_4;
      GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
      GPIO_Init(GPIOB, &GPIO_InitStruct);
  ```

- 四个读取输出电平函数

  ```c
  GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_6);  // 读取单个引脚输入
  GPIO_ReadInputData(GPIOB, GPIO_Pin_6);  // 读取寄存器所有输入
  
  GPIO_ReadOutputDataBit(GPIOA, GPIO_Pin_2);  // 读取单个引脚输出
  GPIO_ReadOutputData(GPIOA);  // 读取寄存器所有输出
  ```

  