---
title: 平衡车-期末作业
date: 2022-06-30 02:33:45
tags: 平衡车
categories: 从代码到实物
cover: "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/contactBusiness01.jpg"
---

# 平衡车-期末作业

## 联系企业

**2022/4/29**

李君老师组织了相关企业和我们的线上交流会，我们在众多企业项目中选择了XMaker提出的《“Arduino”平衡小车》这个题目。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/contactBusiness01.jpg" style="zoom:80%;">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/contactBusiness02.jpg" style="zoom:40%;">

**2022/5/20-2011/5/26**

今天在李君老师的协调下，联系到了魅客企业的人员，与我们联系的同样也是交大毕业的学姐，与我们交流了项目相关的事情，并且按照我们给出的材料清单寄来了元器件。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness03.jpg" style="zoom:35%;">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness04.jpg" style="zoom:35%;">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness05.jpg" style="zoom:35%;">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness06.jpg" style="zoom:25%;">

## 搭建小车

**2022/06/05-2022/06/12**

解决了电路连接问题后，我们便正式开始搭建小车，通过激光切割和亚克力热弯的工艺，为小车设计了造型。通过调整硬件的布局，使得元器件能够合理的安放在小车上，同时预留了活动的电池仓位。最终的成品如下图所示：

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/buildCar01.jpg" style="zoom:20%;">

接下来的任务就是调节PID值，使得平衡小车能够“站起来”、“跑起来”

## 搜集资料并进行电路测试

**2022/5/26-2022/5/28**

这段时间在互联网上对相关信息进行了搜集，整理了部分的开源项目供参考。但在搜索的过程中发现，大多数的开源平衡小车项目，都是基于stm32平台，其中采用arduino方案的较少，其中最具代表性的是B站大佬稚晖君的早期作品，这个机器人的体积十分小巧，麻雀虽小但五脏俱全，Nano是一个小巧可爱的机器人，身高大约10公分，特点是平衡感好，长得很白以及善于卖萌。作为全球最迷你的自平衡机器人，Nano身材虽小，但配置有丰富的传感器—陀螺仪，超声波，Motion sensor，如果喜欢的话你可以让它自主巡线，跟踪，避障…但由于我们使用到的材料有限，且需要使用到激光切割技术，因此仅作参考。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test01.png" style="zoom:70%;">

同时我们在Google上看到了一个简易版的平衡小车项目，这个项目相当于稚晖君项目的“青春版”，更适合利用我们现有的元器件上手制作，该项目的网址是https://www.instructables.com/Arduino-Self-Balancing-Robot-1/。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test02.png" style="zoom:30%;">

要使得小车平衡，需要了解倒立摆原理

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test03.png" style="zoom:60%;">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test04.png" style="zoom:70%;">

当物体离开垂直的平衡位置之后，便会受到重力与悬线的作用合力，驱动重物回复平衡位置，这个力称之为回复力，其大小为

**F = − mg\*sinθ**

在偏移角度 θ很小的情况下， `sinθ ≈ θ`，所以回复力与偏移的角度之间大小成正比关系, 方向相反，在此恢复力作用下，单摆便进行周期运动。

而考虑在空气中运动的单摆，由于受到空气的阻尼力， 单摆最终会停止在垂直平衡位置。空气的阻尼力与单摆运动的角速度成正比， 方向相反。阻尼力越大，单摆越会尽快在垂直位置稳定下来。

现在来看这样一个等效模型

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test05.png" style="zoom:50%;">

我们的车模其实就相当于一个倒立的钟摆，可以看到，这时候重力对于物体的作用是向下的，也就是当物体偏移了一点角度之后，重力作用会和偏移角度方向相同，如果车轮不运动的话，摆就会很快倒下。

为了解决这个问题， 使得倒立摆能够像单摆一样，稳定在垂直位置，我们有两个办法：

1、改变重力的方向 2、增加额外的受力，使得恢复力与位移方向相反。

显然能够做到的只有第二种方法，为此我们根据摆的偏移角度控制车轮加减速运动，这样在小车的坐标系（非惯性系）里，摆就会受到额外的惯性力作用，最终让摆平衡起来。

根据这些开源项目，我们确定了我们的目标，制作这个平衡小车需要用到的材料有：

Arduino Nano、MPU-6050陀螺仪、DRV8833电机驱动模块、5V电池、直流电机、超声波传感器、亚克力外壳等

**2022/5/28-2022/6/05**

确定好基本的技术方案和元器件后，我们就开始进行初步的电路搭建和代码测试。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test06.png" style="zoom:60%;">

但出师不利，按照开源项目中的连线方式进行电路连接，并烧录代码，电机并没有如期转动，要么一直转动，要么不转，而且传感器在串口中返回的数据也出现了问题，时而正常时而不正常。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test07.png" style="zoom:40%;">

我们将电路拆了装、装了拆，但问题一筹莫展，一直折腾了两个晚上，终于发现问题所在，是用于电路连接的杜邦线出现了问题，出现了接触不良的问题，导致传感器数据和电平信号不能正确的传递，通过更换线材，问题终于得到了解决。

## PID调试

硬件部分搭建完成后，需要根据小车的特性对小车完成各传感器的校准和PID的调试。

这里我们使用了网上找的一个MPU6050校准程序（[URL](http://42bots.com/tutorials/arduino-script-for-mpu-6050-auto-calibration/)），可以通过不断地枚举给出MPU6050的偏移量矫正参数。

完成校准后，开始调整PID参数，所谓PID就是Proportional, Integral, and Derivative三个单词的首字母，分别代表比例、积分和微分。

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/pid01.png" alt="PID" style="zoom: 50%;" />

> 比例（P）控制
>
> 比例控制是一种最简单的控制方式。其控制器的输出与输入误差信号成比例关系。当仅有比例控制时系统输出存在稳态误差。
>
> 积分（I）控制
>
> 在积分控制中，控制器的输出与输入误差信号的积分成正比关系。对一个自动控制系统，如果在进入稳态后存在稳态误差，则称这个控制系统是有稳态误差的或简称有差系统（System with Steady-state Error）。为了消除稳态误差，在控制器中必须引入“积分项”。积分项对误差取决于时间的积分，随着时间的增加，积分项会增大。这样，即便误差很小，积分项也会随着时间的增加而加大，它推动控制器的输出增大使得稳态误差进一步减小，直到接近于零。因此，比例+积分（PI）控制器，可以使系统在进入稳态后几乎无稳态误差。
>
> 微分（D）控制
>
> 在微分控制中，控制器的输出与输入误差信号的微分（即误差的变化率）成正比关系。自动控制系统在克服误差的调节过程中可能会出现震荡甚至失稳。其原因是由于存在有较大惯性组件（环节）或有滞后（delay）组件，具有抑制误差的作用， 其变化总是落后于误差的变化。解决的办法是使抑制误差的作用的变化“超前”，即在误差接近零时，抑制误差的作用就应该是零。这就是说，在控制器中仅引入 “比例”项往往是不够的，比例项的作用仅是放大误差的幅值，而需要增加的是“微分项”，它能预测误差变化的趋势，这样，具有比例+微分的控制器，就能够提前使抑制误差的控制作用等于零，甚至为负值，从而避免了被控量的严重超调。所以对有较大惯性或滞后的被控对象，比例+微分（PD）控制器能改善系统在调节过程中的动态特性。

按照如下顺序调整PID：

1. 将Ki和Kd设置为零，逐渐增加Kp，使机器人开始围绕平衡位置振荡。
2. 增加Ki，使机器人在失去平衡时反应更快。Ki应该足够大，以便倾斜角不会增加。如果机器人倾斜时，它应该回到平衡位置。
3. 增加Kd，以减少振荡和过冲。
4. 重复上述步骤，对每个参数进行微调，以达到最佳效果。

调整完成后，小车可以成功的实现自我调整平衡的效果。