---
title: CloudSim能耗计算模型
author: 不二
date: 2019-07-12 09:25:00
mathjax: true
tags: 
- CloudSim
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/云计算/cloudsim1.png
categories: 云计算
---

## CloudSim能耗计算模型的不同

### 一：CloudSim能耗计算模型综述

CloudSim定义了一个能耗计算模型接口类`PowerModel`，分别有5个子类继承，这个五个子类分别为：

1. ：`PowerModelLinear`，以线性增长的方式计算能耗。
2. ：`PowerModelSquare`，以平方增长的方式计算能耗。
3. ：`PowerModelCubic`，以立方增长的方式计算能耗。
4. ：`PowerModelSqrt`，以平方根增长的方式计算能耗。
5. ：`PowerModelSpecPower`，以基于SPECpower benchmark计算能耗。


### 二：CloudSim能耗计算模型分析

​	2.1：`PowerModelLinear`类，其中定义了三个属性：

​		(1)`maxPower`：maxpower定义了最大可以消耗的能耗。

​		(2)`constant`：constant可以反应每个被使用的资源的能耗消耗。

​		(3)`staticPower`：staticpower表示机器不运行任何程序时候的静态能耗。

​	`PowerModelLinear`是以线性增长的方式计算能耗，我们可以用一个公式表达，设最后计算的power为y，constant为a，staticpower为b，(utlization*100)为x,其中constant=(maxPower - StaticPower) /100)得出：

​	y=staticpower+constant*(utlization*100)

​	抽象可以得出:y=ax+b,可以得出PowerModelLinear类是以线性增长的方式计算能耗。

​	2.2：`PowerModelSquare`类，其中定义了三个属性：

​		(1)`maxPower`：maxpower定义了最大可以消耗的能耗。

​		(2)`constant`：constant可以反应每个被使用的资源的能耗消耗。

​		(3)`staticPower`：staticpower表示机器不运行任何程序时候的静态能耗。

​	`PowerModelSquare`是以平方增长的方式计算能耗，我们可以用一个公式表达，设最后计算的power为y，constant为a，staticpower为b，(utlization*100)为x,其中constant=(maxPower - StaticPower) /100²)得出：

​	y=staticpower+constant*(utlization*100)²

​	抽象可以得出:y=ax²+b,可以得出PowerModelSquare类是以平方增长的方式计算能耗。

 	2.3：`PowerModelCubic`类，其中定义了三个属性：

​		(1)`maxPower`：maxpower定义了最大可以消耗的能耗。

​		(2)`constant`：constant可以反应每个被使用的资源的能耗消耗。

​		(3)`staticPower`：staticpower表示机器不运行任何程序时候的静态能耗消耗。

​	`PowerModelCubic`是以立方增长的方式计算能耗，我们可以用一个公式表达，设最后计算的power为y，constant为a，staticpower为b，(utlization*100)为x,其中constant=(maxPower - StaticPower) /100³)得出：

​	y=staticpower+constant*(utlization*100)³

​	抽象可以得出:y=ax³+b,可以得出PowerModelLinear类是以立方增长的方式计算能耗。

 	2.4：`PowerModelSqrt`类，其中定义了三个属性：

​		(1)`maxPower`：maxpower定义了最大可以消耗的能耗。

​		(2)`constant`：constant可以反应每个被使用的资源的能耗消耗。

​		(3)`staticPower`：staticpower表示机器不运行任何程序时候的静态能耗消耗。

​	`PowerModelSqrt`是以平方根增长的方式计算能耗，我们可以用一个公式表达，设最后计算的power为y，constant为a，staticpower为b，(utlization*100)为x,其中constant=(maxPower - StaticPower) /√100)得出：

​	y=staticpower+constant*√(utlization*100)

​	抽象可以得出:y=a√x+b,可以得出PowerModelLinear类是以平方根增长的方式计算能耗

 	2.5：`PowerModelSpecPower`类其中底下还有七个子类分别为：

​		(1)：PowerModelSpecPowerIbmX3550XeonX5675

​		(2)：PowerModelSpecPowerHpProLiantMl110G4Xeon3040

​		(3)：PowerModelSpecPowerHpProLiantMl110G3PentiumD930

​		(4)：PowerModelSpecPowerIbmX3250XeonX3480

​		(5)：PowerModelSpecPowerIbmX3550XeonX5670

​		(6)：PowerModelSpecPowerHpProLiantMl110G5Xeon3075

​		(7)：PowerModelSpecPowerIbmX3250XeonX3470

​	这七个子类不同的只是自身的powerData数组所存储的数组不同，其他没有区别，PowerModelSpecpower主要实现的过程是，首先传来一个utlization，让后根据utlization在不同的子类数组中选择两个返回值定义为power1和power2，定义一个变量delta=(power1-power2)/10，最终计算出

power=power1 + delta * (utilization - utilization1 / 10) * 100即为PowerModelSpecPower策略所测量出来的能耗。
