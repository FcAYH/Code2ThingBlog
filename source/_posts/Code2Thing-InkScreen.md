---
title: 基于ESP32的电子墨水屏TodoList台历
date: 2022-06-30 02:21:46
tags: 墨水瓶
categories: 从代码到实物
cover: "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen01.jpg"
---

# 基于ESP32的电子墨水屏TodoList台历

## 项目简介

​	设备端采用ESP32开发板以及4.2寸红黑白三色电子墨水屏搭建，能够实现天气查看、待办查看、新闻查看等功能。本项目基于开源项目优化而来，在其基础上增加了待办事项等功能。团队利用亚克力材料激光切割，设计出了设备的外壳，并利用激光扫描刻上了团队成员的代号。同时使用python构建TodoList管理后端，连接MySQL数据库。提供“新增待办”接口给QQ机器人，可通过给QQ机器人发送消息，添加待办；提供“查看待办”接口给ESP32，使其能够从服务器中获取待办事项，并显示到TodoList台历上。

## 制作流程

### 嘉立创打板，手动焊接

拿到板子后，支持夹固定，开始焊接！ 电阻，电容 ，滑动开关 等

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen01.jpg" alt="IMG_20220418_193144" style="zoom: 40%;" />

在对于比较小型的电阻焊接时，焊烙铁相对小电阻过于大，容易对不准。所以我们一般上完焊锡后，用镊子夹住小电阻贴紧焊锡，用**热风枪**吹化凝固即可。

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen02.jpg" alt="QQ图片20220629230915" style="zoom:40%;" />

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen03.jpg" alt="QQ图片20220629230920" style="zoom:40%;" />

### 购置墨水瓶

​	我们去咸鱼购置墨水屏，连接到板子上发现点不亮，遂排查板子与屏幕的焊接，最后依旧点不亮是确定是屏幕的问题。于是我们去二手群淘入一块**发现一点就亮**

<video src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen04.jpg"></video>

### 嵌入程序 并且优化

向eps32中嵌入程序，功能含有天气查看、待办查看、新闻查看等。

本项目基于开源项目优化而来，在其基础上增加了待办事项等功能。使用python构建TodoList管理后端，连接MySQL数据库。提供“新增待办”接口给QQ机器人，可通过给QQ机器人发送消息，添加待办；提供“查看待办”接口给ESP32，使其能够从服务器中获取待办事项，并显示到TodoList台历上。

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen05.jpg" alt="QQ图片20220629230822" style="zoom: 80%;" />

### 激光切割

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen06.jpg" alt="Screenshot_20220629_235339_com.tencent.mm_edit_281048788576906.jpg" style="zoom:80%;" />

### 线路连接图

<img src = "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/inkScreen07.png">

## 成果展现

展现视频

\[源开源连接\]([GitHub - zhushengji/smart_desk_calendar_4in2ink_epd: 墨水屏多功能智能台历](https://github.com/zhushengji/smart_desk_calendar_4in2ink_epd))