# KE0175 Keyes STEM 电子积木编程教育开发板

![image-20250317111811323](media/A1.png)

---

## 资料下载

[资料](./资料.7z)

## 1. 简介

KE0175 Keyes STEM 电子积木编程教育开发板是一款基于 ATmega328P 的单片机开发板，完全兼容 Arduino IDE 开发环境。该开发板集成了一块 1.8 寸 TFT 屏和 SD 卡模块，便于显示实验内容和储存数据。可搭配丰富的 XH2.5 接口扩展使用，外围传感器即插即用，开发板上有四个螺丝定位孔，可搭配电子积木，完成简单的造型和创意性的实验。

---

## 2. 特点
- **兼容性强**：完全兼容 Arduino IDE 开发环境，易于上手。
- **集成显示**：内置 1.8 寸 TFT 屏，方便实时显示数据和信息。
- **扩展性好**：支持 XH2.5 接口，外围传感器即插即用。
- **环保设计**：采用环保材料，安全可靠。
- **多功能**：适合教育、DIY 项目和创意实验。

---

## 3. 规格参数
- **USB 输入电压**：DC 3.3V - 5V  
- **VIN 输入电压**：DC 7~12V  
- **IO 输出电流**：80mA  
- **VCC 输出最大电流**：3A  
- **最大功率**：15W  
- **工作温度范围**：-10~50℃  
- **微控制器**：ATmega328P-AU  
- **USB 转串口芯片**：CP2102  
- **数字 I/O 引脚**：8 (D0-D7)  
- **PWM 通道**：3 (D3、D5、D6)  
- **模拟输入通道（ADC）**：8 (A0-A7)  
- **Flash Memory**：32 KB（其中引导程序使用 0.5 KB）  
- **SRAM**：2 KB (ATmega328P-AU)  
- **EEPROM**：1 KB (ATmega328P-AU)  
- **时钟速度**：16MHz  
---

## 4. 接口
- **USB 接口**：用于供电和编程。
- **VIN 接口**：外部电源输入。
- **数字 I/O 引脚**：用于连接传感器和执行器。
- **PWM 接口**：用于脉宽调制输出。
- **模拟输入引脚**：用于读取模拟信号。
- **SD 卡模块**：用于存储数据和文件。

![image-20250317111835415](media/A2.png)

---

## 5. 连接图

![image-20250318160128793](media/A3.png)

**引脚定义**

- **VCC**：连接到 Arduino 的 5V 引脚。
- **GND**：连接到 Arduino 的 GND 引脚。
- **DATA**：连接到 Arduino 的数字引脚（如 D2）。

---

## 6. 示例代码
以下是一个简单的示例代码，用于读取 SD 卡中的 BMP 图片并在 TFT 屏上显示，同时进行电压测试和流水灯测试：

注意：

1、请先将开头下载的资料里面的图片复制到SD卡根目录，并且SD卡需要使用FAT32格式；

2、上传代码时请使用开头下载的资料里面的代码，里面包含了库文件，直接复制下面代码没有库文件会报错。

```c++
#include "Adafruit_GFX.h"
#include "Adafruit_ST7735.h"
#include <SD.h>
#include <SPI.h>

#if defined(__SAM3X8E__)
    #undef __FlashStringHelper::F(string_literal)
    #define F(string_literal) string_literal
#endif

// TFT 显示屏和 SD 卡将共享硬件 SPI 接口。
// 硬件 SPI 引脚特定于 Arduino 开发板类型，
// 并且不能重新映射到备用引脚。对于 Arduino Uno、
// Duemilanove 等，引脚 11 = MOSI，引脚 12 = MISO，引脚 13 = SCK。
#define SD_CS    7  // SD 卡的片选线
#define TFT_CS  8  // TFT 显示屏的片选线
#define TFT_DC   10  // TFT 的数据/命令线
#define TFT_RST  9  // TFT 的复位线（或连接到 +5V）

Adafruit_ST7735 tft = Adafruit_ST7735(TFT_CS, TFT_DC, TFT_RST);

int Delay_Timer = 300;

void setup(void) {
  Serial.begin(9600);
  Pin_Set();
  // 初始化 1.8" TFT
  //tft.initR(INITR_BLACKTAB);   // 初始化 ST7735S 芯片，黑片 (black tab)
  tft.initR();
  
  Serial.println("OK!");
  tft.fillScreen(ST7735_BLACK);
  tft.setRotation(0);
  tft.setTextSize(2);
  Serial.print("Initializing SD card...");
  if (!SD.begin(SD_CS))
  {
    Serial.println("failed!");
    tft.setTextColor(ST7735_RED);
    tft.setCursor(30, 40);
    tft.print("SD Error");
    return;
   }
   else
   {
      Serial.println("SD OK");
      tft.setTextColor(ST7735_RED);
      tft.setCursor(30, 40);
      
      tft.print("SD OK");
   }
  delay(500);
}

void loop() 
{  
  Bmp();            //SD卡读取图片测试
  //while (1);
}

void Bmp()
{
  bmpDraw("car.bmp", 0, 0);
  delay(Delay_Timer);
  bmpDraw("avatar.bmp", 0, 0);
  delay(Delay_Timer);
//  bmpDraw("DEMA.bmp", 0, 0);
//  delay(Delay_Timer);
//  bmpDraw("DLAM.bmp", 0, 0);
//  delay(Delay_Timer);
//  bmpDraw("mangseng.bmp", 0, 0);
//  delay(Delay_Timer);
//  bmpDraw("TLP.bmp", 0, 0);
//  delay(Delay_Timer);
//  bmpDraw("girl.bmp", 0, 0);
//  delay(Delay_Timer);
}

void Pin_Set()
{
  for(int i = 0; i < 7; i++)
  {
    pinMode(i,OUTPUT);
  }
  for(int i = 14; i < 20; i++)
  {
    pinMode(i,OUTPUT);
  }

  for(int i = 0; i < 7; i++)
  {
    digitalWrite(i,HIGH);
  }
  for(int i = 14; i < 20; i++)
  {
    digitalWrite(i,HIGH);
  }
}

// 该函数打开一个 Windows 位图 (BMP) 文件，
// 并将其显示在给定的坐标处。它通过一次读取
// 多个像素的数据（而不是逐个像素读取）来提高速度。
// 增加缓冲区大小会占用更多 Arduino 宝贵的 RAM，
// 但会使加载速度稍微快一点。20 个像素似乎是
// 一个很好的平衡点。

#define BUFFPIXEL 20

void bmpDraw(char *filename, uint8_t x, uint8_t y) {

  File     bmpFile;
  int      bmpWidth, bmpHeight;   // 像素的宽+高
  uint8_t  bmpDepth;              // 位深度（目前必须为 24）
  uint32_t bmpImageoffset;        // 文件中图像数据的起始位置
  uint32_t rowSize;               // 不一定等于 bmpWidth；可能有填充字节
  uint8_t  sdbuffer[3*BUFFPIXEL]; // 像素缓冲区（每个像素 R+G+B）
  uint8_t  buffidx = sizeof(sdbuffer); // sdbuffer 中的当前位置
  boolean  goodBmp = false;       // 在成功解析有效的头部时设置为 true
  boolean  flip    = true;        // BMP 存储方式为自下而上
  int      w, h, row, col;
  uint8_t  r, g, b;
  uint32_t pos = 0, startTime = millis();

  if((x >= tft.width()) || (y >= tft.height())) return;

  Serial.println();
  Serial.print("Loading image '");
  Serial.print(filename);
  Serial.println('\'');

  // 在 SD 卡上打开请求的文件
  if ((bmpFile = SD.open(filename)) == NULL) {
    Serial.print("File not found");
    return;
  }

  // 解析 BMP 头部
  if(read16(bmpFile) == 0x4D42) { // BMP 签名 (0x4D42 是 'BM')
    Serial.print("File size: "); Serial.println(read32(bmpFile));
    (void)read32(bmpFile); // 读取并忽略创建者字节
    bmpImageoffset = read32(bmpFile); // 图像数据的起始位置
    Serial.print("Image Offset: "); Serial.println(bmpImageoffset, DEC);
    // 读取 DIB 头部
    Serial.print("Header size: "); Serial.println(read32(bmpFile));
    bmpWidth  = read32(bmpFile);
    bmpHeight = read32(bmpFile);
    if(read16(bmpFile) == 1) { // 颜色平面数 -- 必须为 '1'
      bmpDepth = read16(bmpFile); // 每像素位数 (bits per pixel)
      Serial.print("Bit Depth: "); Serial.println(bmpDepth);
      if((bmpDepth == 24) && (read32(bmpFile) == 0)) { // 0 = 未压缩

        goodBmp = true; // 支持的 BMP 格式 -- 继续！
        Serial.print("Image size: ");
        Serial.print(bmpWidth);
        Serial.print('x');
        Serial.println(bmpHeight);

        // BMP 的行需要填充到 4 字节的边界（如果需要）
        rowSize = (bmpWidth * 3 + 3) & ~3;

        // 如果 bmpHeight 是负数，则图像是自上而下顺序的。
        // 这不是标准规范，但在实际应用中观察到过这种情况。
        if(bmpHeight < 0) {
          bmpHeight = -bmpHeight;
          flip      = false;
        }

        // 裁剪需要加载的区域
        w = bmpWidth;
        h = bmpHeight;
        if((x+w-1) >= tft.width())  w = tft.width()  - x;
        if((y+h-1) >= tft.height()) h = tft.height() - y;

        // 将 TFT 的地址窗口设置为裁剪后的图像边界
        tft.startWrite();
        tft.setAddrWindow(x, y, w, h);

        for (row=0; row<h; row++) { // 对于每条扫描线...

          // 定位到扫描线的起始位置。在每一行都执行此操作
          // 似乎很费劲，但这种方法涵盖了裁剪和扫描线填充等
          // 许多繁琐的细节。此外，只有在文件位置实际需要
          // 更改时才会执行定位（避免了 SD 库中大量的簇计算）。
          if(flip) // 位图以自下而上的顺序存储（正常 BMP）
            pos = bmpImageoffset + (bmpHeight - 1 - row) * rowSize;
          else     // 位图以自上而下的顺序存储
            pos = bmpImageoffset + row * rowSize;
          if(bmpFile.position() != pos) { // 需要定位吗？
            tft.endWrite();
            bmpFile.seek(pos);
            buffidx = sizeof(sdbuffer); // 强制重新加载缓冲区
          }

          for (col=0; col<w; col++) { // 对于每个像素...
            // 需要读取更多像素数据了吗？
            if (buffidx >= sizeof(sdbuffer)) { // 确实需要
              bmpFile.read(sdbuffer, sizeof(sdbuffer));
              buffidx = 0; // 将索引设置回开头
              tft.startWrite();
            }

            // 将像素从 BMP 格式转换为 TFT 格式，并推送到显示屏
            b = sdbuffer[buffidx++];
            g = sdbuffer[buffidx++];
            r = sdbuffer[buffidx++];
            tft.pushColor(tft.color565(r,g,b));
          } // 结束 pixel 循环
        } // 结束 scanline 循环
        tft.endWrite();
        Serial.print("Loaded in ");
        Serial.print(millis() - startTime);
        Serial.println(" ms");
      } // 结束 goodBmp 判断
    }
  }

  bmpFile.close();
  if(!goodBmp) Serial.println("BMP format not recognized.");
}

// 这些函数从 SD 卡文件中读取 16 位和 32 位类型的数据。
// BMP 数据以小端模式存储，Arduino 也是小端模式。
// 如果移植到其他平台，可能需要反转下标顺序。

uint16_t read16(File f) {
  uint16_t result;
  ((uint8_t *)&result)[0] = f.read(); // 最低有效字节 (LSB)
  ((uint8_t *)&result)[1] = f.read(); // 最高有效字节 (MSB)
  return result;
}

uint32_t read32(File f) {
  uint32_t result;
  ((uint8_t *)&result)[0] = f.read(); // 最低有效字节 (LSB)
  ((uint8_t *)&result)[1] = f.read();
  ((uint8_t *)&result)[2] = f.read();
  ((uint8_t *)&result)[3] = f.read(); // 最高有效字节 (MSB)
  return result;
}
```

---

## 7. 实验现象
上传程序后，开发板将读取 SD 卡中的图片并在 TFT 屏上显示内容。如果 SD 卡插入正确且文件存在，屏幕将显示文件内容；如果出现错误，屏幕将显示相应的错误信息。同时，电压测试结果将显示在屏幕上，流水灯测试将依次点亮数字引脚。

![image-20250317112011488](media/A4.png)

---

## 8. 注意事项
- 确保 SD 卡中存在指定的 BMP 文件（如 `car.bmp` 和 `avatar.bmp`）。

- 确保供电电压在 7-12V 范围内，避免损坏开发板。

- 在上传代码之前，确保选择正确的板和 COM 口。

- 使用合适的库文件以确保程序正常运行。

- SD卡必须使用FAT32的格式。

  

如有更多疑问，请联系 Keyes 官方客服或加入相关创客社区交流。祝使用愉快！