<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# IoT農業(春~秋)

![](../images/prototype/prototype3/field/field_1.JPG#center)

本プロジェクトは栗山町で農業を営んでいる國本さんにご協力いただきプロジェクトを進めさせていただいております。農業は天候に大きく影響されるため、天候が許す限り効率的に作業を行わなければならないことがあります。これにより、農業従事者は天候に合わせて急遽作業計画を変更し、多くの時間を作業に費やすことがとても多いことを問題視しております。
また、國本さんの農場は自宅から10km以上離れており、悪天候などの場合、経験に基づいて農場に問題が生じていないかを確認するためだけに現地に出向くことがよくあります。これはかなりの時間を消費し、効率的ではありません。

そのため、本プロジェクトを通して最終的に栗山町の農家さんが自由に使うことができる時間を増やしていきたいと思います。

## アイディアスケッチ

このプロジェクトは大きく分けて4つのスパイラルに分けております。

### 1stスパイラル
1stスパイラルでは、まず國本さんが最も必要としている環境データの可視化で、ビニールハウス内で最も高い温度、低い温度を知ること、また時間ごとに温度変化のログをとり、温度の移り変わりを可視化することを行なっていこうと思います。

ビニールハウス内での最高・最低温度の把握、時間ごとの温度変化のログ、及びその可視化は、農業の管理と最適化に多くのメリットがあると考えられます。

- 適切な成長条件の維持
異なる作物は、最適な生育温度という異なる成長条件を持っています。温度ログを取ることで、それぞれの作物にとって最適な温度を維持することができ、それにより収穫量と品質を最大化することが可能になります。

- リスク管理
異常な気温の変動や予期しない寒波・熱波は、作物に重大なダメージを与える可能性があります。温度の監視により、これらのリスクを早期に検知し、適切な対策を講じることが可能になります。

- データ駆動型の意思決定
温度のログとその可視化は、過去のパターンを理解し、未来の予測も行うのに役立つと考えられます。これにより、いつ種をまくべきか、いつ収穫すべきか等、より効果的な意思決定を行うことが可能になります。

- 資源の効率的な利用
温度の監視と調整により、現在使用されている電熱線のエネルギー消費量を最適化することができます。これは、環境に優しく、経済的にも効果的です。

- 研究と学習
國本さん自身も農業のための環境データを基に、最適な条件を特定したりと研究や学習をされているため、それらのデータのログを残していくためにお力になりたいと思います。


プロトタイプは以下の要件を満たします。


- SDカードで取得した環境データをスプレッドシートにデータを反映させて、グラフで温度変化を確認することができる。

- 防滴ができるレベルのケースを作成し電子機器を保護することができる。

![](../images/prototype/prototype3/idea_sketch/idea_sketch_1.jpg#center)

#### 必要なパーツ

|    部品    | 
| --------- | 
| マイクロＳＤカードスロットＤＩＰ化キット  | 
| マイクロＳＤカード | 
| LCD ディスプレイモジュール | 
| 土壌湿度計 湿度検出モジュール | 
| DHT22 温度 湿度 センサー モジュール | 
| ESP-WROOM-32E | 

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

##### プログラム解説

以下のライブラリを読み込みます。
```c++
#include "FS.h"
#include "SD.h"
#include "SPI.h"
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>
```
SDカードにデータを書き込むために、"FS.h"、"SD.h"、"SPI.h"を読み込み
LCDに情報を表示するため、Wire.h、LiquidCrystal_I2C.hを読み込み、
DHT22から温湿度を取得するため、DHT.h
を読み込んでおります。


```c++
#define DHTPIN 2
#define DHTTYPE DHT22
#define SCK 18  //SDカードのCLKピン
#define MISO 19 //SDカードのDAT0ピン
#define MOSI 23 //SDカードのCMDピン
#define SS 4    //SDカードのCD/DAT3ピン
#define BUTTON_PIN 15  
#define BUTTON2_PIN 16 
```





```c++

// 土壌管理

#include "FS.h"
#include "SD.h"
#include "SPI.h"
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

#define SOIL_MOISTURE_SENSOR_PIN 34  
#define SCK 18  //SDカードのCLKピン
#define MISO 19 //SDカードのDAT0ピン
#define MOSI 23 //SDカードのCMDピン
#define SS 5    //SDカードのCD/DAT3ピン
#define BUTTON_PIN 15  //ボタンが接続されているピン
#define BUTTON2_PIN 16 //新たなボタンが接続されているピン

LiquidCrystal_I2C lcd(0x27, 16, 2);
volatile bool sdCardStatus = false;
volatile bool mountSDCard = false;  
bool lcdStatus = true; // LCDの電源状態

unsigned long lastRecordTime = 0;  
unsigned long lastDisplayUpdateTime = 0;  

void setup() {
  lcd.init();
  lcd.backlight();

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(BUTTON2_PIN, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(BUTTON_PIN), flagMountSDCard, FALLING);

  SPI.begin(SCK, MISO, MOSI, SS);  // Add this
  tryMountSDCard();
}

void flagMountSDCard() {
  mountSDCard = true;
}

void tryMountSDCard() {
  lcd.clear();
  if(!SD.begin(SS, SPI)){  
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
  mountSDCard = false;
}

void displayData() {
  int moisture = analogRead(SOIL_MOISTURE_SENSOR_PIN);

  lcd.setCursor(0, 0);
  lcd.print("Soil M: ");
  lcd.print(moisture);
}

void recordData() {
  int moisture = analogRead(SOIL_MOISTURE_SENSOR_PIN);

  File dataFile = SD.open("/data.csv", FILE_APPEND);
  if(dataFile){
    dataFile.println(moisture);
    dataFile.close();
  }
}

void loop() {
  unsigned long currentMillis = millis();
  
  if (mountSDCard) {
    tryMountSDCard();
    delay(1000);  // Debounce
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

  if (sdCardStatus && lcdStatus && currentMillis - lastRecordTime >= 5000) {
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

```c++

// DS18B20デジタル温度センサー

#include "FS.h"
#include "SD.h"
#include "SPI.h"
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <OneWire.h>
#include <DallasTemperature.h>

#define ONE_WIRE_BUS 2 // DS18B20のデータ線が接続されているピン
#define BUTTON_PIN 15  // ボタンが接続されているピン
#define BUTTON2_PIN 16 // 新たなボタンが接続されているピン

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);
LiquidCrystal_I2C lcd(0x27, 16, 2);
volatile bool sdCardStatus = false;
bool lcdStatus = true; // LCDの電源状態

unsigned long lastRecordTime = 0;  
unsigned long lastDisplayUpdateTime = 0;  

void setup() {
  lcd.init();
  lcd.backlight();
  sensors.begin();

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
  sensors.requestTemperatures(); 
  float t = sensors.getTempCByIndex(0);

  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(t);
  lcd.print("C");
}

void recordData() {
  sensors.requestTemperatures();
  float t = sensors.getTempCByIndex(0);

  File dataFile = SD.open("/data.csv", FILE_APPEND);
  if(dataFile){
    dataFile.println(t);
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

  if (sdCardStatus && lcdStatus && currentMillis - lastRecordTime >= 10000) {
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

### 2ndスパイラル
2ndスパイラルでは、確認のために現場に直接行く頻度を削減するため、遠隔地から農場の環境データをリアルタイムで取得できるようにしたいと思います。また、國本さんの自宅から農場までは10km以上の距離があるため、農場の環境データをリアルタイムで取得することができれば、温度が問題ない場合、ビニールハウスの状態を確認するためだけに現場に向かう必要がなくなり、効率化を図ることができます。


よって、1stスパイラルのメリットに加え、以下のことができるようになります。

- リアルタイムの意思決定
データがリアルタイムで取得できると、問題が発生した場合にすぐに対応することが可能となります。例えば、温度が急に上昇した場合や、湿度が一定の閾値を下回った場合など、状況に応じてすぐに対策を講じることができます。

- 遠隔地からの監視
物理的に現場にいることなく、農場の状況を監視することができます。これは、自宅から農場まで距離がある場合や大規模な農場や複数の農場を管理している場合は特に有用です。

![](../images/prototype/prototype3/idea_sketch/idea_sketch_2.jpg#center)

取得したデータはグラフを描画したWebアプリ上で確認できるようにします。


また、ビニールハウス内では、さまざまな種類の野菜を育てているため、区画ごとに異なる温湿度を設定されているため、区画ごとにセンサーを配置して温度を取得することが理想的です。応用例として、ESP-NOW通信を用いてスレーブ側で設定したマイコンで取得したセンサデータをマスター側のマイコンに送信し、一括でデータを保存しLPWAの通信によりデータを送ることを考えております。

![](../images/prototype/prototype3/idea_sketch/idea_sketch_5.jpg#center)

ESP-NOW通信を用いることで、Wi-Fiルーターなどを経由せずマイコン同士で通信を行うことができ、MACアドレスを指定しての1対1の通信やブロードキャストでの1対多の一斉送信も行うことができるためこの状況下で効率よく通信ができます。


#### 必要なパーツ
|    部品    | 
| --------- | 
| M5Stack Basic V2.6  | 
| M5Stack用Sigfoxモジュール | 

### 3rdスパイラル(2023年8月1日 ~ 8月31日)
3rdスパイラルではさらに発展させ、現場に直接行かなくて良いように、環境センサをトリガーとして、ビニールハウスの自動巻き取りなどアクチュエーターを動作させたいと思います。

これにより、1st、2ndスパイラルのメリットに加え、以下のことができるようになります。
- 自動化による労力削減
環境センサによってトリガーされるアクチュエーターは、人間の介入を必要とせずに設備を自動的に操作することができます。これにより、手動での管理や監視の労力を大幅に削減できます。

- 迅速な対応
センサがリアルタイムでデータを提供することにより、環境の変化に対するアクチュエーターの反応時間が大幅に短縮されます。これは、突然の天候の変化や環境条件の変動に対して迅速に対応することを可能にします。

- 自動化による生産性の向上
自動化により、人間が行っていた作業を機械が行うことで生産性が向上します。また、人間のエラーを排除することで作業の品質も向上します。

- 精密な制御
センサーとアクチュエーターを連動させることで、農場の環境条件を細かく調節することが可能となります。これにより、作物の成長に最適な条件を維持することができ、生産量や品質を向上させることが可能となります。

![](../images/prototype/prototype3/idea_sketch/idea_sketch_3.jpg#center)


### 4thスパイラル(2023年9月1日 ~ 2023年9月30日)

4thスパイラルでは[デジタルツイン](https://www.ntt.com/bizon/glossary/j-t/digital-twin.html#:~:text=%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E3%83%84%E3%82%A4%E3%83%B3%EF%BC%88DigitalTwin%EF%BC%89%E3%81%A8%E3%81%AF,%E3%81%99%E3%82%8B%E6%8A%80%E8%A1%93%E3%81%AE%E3%81%93%E3%81%A8%E3%81%A7%E3%81%99%E3%80%82)の実現をしていきたいと思います。

- 予測モデリングとシミュレーション
デジタルツインは、現実世界の農場の正確なデジタルの複製を提供することができるため、様々なシナリオや条件下での作物の成長を予測し、シミュレーションすることが可能となります。

- リアルタイムの意思決定
デジタルツインは、農場のリアルタイムの状態を反映します。これにより、現在の状況に基づいた迅速で効果的な意思決定を行うことができます。

- リスク管理
デジタルツインを用いて様々なシナリオをテストすることで、リスクを予測し、その影響を最小化する適切な対策を事前に計画することができます。


![](../images/prototype/prototype3/idea_sketch/idea_sketch_4.jpg#center)

## 通信方式
IoTのプロダクトを開発する際は選択肢がたくさんあり、以下の表のように近距離か遠距離か、また、低速低消費か高速高消費か、用途によって適したものを使い分ける必要がある。

[IoT ガイド IoT向け無線通信技術 LPWA 参照](https://jp.rs-online.com/web/generalDisplay.html?id=ideas-and-advice/iot-internet-of-things)
![](../images/prototype/prototype3/ communication/communication_1.jpg#center)

今回のIoT農業の場合、以下の理由からLPWAを用いてプロダクトを作成していきたいと思います。また、LoRaWAN、Sigfoxを用いていく予定です。

- 広範囲のカバレッジ
農業IoTは、広大な畑やビニールハウスでのセンサーデータ収集が求められるため、長距離通信が可能なLPWA技術が適しているため。

- 低消費電力
農業IoTデバイスは、電源供給が限られる場所で運用されることが多く、省電力な通信技術が必要であるが、LPWAは低消費電力で動作するため、バッテリ寿命の長いIoTデバイスに適しているため。

- 低コスト
農業IoTでは、多数のセンサーやデバイスが必要になることがありますが。LPWA技術は、低コストでデバイスやネットワークの導入・運用が可能であるため、コストを抑えることができるため。

- 信頼性と拡張性
LPWA技術は、干渉や遮蔽に対して強く、信頼性の高い通信が可能であり、また、IoTデバイスの数が増えても、ネットワークを拡張しやすい特徴があるため。


<!-- ## 部品
|    部品    | 販売先 |  金額 | 必要個数 |   合計金額   | リンク |
| --------- | ----- | ---- | ------- | ----------- | ----- |
|   Sample  | Sample | 1000円 |   1  |     1000円   | ----- | -->

## 参考資料
- [スマート農業/IoT導入 農業ICTクラウドサービス アグリネットアドバンス](https://pr.nepon.co.jp/lp/a02)

- [IoT ガイド IoT向け無線通信技術 LPWA](https://jp.rs-online.com/web/generalDisplay.html?id=ideas-and-advice/iot-internet-of-things)
- [sigfoxとは](https://www.kccs.co.jp/sigfox/service/)
- [ESP32でのアナログ入力とSDカード装置](http://marchan.e5.valueserver.jp/cabin/comp/jbox/arc202/doc21104.html)
- [ESP32でmicroSDカードを読み書き](https://kanpapa.com/today/2023/01/esp32-otafab-study-microsd.html)
- [ESP-NOW](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/network/esp_now.html)

## 使用データ
- [3D Data](https://grabcad.com/library/1602-lcd-display-1)

