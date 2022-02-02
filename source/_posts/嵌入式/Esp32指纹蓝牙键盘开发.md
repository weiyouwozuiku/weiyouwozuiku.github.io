---
title: Esp32指纹蓝牙键盘开发
author: 不二
mathjax: true
date: 2020-11-18 18:57:29
tags: 
- Esp32
- 树莓派
cover: true
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/嵌入式/nnn.jpg
categories: 
- 嵌入式
---

因为Linux系统每次开机或者执行一些需要权限的命令时要输入密码，但是本身输入密码这件事就不太安全。综合现有情况，windows可以很方便的使用人脸识别，但是linux上指纹都不方便，也怕破坏启动配置。于是决定使用外部硬件的方式实现指纹识别后自动输入密码的功能。相对安全了点。

[**项目地址**](https://github.com/weiyouwozuiku/ESP32_BluetoothKeyboard)

### 功能逻辑

1. 通过蓝牙连接ESP32
2. 通过AS608校验指纹
3. 指纹校验通过则模拟成蓝牙键盘发送密码

### 硬件方面

- 采用的ESP32作为主控，其本身自带WIFI和蓝牙，非常便于之后的功能改善。
- AS608作为指纹传感器，负责指纹的录入和校验。
- OLED SSD1306作为屏幕进行必要的显示，方便了解处理进度。

ESP32的管脚图：

![ESP32指纹蓝牙键盘开发_IO.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/嵌入式/ESP32指纹蓝牙键盘开发/ESP32指纹蓝牙键盘开发_IO.png)

![ESP32指纹蓝牙键盘开发_IO2.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/嵌入式/ESP32指纹蓝牙键盘开发/ESP32指纹蓝牙键盘开发_IO2.png)

ESP32原理图：

![ESP32指纹蓝牙键盘开发_原理图1.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/嵌入式/ESP32指纹蓝牙键盘开发/ESP32指纹蓝牙键盘开发_原理图1.png)

![ESP32指纹蓝牙键盘开发_原理图2.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/嵌入式/ESP32指纹蓝牙键盘开发/ESP32指纹蓝牙键盘开发_原理图2.png)

IDE图方便选择的是Arduino的环境，在板子管理处添加如下字段，添加Esp32板子信息：

`https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`。

选择开发版`ESP32 Dev Module`。

安装库：`ESP8266 and ESP32 OLED driver for SSD1306 displays`和`Adafruit Fingerprint Senser Library`。

安装第三方库：从`https://github.com/T-vK/ESP32-BLE-Keyboard`下载并添加。

### 电路连接

OLED：

- VCC->3.3V
- GND->GND
- SCL->接D22
- SDA->D21

AS608:

- VCC->3.3V(5V会烧坏元件)
- GND->GND
- TX->RX2
- RX->TX2

![ESP32指纹蓝牙键盘开发_连线图.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/嵌入式/ESP32指纹蓝牙键盘开发/ESP32指纹蓝牙键盘开发_连线图.png)

### 代码

#### 指纹注册代码

```cpp
#include <Adafruit_Fingerprint.h>
#define mySerial Serial2 

Adafruit_Fingerprint finger = Adafruit_Fingerprint(&mySerial);

uint8_t id;

void setup()
{
  Serial.begin(9600);
  while (!Serial);
  delay(100);
  Serial.println("\n\nAdafruit Fingerprint sensor enrollment");

  // set the data rate for the sensor serial port
  finger.begin(57600);

  if (finger.verifyPassword()) {
    Serial.println("Found fingerprint sensor!");
  } else {
    Serial.println("Did not find fingerprint sensor :(");
    while (1) { delay(1); }
  }

  Serial.println(F("Reading sensor parameters"));
  finger.getParameters();
  Serial.print(F("Status: 0x")); Serial.println(finger.status_reg, HEX);
  Serial.print(F("Sys ID: 0x")); Serial.println(finger.system_id, HEX);
  Serial.print(F("Capacity: ")); Serial.println(finger.capacity);
  Serial.print(F("Security level: ")); Serial.println(finger.security_level);
  Serial.print(F("Device address: ")); Serial.println(finger.device_addr, HEX);
  Serial.print(F("Packet len: ")); Serial.println(finger.packet_len);
  Serial.print(F("Baud rate: ")); Serial.println(finger.baud_rate);
}

uint8_t readnumber(void) {
  uint8_t num = 0;

  while (num == 0) {
    while (! Serial.available());
    num = Serial.parseInt();
  }
  return num;
}

void loop()                     // run over and over again
{
  Serial.println("Ready to enroll a fingerprint!");
  Serial.println("Please type in the ID # (from 1 to 127) you want to save this finger as...");
  id = readnumber();
  if (id == 0) {// ID #0 not allowed, try again!
     return;
  }
  Serial.print("Enrolling ID #");
  Serial.println(id);

  while (!  getFingerprintEnroll() );
}

uint8_t getFingerprintEnroll() {

  int p = -1;
  Serial.print("Waiting for valid finger to enroll as #"); Serial.println(id);
  while (p != FINGERPRINT_OK) {
    p = finger.getImage();
    switch (p) {
    case FINGERPRINT_OK:
      Serial.println("Image taken");
      break;
    case FINGERPRINT_NOFINGER:
      Serial.println(".");
      break;
    case FINGERPRINT_PACKETRECIEVEERR:
      Serial.println("Communication error");
      break;
    case FINGERPRINT_IMAGEFAIL:
      Serial.println("Imaging error");
      break;
    default:
      Serial.println("Unknown error");
      break;
    }
  }

  // OK success!

  p = finger.image2Tz(1);
  switch (p) {
    case FINGERPRINT_OK:
      Serial.println("Image converted");
      break;
    case FINGERPRINT_IMAGEMESS:
      Serial.println("Image too messy");
      return p;
    case FINGERPRINT_PACKETRECIEVEERR:
      Serial.println("Communication error");
      return p;
    case FINGERPRINT_FEATUREFAIL:
      Serial.println("Could not find fingerprint features");
      return p;
    case FINGERPRINT_INVALIDIMAGE:
      Serial.println("Could not find fingerprint features");
      return p;
    default:
      Serial.println("Unknown error");
      return p;
  }

  Serial.println("Remove finger");
  delay(2000);
  p = 0;
  while (p != FINGERPRINT_NOFINGER) {
    p = finger.getImage();
  }
  Serial.print("ID "); Serial.println(id);
  p = -1;
  Serial.println("Place same finger again");
  while (p != FINGERPRINT_OK) {
    p = finger.getImage();
    switch (p) {
    case FINGERPRINT_OK:
      Serial.println("Image taken");
      break;
    case FINGERPRINT_NOFINGER:
      Serial.print(".");
      break;
    case FINGERPRINT_PACKETRECIEVEERR:
      Serial.println("Communication error");
      break;
    case FINGERPRINT_IMAGEFAIL:
      Serial.println("Imaging error");
      break;
    default:
      Serial.println("Unknown error");
      break;
    }
  }

  // OK success!

  p = finger.image2Tz(2);
  switch (p) {
    case FINGERPRINT_OK:
      Serial.println("Image converted");
      break;
    case FINGERPRINT_IMAGEMESS:
      Serial.println("Image too messy");
      return p;
    case FINGERPRINT_PACKETRECIEVEERR:
      Serial.println("Communication error");
      return p;
    case FINGERPRINT_FEATUREFAIL:
      Serial.println("Could not find fingerprint features");
      return p;
    case FINGERPRINT_INVALIDIMAGE:
      Serial.println("Could not find fingerprint features");
      return p;
    default:
      Serial.println("Unknown error");
      return p;
  }

  // OK converted!
  Serial.print("Creating model for #");  Serial.println(id);

  p = finger.createModel();
  if (p == FINGERPRINT_OK) {
    Serial.println("Prints matched!");
  } else if (p == FINGERPRINT_PACKETRECIEVEERR) {
    Serial.println("Communication error");
    return p;
  } else if (p == FINGERPRINT_ENROLLMISMATCH) {
    Serial.println("Fingerprints did not match");
    return p;
  } else {
    Serial.println("Unknown error");
    return p;
  }

  Serial.print("ID "); Serial.println(id);
  p = finger.storeModel(id);
  if (p == FINGERPRINT_OK) {
    Serial.println("Stored!");
  } else if (p == FINGERPRINT_PACKETRECIEVEERR) {
    Serial.println("Communication error");
    return p;
  } else if (p == FINGERPRINT_BADLOCATION) {
    Serial.println("Could not store in that location");
    return p;
  } else if (p == FINGERPRINT_FLASHERR) {
    Serial.println("Error writing to flash");
    return p;
  } else {
    Serial.println("Unknown error");
    return p;
  }

  return true;
}
```

#### 删除指纹代码

```cpp
#define mySerial Serial2
Adafruit_Fingerprint finger = Adafruit_Fingerprint(&mySerial);

void setup()
{
  Serial.begin(9600);
  while (!Serial);  // For Yun/Leo/Micro/Zero/...
  delay(100);
  Serial.println("\n\nDelete Finger");

  // set the data rate for the sensor serial port
  finger.begin(57600);

  if (finger.verifyPassword()) {
    Serial.println("Found fingerprint sensor!");
  } else {
    Serial.println("Did not find fingerprint sensor :(");
    while (1);
  }
}


uint8_t readnumber(void) {
  uint8_t num = 0;

  while (num == 0) {
    while (! Serial.available());
    num = Serial.parseInt();
  }
  return num;
}

void loop()                     // run over and over again
{
  Serial.println("Please type in the ID # (from 1 to 127) you want to delete...");
  uint8_t id = readnumber();
  if (id == 0) {// ID #0 not allowed, try again!
     return;
  }

  Serial.print("Deleting ID #");
  Serial.println(id);

  deleteFingerprint(id);
}

uint8_t deleteFingerprint(uint8_t id) {
  uint8_t p = -1;

  p = finger.deleteModel(id);

  if (p == FINGERPRINT_OK) {
    Serial.println("Deleted!");
  } else if (p == FINGERPRINT_PACKETRECIEVEERR) {
    Serial.println("Communication error");
    return p;
  } else if (p == FINGERPRINT_BADLOCATION) {
    Serial.println("Could not delete in that location");
    return p;
  } else if (p == FINGERPRINT_FLASHERR) {
    Serial.println("Error writing to flash");
    return p;
  } else {
    Serial.print("Unknown error: 0x"); Serial.println(p, HEX);
    return p;
  }
}
```

#### 使用代码

```cpp
#include "SSD1306Wire.h"        // legacy: #include "SSD1306.h"
#include <Wire.h>     
#include <BleKeyboard.h>
#include <Adafruit_Fingerprint.h>
#define mySerial Serial2 
BleKeyboard bleKeyboard;
SSD1306Wire display(0x3c, 21, 22);
Adafruit_Fingerprint finger = Adafruit_Fingerprint(&mySerial); 
//ESP32上的蓝色指示灯，GPIO2控制
const int led=2;
void displayHello(){
  display.clear();
  display.setTextAlignment(TEXT_ALIGN_LEFT);
  display.setFont(ArialMT_Plain_16);
  display.drawStringMaxWidth(0, 0, 128,
      "Weclome to use King's fingerprint bluetooth keyboard!" );
  display.display();
}
void displayMeg(String meg,int showTime=200){
    display.clear();
    display.drawStringMaxWidth(0,0,128,meg);
    display.display();
    delay(showTime);
}
void displayMeg3line(String meg1,String meg2,String meg3,int showTime=1000){
    display.clear();
    display.drawString(0,0,meg1);
    display.drawString(0,16,meg2);
    display.drawString(0,32,meg3);
    display.display();
    delay(showTime);
}
void connectFinger(){
  if (finger.verifyPassword()) {
    displayMeg("Found fingerprint sensor! :)");
  } else {
    display.clear();
    //Serial.println("Did't find fingerprint sensor :(");
    display.drawStringMaxWidth(0,0,128,"Did't find fingerprint sensor :(");
    display.display();
    delay(2000);
    while (1) { 
      delay(2000);
      display.clear();
      //Serial.println("waiting for fingerprint sensor...");
      display.drawStringMaxWidth(0,0,128,"Waiting for fingerprint sensor..."); 
      display.display();
      }
  }
}
void getValid(){
  displayMeg("Waiting for valid finger...",1000);
  finger.getTemplateCount();
  if (finger.templateCount == 0) {
    displayMeg("Sensor doesn't contain any fingerprint data. Please run the 'enroll'.");
  }
  else {
    displayMeg("Sensor contains templates!   ._.");
  }
}

void sendPassword(uint8_t id){
    digitalWrite(led,HIGH);
    display.clear();
    char stringId[25];
    itoa(id,stringId,10);
    display.drawString(0,0,"Fingerprint id :");
    display.drawString(0,16,stringId);
    display.display();
    if(bleKeyboard.isConnected()) {
      bleKeyboard.print("hello world");//自定义密码
      bleKeyboard.write(KEY_RETURN);
    }
    display.drawString(0,32,"Key sended by BT!");
    display.display();
    delay(3000);
    digitalWrite(led,LOW);
}
uint8_t getFingerprintID(){
  uint8_t p = finger.getImage();
  switch (p) {
    case FINGERPRINT_OK:
      //Serial.println("Image taken");
      break;
    case FINGERPRINT_NOFINGER:
      //Serial.println("No finger detected");
      return p;
    case FINGERPRINT_PACKETRECIEVEERR:
      //Serial.println("Communication error");
      return p;
    case FINGERPRINT_IMAGEFAIL:
      //Serial.println("Imaging error");
      return p;
    default:
      //Serial.println("Unknown error");
      return p;
  }

  // OK success!

  p = finger.image2Tz();
  switch (p) {
    case FINGERPRINT_OK:
      //Serial.println("Image converted");
      break;
    case FINGERPRINT_IMAGEMESS:
      //Serial.println("Image too messy");
      return p;
    case FINGERPRINT_PACKETRECIEVEERR:
      //Serial.println("Communication error");
      return p;
    case FINGERPRINT_FEATUREFAIL:
      //Serial.println("Could not find fingerprint features");
      return p;
    case FINGERPRINT_INVALIDIMAGE:
      //Serial.println("Could not find fingerprint features");
      return p;
    default:
      //Serial.println("Unknown error");
      return p;
  }

  // OK converted!
  p = finger.fingerSearch();
  if (p == FINGERPRINT_OK) {
    //Serial.println("Found a print match!");
  } else if (p == FINGERPRINT_PACKETRECIEVEERR) {
    //Serial.println("Communication error");
    return p;
  } else if (p == FINGERPRINT_NOTFOUND) {
    //Serial.println("Did not find a match");
    return p;
  } else {
    //Serial.println("Unknown error");
    return p;
  }

  // found a match!
  //Serial.print("Found ID #"); Serial.print(finger.fingerID);
  //Serial.print(" with confidence of "); Serial.println(finger.confidence);
  sendPassword(finger.fingerID);
  return finger.fingerID;
}
void checkBlueTooth(){
  String bluetoothStatue;
  if(bleKeyboard.isConnected()){
    bluetoothStatue="BT connected!";
    displayMeg3line(bluetoothStatue,"Waiting your fing-","er,King!",50);
  }else{
    bluetoothStatue="BT unconnected!";
    displayMeg3line(bluetoothStatue,"Please restart or ","connect!",50);
  }
}
void setup() {
  //Serial.begin(115200);
  //Serial.println("Weclome to use King's fingerprint bluetooth keyboard!");
  pinMode(2, OUTPUT);
  display.init();
  display.flipScreenVertically();
  displayHello();
  bleKeyboard.begin();
  finger.begin(57600);
  connectFinger();
  getValid();
}

void loop() {
//  displayMeg("Waiting your finger,king!");
  checkBlueTooth();
  getFingerprintID();
}
```

想要修改默认的蓝牙名称需要在默认`C:\Users\<用户名>\Documents\Arduino\libraries\ESP32-BLE-Keyboard`的`BleKeyboard.h`的第101行进行修改。

### 遇到的问题

在开发过程中，当OLED显示屏没有在和ESP32同时上电，可能导致OLED屏幕无法显示。但是在电脑端未出现此问题。同时采用ESP32的电源进行供电就没有此问题。

### 可选升级

#### 通过树莓派控制供电

USB 接口的供电并不是通过 GPIO 直接控制的，而是通过 USB 集线器 LAN9514 控制的。CPU 可以通过设置集线器的 PORT_POWER 选项完成 USB 供电电路的开关。

控制 USB 供电的电路出现在 Raspberry Pi Model B+ 之后的版本。原版的 Raspberry Pi Model B 的 USB 供电电路直接与 5V 相连，不受控制。

-P 参数用于指定要控制的端口，1 为集成网卡，2 为 4 个 USB 口。网卡的供电可以独立控制，所以开关 USB 口的供电并不会导致 ssh 断开。

下载并编译 [hub-ctrl.c](https://github.com/codazoda/hub-ctrl.c)（这个程序依赖于 libusb，编译时请带上参数 -lusb）。

调用如下命令控制USB的供电：

```shell
//查看设备
lsusb
//USB供电
sudo /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 1
//USB不供电
sudo /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 0
```

设置定时任务：

```shell
sudo crontab -e
//命令如下
//每天7点30供电，21点40停止供电
30 7 * * * /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 0
40 21 * * * /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 1
```

#### 添加触摸传感器

为了避免长时间供电损坏引脚以及避免烧屏，采用传感器触发开启指纹。

添加触摸传感器，其只有三个引脚VCC、GND、Sig。

通过Respeaker上的Grove接口预留的GPIO12采集其信号。当检测到触摸传感器被触摸后返回的高电平信号，树莓派给USB口供电，启动指纹蓝牙键盘。使用树莓派的$I^2C$连接SSD1306的OLED屏幕显示树莓派当前状态，并将每次的log信息保存在项目文件夹中。

代码如下：

```python
import time
import subprocess
from board import SCL, SDA
import busio
from PIL import Image, ImageDraw, ImageFont
import adafruit_ssd1306
import RPi.GPIO as GPIO
import os
Touch = 12
GPIO.setmode(GPIO.BCM)
GPIO.setup(Touch, GPIO.IN)
# Create the I2C interface.
i2c = busio.I2C(SCL, SDA)

disp = adafruit_ssd1306.SSD1306_I2C(128, 64, i2c)

disp.fill(0)
disp.show()

width = disp.width
height = disp.height
image = Image.new("1", (width, height))

draw = ImageDraw.Draw(image)

draw.rectangle((0, 0, width, height), outline=0, fill=0)

padding = -2
top = padding
bottom = height - padding
# Move left to right keeping track of the current x position for drawing shapes.
x = 0

font = ImageFont.truetype(
    '/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf', 13)

def displayInfo():
    draw.rectangle((0, 0, width, height), outline=0, fill=0)

    cmd = "hostname -I | cut -d' ' -f1"
    IP = subprocess.check_output(cmd, shell=True).decode("utf-8")
    cmd = "top -bn1 | grep load | awk '{printf \"CPU Load: %.2f\", $(NF-2)}'"
    CPU = subprocess.check_output(cmd, shell=True).decode("utf-8")
    cmd = "free -m | awk 'NR==2{printf \"Mem:%s/%s %.2f%%\", $3,$2,$3*100/$2 }'"
    MemUsage = subprocess.check_output(cmd, shell=True).decode("utf-8")
    cmd = 'df -h | awk \'$NF=="/"{printf "Disk:%d/%d GB  %s", $3,$2,$5}\''
    Disk = subprocess.check_output(cmd, shell=True).decode("utf-8")

    # Write four lines of text.

    draw.text((x, top + 0), "IP: " + IP, font=font, fill=255)
    draw.text((x, top + 15), CPU, font=font, fill=255)
    draw.text((x, top + 31), MemUsage, font=font, fill=255)
    draw.text((x, top + 47), Disk, font=font, fill=255)

    # Display image.
    disp.image(image)
    disp.show()
    time.sleep(0.1)

def logToFile(filePath, *words) -> bool:
    with open(filePath, 'a+', encoding="utf8")as f:
        for word in words:
            print(word, file=f, end="")
        print(file=f)
        return True
    return False

def checkTouch():
    info = GPIO.input(Touch)
    # print(info)
    if info == 1:
        logToFile(filePath, time.strftime(
            "%Y-%m-%d %H:%M:%S", time.localtime()))
        logToFile(filePath, "*"*30)
        logToFile(filePath, "FingerPrint Start!")
        os.system("sudo /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 1")
        draw.rectangle((0, 0, width, height), outline=0, fill=0)
        draw.text((x+10, top + 16), "FingerPrint Start!", font=font, fill=255)
        disp.image(image)
        disp.show()
        time.sleep(60)
        logToFile(filePath, "FingerPrint Stop!\n"+"*"*30+"\n")
        os.system("sudo /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 0")

pwd = os.getcwd()
filePath = os.path.join(pwd, "FingerLog.txt")
while True:
    displayInfo()
    checkTouch()
```

最终的crontab：

```shell
# sudo crontab -e 定时任务
40 21 * * * /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 0
# vim /etc/rc.local 开机自启
sudo /home/pi/hub-ctrl.c/hub-ctrl -b 001 -d 002 -P 2 -p 0
cd /home/pi && python3 finger.py
```

### 最终实物图

![ESP32指纹蓝牙键盘开发_实物图.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/嵌入式/ESP32指纹蓝牙键盘开发/ESP32指纹蓝牙键盘开发_实物图.jpgg)
