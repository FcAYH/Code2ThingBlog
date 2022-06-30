---
title: 平衡车-期末作业
date: 2022-06-30 02:33:45
tags: 平衡车
categories: 从代码到实物
---

# 平衡车-期末作业

## 联系企业

**2022/4/29**

李君老师组织了相关企业和我们的线上交流会，我们在众多企业项目中选择了XMaker提出的《“Arduino”平衡小车》这个题目。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/contactBusiness01.jpg">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/contactBusiness02.jpg">
2022/5/20-2011/5/26

今天在李君老师的协调下，联系到了魅客企业的人员，与我们联系的同样也是交大毕业的学姐，与我们交流了项目相关的事情，并且按照我们给出的材料清单寄来了元器件。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness03.jpg">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness04.jpg">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness05.jpg">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/ContactBusiness06.jpg">

## 搭建小车

**2022/06/05-2022/06/12**

解决了电路连接问题后，我们便正式开始搭建小车，通过激光切割和亚克力热弯的工艺，为小车设计了造型。通过调整硬件的布局，使得元器件能够合理的安放在小车上，同时预留了活动的电池仓位。最终的成品如下图所示：

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/buildCar01.jpg">

接下来的任务就是调节PID值，使得平衡小车能够“站起来”、“跑起来”

## 搜集资料并进行电路测试

**2022/5/26-2022/5/28**

这段时间在互联网上对相关信息进行了搜集，整理了部分的开源项目供参考。但在搜索的过程中发现，大多数的开源平衡小车项目，都是基于stm32平台，其中采用arduino方案的较少，其中最具代表性的是B站大佬稚晖君的早期作品，这个机器人的体积十分小巧，麻雀虽小但五脏俱全，Nano是一个小巧可爱的机器人，身高大约10公分，特点是平衡感好，长得很白以及善于卖萌。作为全球最迷你的自平衡机器人，Nano身材虽小，但配置有丰富的传感器—陀螺仪，超声波，Motion sensor，如果喜欢的话你可以让它自主巡线，跟踪，避障…但由于我们使用到的材料有限，且需要使用到激光切割技术，因此仅作参考。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test01.png">

同时我们在Google上看到了一个简易版的平衡小车项目，这个项目相当于稚晖君项目的“青春版”，更适合利用我们现有的元器件上手制作，该项目的网址是https://www.instructables.com/Arduino-Self-Balancing-Robot-1/。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test02.png">

要使得小车平衡，需要了解倒立摆原理

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test03.png">

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test04.png">

当物体离开垂直的平衡位置之后，便会受到重力与悬线的作用合力，驱动重物回复平衡位置，这个力称之为回复力，其大小为

**F = − mg\*sinθ**

在偏移角度 θ很小的情况下， `sinθ ≈ θ`，所以回复力与偏移的角度之间大小成正比关系, 方向相反，在此恢复力作用下，单摆便进行周期运动。

而考虑在空气中运动的单摆，由于受到空气的阻尼力， 单摆最终会停止在垂直平衡位置。空气的阻尼力与单摆运动的角速度成正比， 方向相反。阻尼力越大，单摆越会尽快在垂直位置稳定下来。

现在来看这样一个等效模型

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test05.png">

我们的车模其实就相当于一个倒立的钟摆，可以看到，这时候重力对于物体的作用是向下的，也就是当物体偏移了一点角度之后，重力作用会和偏移角度方向相同，如果车轮不运动的话，摆就会很快倒下。

为了解决这个问题， 使得倒立摆能够像单摆一样，稳定在垂直位置，我们有两个办法：

1、改变重力的方向 2、增加额外的受力，使得恢复力与位移方向相反。

显然能够做到的只有第二种方法，为此我们根据摆的偏移角度控制车轮加减速运动，这样在小车的坐标系（非惯性系）里，摆就会受到额外的惯性力作用，最终让摆平衡起来。

根据这些开源项目，我们确定了我们的目标，制作这个平衡小车需要用到的材料有：

Arduino Nano、MPU-6050陀螺仪、DRV8833电机驱动模块、5V电池、直流电机、超声波传感器、亚克力外壳等



**2022/5/28-2022/6/05**

确定好基本的技术方案和元器件后，我们就开始进行初步的电路搭建和代码测试。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test06.png">

但出师不利，按照开源项目中的连线方式进行电路连接，并烧录代码，电机并没有如期转动，要么一直转动，要么不转，而且传感器在串口中返回的数据也出现了问题，时而正常时而不正常。

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/test07.png">

我们将电路拆了装、装了拆，但问题一筹莫展，一直折腾了两个晚上，终于发现问题所在，是用于电路连接的杜邦线出现了问题，出现了接触不良的问题，导致传感器数据和电平信号不能正确的传递，通过更换线材，问题终于得到了解决。


