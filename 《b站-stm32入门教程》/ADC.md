# 理论

## 简介

![](D:\notes\《b站-stm32入门教程》\pic\adc1.png)

## ADC原理

![](D:\notes\《b站-stm32入门教程》\pic\adc2.png)

- EOC：转换完成信号
- 地址锁存和译码：用于选择通道
- DAC：根据输入的值输出模拟电压

## stm中的adc

![](D:\notes\《b站-stm32入门教程》\pic\adc3.png)

- 注入通道和规则通道是两种模式，规则通道要配合DMA(数据转运器使用)

- 触发ADC转换的信号可以是TIM和EXTI

- ADC预分频器只能选择6/8分频来满足最大14mhz的要求

## adc结构图

![](D:\notes\《b站-stm32入门教程》\pic\adc4.png)

## adc对应引脚和通道图

![](D:\notes\《b站-stm32入门教程》\pic\adc5.png)

## ADC转换模式

连续/非连续转换，扫描/非扫描四种模式

## 数据对齐

![](D:\notes\《b站-stm32入门教程》\pic\adc6.png)

- 左对齐的作用是修改分辨率(舍弃某几位)

## 转换时间、校准

/

