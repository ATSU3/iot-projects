<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 育苗期間中の通い農家さんの課題を解決してみました

SORACOM IoT SIMを利用して、ビニールハウスの温湿度の遠隔監視を実現しました。
完成したデバイスは以下のものになります。

![](../images/prototype/prototype5/device/device_1.jpg#center)

農家さんの中には、自宅と農地が離れており、温度の確認のために移動を繰り返す手間があったり、一度現場に行った後には、ビニールハウスの温度が上がってしまい、苗を焼いてしまったりすることが気掛かりで現場を離れられないと行った声をいただいておりました。そこで、この課題を解決すべく、ビニールハウス内の温湿度の遠隔監視をできるデバイスの作成に取り掛かりました。

![](../images/prototype/prototype5/issue/issue_1.jpg#center)

ダッシュボードは、SORACOM Lagoonを使用して、以下のように温度、湿度を表示しております。詳細は[こちら](../農業/活用例/SORACOM/技術構成/measure_temp_and_humi.md)に記載しております。

![](../images/prototype/prototype5/soracom/soracom_1.jpg#center)

測定は2ヶ所のビニールハウスの温湿度の情報を同時に確認できるようにダッシュボードで可視化しております。

![](../images/prototype/prototype5/soracom/soracom_2.jpg#center)

通風筒はAutodesk Fusionでモデリングしました。

![](../images/prototype/prototype5/fusion/fusion_1.png#center)

温湿度センサDHT22を取り外すことができるように分割できるように作成しました。

![](../images/prototype/prototype5/fusion/fusion_2.png#center)

通風筒は一層ずつパーツを分けてプリントしました。

![](../images/prototype/prototype5/device/device_2.jpg#center)

重ねて積み上げていくことができます。

![](../images/prototype/prototype5/device/device_3.JPG#center)

センサは以下のように取り付けて、上部のパーツと固定してねじることで、接続できるような構造にしました。

![](../images/prototype/prototype5/device/device_4.JPG#center)

合わせるとこのような形になります。

![](../images/prototype/prototype5/device/device_5.JPG#center)

マイコンや配線の収納するボディパーツも3Dプリンタで作成しました。

![](../images/prototype/prototype5/device/device_7.JPG#center)

温湿度センサの配線は以下のように取り出しております。

![](../images/prototype/prototype5/device/device_8.JPG#center)

## コード

プログラムは以下のようになっております。

- [育苗期間中の3ヶ所の温度監視プログラム](https://github.com/ATSU3/green-house-project)

```
#define TINY_GSM_MODEM_BG96 // BG96 GSMモデルを使用するための定義
#include <TinyGsmClient.h> // TinyGSMライブラリの読み込み
#include <SoftwareSerial.h> // ソフトウェアシリアルライブラリの読み込み
#include <DHT.h> // DHTセンサーライブラリの読み込み
#include <OneWire.h> // OneWire通信ライブラリの読み込み
#include <DallasTemperature.h> // Dallas温度センサーライブラリの読み込み

#include <LowPower.h> // 電力消費を抑えるためのLowPowerライブラリの読み込み

#define CONSOLE Serial // SerialをCONSOLEとして定義
#define INTERVAL_MS 300000 // インターバルタイマーの設定（ミリ秒）
#define SERVER "harvest.soracom.io" // サーバーのアドレス
#define PORT 8514 // サーバーのポート番号

#define RX 10 // RXピン番号の定義
#define TX 11 // TXピン番号の定義
#define BAUDRATE 9600 // ボーレートの設定
#define BG96_RESET 15 // BG96のリセットピン番号

SoftwareSerial LTE_M_shieldUART(RX, TX); // ソフトウェアシリアルの設定
TinyGsm modem(LTE_M_shieldUART); // TinyGSMオブジェクトの初期化
TinyGsmClient client(modem); // TinyGsmClientオブジェクトの初期化

#define DHTPIN 4 // DHTセンサーのピン番号
#define DHTTYPE DHT22 // DHTセンサーのタイプ
DHT dht(DHTPIN, DHTTYPE); // DHTオブジェクトの初期化

#define TRAY_ONE_WIRE_BUS 2 // トレイ用ワンワイヤーバスのピン番号
#define TUNNEL_ONE_WIRE_BUS 3 // トンネル用ワンワイヤーバスのピン番号
OneWire oneWireTray(TRAY_ONE_WIRE_BUS); // トレイ用OneWireオブジェクト
OneWire oneWireTunnel(TUNNEL_ONE_WIRE_BUS); // トンネル用OneWireオブジェクト
DallasTemperature traySensor(&oneWireTray); // ビニールハウスのトンネル（2層目）のトレイ下の温度センサーオブジェクト
DallasTemperature tunnelSensor(&oneWireTunnel); // トンネル用温度センサーオブジェクト

void setup() {
  CONSOLE.begin(115200); // コンソール通信の開始
  LTE_M_shieldUART.begin(BAUDRATE); // シリアル通信の開始

  CONSOLE.println(F("Starting...")); // 開始のメッセージを出力

  pinMode(BG96_RESET, OUTPUT); // リセットピンを出力として設定
  digitalWrite(BG96_RESET, HIGH); // リセットピンをHIGHに設定
  delay(300); // 300ms待機
  digitalWrite(BG96_RESET, LOW); // リセットピンをLOWに設定
  delay(10000); // 10秒待機

  if (!modem.restart()) { // モデムのリスタートを試みる
    CONSOLE.println(F("Failed to restart modem")); // リスタート失敗時のメッセージ
    return; // 失敗時はsetup関数を終了
  }

  String modemInfo = modem.getModemInfo(); // モデム情報の取得
  CONSOLE.print(F("Modem: ")); // モデム情報の出力
  CONSOLE.println(modemInfo);

  if (!modem.gprsConnect("soracom.io", "sora", "sora")) { // GPRS接続の試み
    CONSOLE.println(F("Failed to connect")); // 接続失敗時のメッセージ
    return; // 失敗時はsetup関数を終了
  }

  CONSOLE.println(F("GPRS connected")); // GPRS接続成功時のメッセージ

  dht.begin(); // DHTセンサーの初期化
  traySensor.begin(); // ビニールハウスのトンネル（2層目）のトレイ下の温度センサーの初期化
  tunnelSensor.begin(); // ビニールハウスのトンネル（2層目）のトンネル用温度センサーの初期化
}

void loop() {
  if (!client.connect(SERVER, PORT)) { // サーバーへの接続試み
    CONSOLE.println(F("Connection to server failed")); // 接続失敗時のメッセージ
    delay(10000); // 10秒待機
    return; // 失敗時はloop関数の最初に戻る
  }

  CONSOLE.println(F("Connected to server")); // サーバー接続成功時のメッセージ

  traySensor.requestTemperatures(); // ビニールハウスのトンネル（2層目）のトレイ用センサーから温度を要求
  float trayTemp = traySensor.getTempCByIndex(0); // ビニールハウスのトンネル（2層目）のトレイの温度取得
  tunnelSensor.requestTemperatures(); // トンネル用センサーから温度を要求
  float tunnelTemp = tunnelSensor.getTempCByIndex(0); // ビニールハウスのトンネル（2層目）の温度取得
  float ambientTemp = dht.readTemperature(); // ビニールハウス一層目の温度の取得
  float humidity = dht.readHumidity(); // ビニールハウス1層目の湿度の取得

// JSON形式でデータを構築

  String payload = "{\"tray_temperature\":" + (isnan(trayTemp) ? "null" : String(trayTemp, 2)) +
                   ",\"tunnel_temperature\":" + (isnan(tunnelTemp) ? "null" : String(tunnelTemp, 2)) +
                   ",\"ambient_temperature\":" + (isnan(ambientTemp) ? "null" : String(ambientTemp, 2)) +
                   ",\"humidity\":" + (isnan(humidity) ? "null" : String(humidity, 2)) + "}"; 


  client.print(payload); // データのサーバーへの送信
  CONSOLE.println(F("Data sent: ")); // データ送信メッセージ
  CONSOLE.println(payload); // 送信データの出力

  client.stop(); // サーバー接続の終了
  CONSOLE.println(F("Connection closed")); // 接続終了メッセージ

  for (int i = 0; i < 38; i++) { // 低消費電力モードで8秒ずつスリープ
    LowPower.powerDown(SLEEP_8S, ADC_OFF, BOD_OFF); // スリープモードに入る
  }
}

```


## 電子回路

### 必要なパーツ
|    部品    |  個数  | URL |
| --------- | ------ | --- |
| Arduino Uno R3 | 1 | [Link](https://www.switch-science.com/products/789)|
| LTE-M Shield for Arduino | 1 | [Link](https://soracom.jp/store/5303/)|
| 特定地域向けIoT SIM（plan-D） | 1 | [Link](https://soracom.jp/store/13380/) |
| DHT22 温度 湿度 センサー モジュール | 1 |[Link](https://www.amazon.co.jp/OSOYOO-DHT22-%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E6%B8%A9%E6%B9%BF%E5%BA%A6%E6%B8%AC%E5%AE%9A-Arduino%E3%82%84Raspberry-3%E9%9B%BB%E5%AD%90%E5%B7%A5%E4%BD%9C%E7%94%A8/dp/B07CSNMYN7/ref=sr_1_4?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=2XXJYMKFQDG41&keywords=DHT22&qid=1684202130&s=industrial&sprefix=dht22%2Cindustrial%2C190&sr=1-4) |
| DS18B20 温度センサ | 2 |[Link](https://www.amazon.co.jp/DiyStudio-A30110793JP-3%E5%80%8B-%E3%82%BB%E3%83%83%E3%83%88-Arduino%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BCDS18B20%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AE%E9%98%B2%E6%B0%B4%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E3%82%B5%E3%83%BC%E3%83%9E%E3%83%AB%E3%83%97%E3%83%AD%E3%83%BC%E3%83%96%E3%81%BE%E3%81%9F%E3%81%AF%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BC%E3%83%A9%E3%83%9C%E6%B8%A9%E5%BA%A6%E6%B8%AC%E5%AE%9A%E6%9D%90%E6%96%99/dp/B07M886SBK?th=1) |


## 農家さんに使用していただいて


「毎日スマホ/タブレットで常にダッシュボードを確認して活用してます！」
「農業現場を離れていても、安心して別作業ができます！」
「農業現場の状況がどこからでも確認できるので、特に通い農家さんにおすすめしたいです！」
とありがたいお言葉をいただきました。


## 注意

農地にデバイスを長期で置いておくと土などで汚れたり、水分が付着して錆びてしまい、温湿度を正しく測定できない場合がありますので、定期的に取り外して、金属部分の汚れを落とす必要がありました。



![](../images/prototype/prototype5/device/device_6.jpg#center)

## ボディパーツ
- [ボディパーツまとめzipファイル[.stl]](../files/prototype5/device-body-parts.zip)



育苗期間終了後もハウス内の温湿度を取得するために引き続きビニールハウスに配置させていただいております。

# 育苗期間終了後の温度測定

## コード

```
#define CONSOLE Serial
#define INTERVAL_MS (300000) // 5分に一度
#define ENDPOINT "harvest.soracom.io"
#define PORT 8514
#define SKETCH_NAME "send_dht22_harvest_lagoon"
#define VERSION "1.1"

/* for LTE-M Shield for Arduino */
#define RX 10
#define TX 11
#define BAUDRATE 9600
#define BG96_RESET 15

#define TINY_GSM_MODEM_BG96
#include <TinyGsmClient.h>

#include <SoftwareSerial.h>
SoftwareSerial LTE_M_shieldUART(RX, TX);
TinyGsm modem(LTE_M_shieldUART);
TinyGsmClient client(modem);

#include <DHT.h>

// #define USE_DHT11 // Use DHT11 (Blue)
#define USE_DHT20 // Use DHT20 (Black)

#define DHTPIN 4
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

#define OLED_MAX_CHAR_LENGTH 16

#define RESET_DURATION 86400000UL // 1 day
void software_reset() {
  asm volatile ("  jmp 0");
}

void setup() {
  CONSOLE.begin(115200);
  LTE_M_shieldUART.begin(BAUDRATE);

  CONSOLE.print(F("Welcome to ")); CONSOLE.print(SKETCH_NAME); CONSOLE.print(F(" ")); CONSOLE.println(VERSION);
  delay(3000);

  CONSOLE.print(F("resetting module "));
  pinMode(BG96_RESET, OUTPUT);
  digitalWrite(BG96_RESET, LOW);
  delay(300);
  digitalWrite(BG96_RESET, HIGH);
  delay(300);
  digitalWrite(BG96_RESET, LOW);
  CONSOLE.println(F(" done."));

  modem.restart();
  delay(500);

  while (!modem.waitForNetwork());
  delay(500);

  modem.gprsConnect("soracom.io", "sora", "sora");
  delay(500);

  while (!modem.isNetworkConnected());

  delay(500);

  dht.begin();
}

float calculateDewPoint(float t, float h) {
  float a = 17.27;
  float b = 237.7;
  float alpha = ((a * t) / (b + t)) + log(h / 100.0);
  float dewPoint = (b * alpha) / (a - alpha);
  return dewPoint;
}

const float M_water = 18.01528;
const float R = 8.314;

float calculateVPD_gm3(float t, float h) {
  float SVP = 6.1078 * pow(10, (7.5 * t) / (237.3 + t)); // SVP(飽和水蒸気量) hPa
  float T_kelvin = t + 273.15; // 温度をケルビンに変換
  float SVD = 216.7 * (SVP / T_kelvin); // 飽和水蒸気量 g/m³
  float VPD = SVD * (1 - (h / 100)); // 飽差 g/m³
  return VPD;
}

void loop() {
  float t = dht.readTemperature();
  float h = dht.readHumidity();

  float dewPoint = calculateDewPoint(t, h);
  float vpd = calculateVPD_gm3(t, h);

  char t_str[10], h_str[10], dewPoint_str[10], vpd_str[10];
  dtostrf(t, 5, 1, t_str);
  dtostrf(h, 5, 1, h_str);
  dtostrf(dewPoint, 5, 1, dewPoint_str);
  dtostrf(vpd, 5, 1, vpd_str);

  char payload[120];
  sprintf_P(payload, PSTR("{\"temp_c\":%s,\"humi\":%s, \"dew_point\":%s, \"vpd\":%s}"), t_str, h_str, dewPoint_str, vpd_str);

  CONSOLE.println(payload);

  CONSOLE.print(F("Send..."));

  if (!client.connect(ENDPOINT, PORT)) {
    CONSOLE.println(F("failed."));
    delay(3000);
    return;
  }

  client.print(payload); // データのサーバーへの送信

  CONSOLE.println(F("Data sent: ")); // データ送信メッセージ
  CONSOLE.println(payload); // 送信データの出力
  client.stop();
  CONSOLE.println(F("done."));

  delay(INTERVAL_MS);

#ifdef RESET_DURATION
  if(millis() > RESET_DURATION )
  {
    CONSOLE.println("Execute software reset...");
    delay(1000);
    software_reset();
  }
#endif
}


```


## 参考URL
- [露点計算機](https://www.calculatorultra.com/ja/tool/dew-point-calculator.html)
- [飽差について](https://www.zero-agri.jp/about-saturation-deficit)
- [イトウさんのちょっとためになる農業情報](https://www.agri-note.jp/2018/06/fb-archive24/)
- [飽差とは? ハウス栽培に欠かせない指標を知り、収量アップを実現！](https://minorasu.basf.co.jp/80240)
- [気温と相対湿度から露点温度を求める（計算サイト）](https://keisan.casio.jp/exec/user/1326944648)



