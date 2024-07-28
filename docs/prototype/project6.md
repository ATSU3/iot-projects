<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 温湿度管理システムを作成してみた

ESP-NOW通信を使用することで、
<br>
- WIFIが使用できない屋外でSIMカードなども使用せずに通信できること
<br>
- 省電力で駆動するため、電池駆動をすることができること
<br>
がメリットとして挙げられます。

今回は以下の図のようなシステムを作成していきたいと思います。
![](../images/prototype/prototype6/sketch/sketch_1.jpg#center)

各ESP32デバイス(温度データを送信する子機)からESP32デバイス（温度データを受信する親機）にESP-NOW通信(Wi-Fiネットワークを使用せずに、デバイス間で直接データを送受信する通信)を使用してデータを送ります。

ESP32によるESP-NOW通信でのデータ送受信を行うメリットは、
<br>
- ESP32はDeepSleepモードを活用することで、長期間の電池駆動が可能であること。
<br>
これにより、農地に設置したセンサーやデバイスが頻繁なバッテリー交換を必要とせず、メンテナンスが容易になります。
<br>
- MACアドレスを使って直接通信することができ、Wi-Fiネットワークを介さないため、低遅延でのデータ送受信が可能であること
<br>
が挙げられます。

## ESP-NOW

![](../images/prototype/prototype6/device/device_1.JPG#center)

![](../images/prototype/prototype6/display/display_1.jpg#center)


MACアドレスの確認

```
#include "WiFi.h"

void setup() {
  Serial.begin(115200);
  Serial.print("WiFi MAC Address:");
  Serial.println(WiFi.macAddress());
}

void loop() {
  Serial.print("WiFi MAC Address:");
  Serial.println(WiFi.macAddress());
  delay(5000);
}

```

ESP-NOW 受信プログラム

```
#include <esp_now.h>
#include <WiFi.h>
float temperature;

// データ受信時に実行されるコールバック関数
void OnDataRecv(const uint8_t *mac, const uint8_t *incomingData, int len) {
  memcpy(&temperature, incomingData, sizeof(temperature));
  Serial.print("Received Temperature: ");
  Serial.println(temperature);
}

void setup() {
  // シリアルモニタを初期化、通信速度115200bps
  Serial.begin(115200);
  
  WiFi.mode(WIFI_STA); // デバイスをWi-Fiステーションモードに設定

  // ESP-NOWの初期化
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }

  // データ受信時のコールバック関数を登録
  esp_now_register_recv_cb(OnDataRecv);
}

void loop() {
  // メインループでは特に処理を行わない
}

```

ESP-NOW 送信プログラム

```
#include <esp_now.h>
#include <WiFi.h>
#include <Wire.h>
#include "Adafruit_SHT31.h"

// SHT31センサーのインスタンスを作成
Adafruit_SHT31 sht31 = Adafruit_SHT31();

// 送信先のMACアドレス（こちらに取得したMACアドレスを入力してください）
uint8_t broadcastAddress[] = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};

// ピア情報を保持する構造体
esp_now_peer_info_t peerInfo;

// 送信するデータを保持する構造体
struct DataPacket {
  int area_id;
  float temperature;
};

// 送信するデータ
DataPacket dataToSend;

// データ送信後に実行されるコールバック関数
void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
  Serial.print("\r\nLast Packet Send Status:\t");
  Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Delivery Success" : "Delivery Fail");
}

void setup() {
  // シリアルモニタを初期化、通信速度115200bps
  Serial.begin(115200);
  
  // SHT31センサーを初期化
  if (!sht31.begin(0x45)) {
    Serial.println("Couldn't find SHT31");
    while (1) delay(1);
  }
  
  // デバイスをWi-Fiステーションモードに設定
  WiFi.mode(WIFI_STA);

  // ESP-NOWの初期化
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }

  // データ送信後のコールバック関数を登録
  esp_now_register_send_cb(OnDataSent);
  
  // ピア情報を設定
  memcpy(peerInfo.peer_addr, broadcastAddress, 6);
  peerInfo.channel = 0;  
  peerInfo.encrypt = false;
  
  // ピアを追加
  if (esp_now_add_peer(&peerInfo) != ESP_OK) {
    Serial.println("Failed to add peer");
    return;
  }

  // area_idを設定
  dataToSend.area_id = 1;
}

void loop() {
  // SHT31センサーから温度を取得
  dataToSend.temperature = sht31.readTemperature();
  if (!isnan(dataToSend.temperature)) {  // 温度が正常に読み取れた場合
    Serial.print("Temperature: ");
    Serial.println(dataToSend.temperature);
    
    // 温度データとエリアIDを送信
    esp_now_send(broadcastAddress, (uint8_t *)&dataToSend, sizeof(dataToSend));
  } else {
    Serial.println("Failed to read temperature");
  }
  delay(5000); // 1秒間隔でデータを送信
}

```


### 参考資料

[RaspberryとArduinoをシリアル通信する](http://soup01.com/ja/2020/08/12/raspberry-arduino-serialcomm/)
[【シリアル通信〜基礎編】USBを繋いでArduino Unoとラズパイ間で簡単なシリアル通信を行ってみる](https://geek.tacoskingdom.com/blog/30)
