<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 育苗期間中の通い農家さんの課題を解決してみました

本プロジェクトでは自宅と農地が離れている通い農家さんの課題を解決すべく、SORACOM IoT SIMを利用して、ビニールハウスの温湿度の遠隔監視を実現しました。
完成したデバイスは以下のものになります。

![](../images/prototype/prototype5/device/device_1.jpg#center)

ダッシュボードは、SORACOM Lagoonを使用して、以下のように温度、湿度を表示しております。詳細は[こちら](../農業/活用例/SORACOM/技術構成/measure_temp_and_humi.md)に記載しております。

![](../images/prototype/prototype5/soracom/soracom_1.jpg#center)


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

## コード
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

## ボディパーツ
- [ボディパーツまとめzipファイル[.stl]](../files/prototype5/device-body-parts.zip)

