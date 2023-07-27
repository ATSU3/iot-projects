<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# IoT農業(土壌の温度測定、ビニールハウス内の温湿度測定)

#### 必要なパーツ

|    部品    | 
| --------- | 
| マイクロＳＤカードスロットＤＩＰ化キット  | 
| マイクロＳＤカード | 
| LCD ディスプレイモジュール | 
| DHT22 温度 湿度 センサー モジュール | 
| ESP-WROOM-32E | 
| タクトスイッチ |

#### プログラム

以下のリンクを参考にしました。
[ESP32でmicroSDカードを読み書きしてみました（おおたfab 電子工作初心者勉強会）](https://kanpapa.com/today/2023/01/esp32-otafab-study-microsd.html)

```c++

// 温湿度管理

#include "FS.h"
#include "SD.h"
#include "SPI.h"
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT22
#define SCK 18  //SDカードのCLKピン
#define MISO 19 //SDカードのDAT0ピン
#define MOSI 23 //SDカードのCMDピン
#define SS 4    //SDカードのCD/DAT3ピン
#define BUTTON_PIN 15  
#define BUTTON2_PIN 16 

DHT dht(DHTPIN, DHTTYPE);
LiquidCrystal_I2C lcd(0x27, 16, 2);
volatile bool sdCardStatus = false;
bool lcdStatus = true; // LCDの電源状態

unsigned long lastRecordTime = 0;  
unsigned long lastDisplayUpdateTime = 0;  

void setup() {
  lcd.init();
  lcd.backlight();
  dht.begin();

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(BUTTON2_PIN, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(BUTTON_PIN), tryMountSDCard, FALLING);

  tryMountSDCard();
}

void tryMountSDCard() {
  lcd.clear();
  if(!SD.begin()){
    lcd.print("Card Mount Failed");
    sdCardStatus = false;
    return;
  }
  uint8_t cardType = SD.cardType();
  if(cardType == CARD_NONE){
    lcd.print("No SD card attached");
    sdCardStatus = false;
  }
  else {
    sdCardStatus = true;
  }
}

void displayData() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(t);
  lcd.print("C");
  
  lcd.setCursor(0, 1);
  lcd.print("Hum: ");
  lcd.print(h);
  lcd.print("%");
}

void recordData() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  File dataFile = SD.open("/data.csv", FILE_APPEND);
  if(dataFile){
    dataFile.print(t);
    dataFile.print(",");
    dataFile.println(h);
    dataFile.close();
  }
}

void loop() {
  unsigned long currentMillis = millis();
  
  if (digitalRead(BUTTON_PIN) == LOW) {
    tryMountSDCard();
    delay(1000);  
  }
  
  if (digitalRead(BUTTON2_PIN) == LOW) {
    lcdStatus = !lcdStatus; 
    delay(1000);  
    if(lcdStatus) {
      lcd.backlight();
    } else {
      lcd.noBacklight();
    }
  }

  if (sdCardStatus && lcdStatus && currentMillis - lastRecordTime >= 3600000) {
    recordData();
    lastRecordTime = currentMillis;  
  }

  if (sdCardStatus && lcdStatus && currentMillis - lastDisplayUpdateTime >= 4000) {
    displayData();
    lastDisplayUpdateTime = currentMillis; 
  }

  if (!lcdStatus) {
    
  } else if(!sdCardStatus) {
    lcd.setCursor(0, 0);
    lcd.print("Check SD Card...  ");
    lcd.setCursor(0, 1);
    lcd.print("Press button to retry");
  }
}

```

#### 電気回路配線
![](../images/prototype/prototype4/circuit/circuit_1.JPG#center)


|    ESP32    | マイクロＳＤカードスロット |
| ----- | ----- | 
| 1   | 1 |  
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 

|    ESP32    | LCDディスプレイ |
| ----- | ----- | 
| 1   | 1 |  
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 


|    ESP32    | DHT22 |
| ----- | ----- | 
| 1   | 1 |  
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 
| 1 | 1 | 

