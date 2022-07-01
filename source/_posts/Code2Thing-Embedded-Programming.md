---
title: Code2thing：嵌入式编程作业
date: 2022-06-30 02:04:50
tags: 点灯
categories: 从代码到实物
cover: "https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/light01.png"
---

# Code2thing：嵌入式编程作业

## 任务主题

ESP32+巴法云 实现语音助手（小爱同学/天猫精灵）控制舵机开关灯

## 任务完成时间

2022年4月8日

## 任务过程

1、确定主题

根据本次嵌入式编程作业的要求（通过ESP32远程控制外设完成任务），我们小组初步确定了本次作业的想法：用ESP32制作一个可以语音控制关灯的设备。

2、细化需求

从软硬件来看，要完成语音控制关灯，软件需要实现识别语音指令，启动外设；硬件需要实现一个用于关灯的机械装置。

从原材料来看，我们小组领到了1个ESP32模块、1个舵机、1个面包板以及若干连接线。

考虑ESP32的性能，以及没有录音模块这个实际情况，我们选择将语音识别这个功能交给手机上的语音助手完成，引入巴法云平台作为ESP32和语音助手之间的桥梁。巴法云可以与手机上的小爱同学等语音助手联动，同时可以将ESP32绑定为虚拟的智能家居设备，当语音助手识别到“关灯”语音时，会给巴法云平台发送指令关灯，巴法云将操作信息放入ESP32对应的任务序列，ESP32通过不断向巴法云平台查询任务序列来获取任务，并完成预设的操作。

<img src="https://cdn.jsdelivr.net/gh/FcAYH/Images/2022/06/30/light01.png" alt="巴法云" style="zoom:67%;" />

关灯的硬件选择舵机+塑料延长件，舵机在开灯和关灯时，分别向不同方向旋转60°，带动塑料延长件按下电灯按键，实现开关等的操作。

3、外观设计

在硬件上，除了选择合适长度的塑料延长件以外，我们还量取了标准开关的外框大小，准备使用3D打印机打印一个外壳，复用电灯开关盒的螺丝孔，直接固定到开关盒上。将舵机、ESP32模块直接放在外壳内，使得整个设备更加美观。（受限于时间和材料未具体实现，仅完成模型设计）

4、软件编写

软件需要完成的流程：轮询巴法云接口 -> 读取到开/关灯指令 -> 控制舵机旋转并复位

编程的整体难度不大，参考巴法云官方例程可以比较轻松的完成编写，具体代码及注释如下：

```c
/*
 * esp32+巴法云 语音助手控制舵机
 * 2022-04-08
 * Author：CrayonDing、Yintel、FcAYH、KaiRay
 * 参考代码：巴法云官方例程 
 */
#include <WiFi.h>
#include <WiFiClient.h>
#include <Servo.h>

//巴法云服务器地址默认即可
#define TCP_SERVER_ADDR "bemfa.com"
//服务器端口，tcp创客云端口8344
#define TCP_SERVER_PORT "8344"

//********************需要修改的部分*******************//

#define DEFAULT_STASSID  "WIFI-SSID"     //WIFI名称，区分大小写，不要写错
#define DEFAULT_STAPSW   "WIFI-Password"  //WIFI密码
String UID = "af7afacb*******19f629b2d";  //用户私钥，可在控制台获取,修改为自己的UID
String TOPIC =   "esp32test002";         //主题名字，可在控制台新建，这里如果需要用灯的配置需要尾缀002

Servo servo1;
static const int servoPin = 16;
static const int OpenParameter = 105;   //开启开关舵机的角度
static const int CloseParameter = 5; //关闭开关舵机的角度
static const int ResetParemeter = 45; //舵机复位角度

String readMsg = "";

//******************巴法云长连接例程************************//

//最大字节数
#define MAX_PACKETSIZE 512
//设置心跳值30s
#define KEEPALIVEATIME 30*1000

//tcp客户端相关初始化，默认即可
WiFiClient TCPclient;
String TcpClient_Buff = "";
unsigned int TcpClient_BuffIndex = 0;
unsigned long TcpClient_preTick = 0;
unsigned long preHeartTick = 0;//心跳
unsigned long preTCPStartTick = 0;//连接
bool preTCPConnected = false;

//相关函数初始化
//连接WIFI
void doWiFiTick();
void startSTA();

//TCP初始化连接
void doTCPClientTick();
void startTCPClient();
void sendtoTCPServer(String p);

//led 控制函数
void turnOnLed();
void turnOffLed();

/*
  *发送数据到TCP服务器
 */
void sendtoTCPServer(String p){
  
  if (!TCPclient.connected()) 
  {
    Serial.println("Client is not readly");
    return;
  }
  TCPclient.print(p);
  Serial.println("[Send to TCPServer]:String");
  Serial.println(p);
}

/*
  *初始化和服务器建立连接
*/
void startTCPClient(){
  if(TCPclient.connect(TCP_SERVER_ADDR, atoi(TCP_SERVER_PORT))){
    Serial.print("\nConnected to server:");
    Serial.printf("%s:%d\r\n",TCP_SERVER_ADDR,atoi(TCP_SERVER_PORT));
    
    String tcpTemp="";  //初始化字符串
    tcpTemp = "cmd=1&uid="+UID+"&topic="+TOPIC+"\r\n"; //构建订阅指令
    sendtoTCPServer(tcpTemp); //发送订阅指令
    tcpTemp="";//清空
    
    preTCPConnected = true;
    preHeartTick = millis();
    TCPclient.setNoDelay(true);
  }
  else{
    Serial.print("Failed connected to server:");
    Serial.println(TCP_SERVER_ADDR);
    TCPclient.stop();
    preTCPConnected = false;
  }
  preTCPStartTick = millis();
}

/*
  *检查数据，发送心跳
*/
void doTCPClientTick(){
 //检查是否断开，断开后重连
   if(WiFi.status() != WL_CONNECTED) return;

  if (!TCPclient.connected()) {//断开重连

  if(preTCPConnected == true){

    preTCPConnected = false;
    preTCPStartTick = millis();
    Serial.println();
    Serial.println("TCP Client disconnected.");
    TCPclient.stop();
  }
  else if(millis() - preTCPStartTick > 1*1000)//重新连接
    startTCPClient();
  }
  else
  {
    if (TCPclient.available()) {//收数据
      char c =TCPclient.read();
      TcpClient_Buff +=c;
      TcpClient_BuffIndex++;
      TcpClient_preTick = millis();
      
      if(TcpClient_BuffIndex>=MAX_PACKETSIZE - 1){
        TcpClient_BuffIndex = MAX_PACKETSIZE-2;
        TcpClient_preTick = TcpClient_preTick - 200;
      }
      preHeartTick = millis();
    }
    if(millis() - preHeartTick >= KEEPALIVEATIME){//保持心跳
      preHeartTick = millis();
      Serial.println("--Keep alive:");
      sendtoTCPServer("cmd=0&msg=keep\r\n");
    }
  }
  if((TcpClient_Buff.length() >= 1) && (millis() - TcpClient_preTick>=200)) // TCP链接正常，根据收到的指令调用具体操作
  {//data ready
    TCPclient.flush();
    Serial.println("Buff");
    Serial.println(TcpClient_Buff);
    if((TcpClient_Buff.indexOf("&msg=on") > 0)) {	//收到开灯指令
      turnOnLight();
    }else if((TcpClient_Buff.indexOf("&msg=off") > 0)) {	//收到关灯指令
      turnOffLight();
    }
   TcpClient_Buff="";
   TcpClient_BuffIndex = 0;
  }
}

void startSTA(){
  WiFi.disconnect();
  WiFi.mode(WIFI_STA);
  WiFi.begin(DEFAULT_STASSID, DEFAULT_STAPSW);
}

/**************************************************************************
                                 WIFI
***************************************************************************/
/*
  WiFiTick
  检查是否需要初始化WiFi
  检查WiFi是否连接上，若连接成功启动TCP Client
  控制指示灯
*/
void doWiFiTick(){
  static bool startSTAFlag = false;
  static bool taskStarted = false;
  static uint32_t lastWiFiCheckTick = 0;

  if (!startSTAFlag) {
    startSTAFlag = true;
    startSTA();
    Serial.printf("Heap size:%d\r\n", ESP.getFreeHeap());
  }

  //未连接1s重连
  if ( WiFi.status() != WL_CONNECTED ) {
    if (millis() - lastWiFiCheckTick > 1000) {
      lastWiFiCheckTick = millis();
    }
  }
  //连接成功建立
  else {
    if (taskStarted == false) {
      taskStarted = true;
      Serial.print("\r\nGet IP Address: ");
      Serial.println(WiFi.localIP());
      startTCPClient();
    }
  }
}

//*************************具体操作*****************************//
void turnOnLight(){//开灯
	servo1.write(OpenParameter);
	delay(200);
	servo1.write(ResetParemeter);
	Serial.print("TURN ON\n");
}
void turnOffLight(){//关灯
    servo1.write(CloseParameter);
    delay(200);
    servo1.write(ResetParemeter);
    Serial.print("TRUN OFF\n");
}

// 初始化，相当于main 函数
void setup() {
  Serial.begin(115200);
  Serial.println("Beginning...");
  doWiFiTick();
  delay(50);
  Serial.println("The device started, now you can pair it with bluetooth!");
  servo1.attach(
    servoPin,
    Servo::CHANNEL_NOT_ATTACHED,
    0,
    180
  );
  servo1.write(ResetParemeter);
}

//循环
void loop() {
  doWiFiTick();
  doTCPClientTick();
}
```

## 效果展示

[展示视频](https://gitee.com/yintel/code2-thing2022-spring/raw/master/Startup/%E6%BC%94%E7%A4%BA%E8%A7%86%E9%A2%91.mp4)

## 仓库地址

[Yintel/Code2Thing2022Spring](https://gitee.com/yintel/code2-thing2022-spring)