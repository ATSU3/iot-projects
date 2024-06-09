# 技術構成

（WIP）

育苗期間中には、ビニールハウス内にトンネル（育苗のための2層目のビニールを被せる）を使用するため、ビニールハウス内（一層目）の温湿度に加えて、トンネル内の空気中の温度とトレーの下の温度のことも考慮して監視する必要があります。


温湿度センサで取得した値をSORACOM Harvest（データベース）に保存して、SORACOM Lagoon (ダッシュボード)で可視化しております。

![](../../../../images/agri/example/soracom/tech_stack/tech_stack_1.jpg#center)

DS18B20（温度センサ）はArduinoの2番ピンと3番ピンに接続しております。
DHT22（温湿度センサ）はArduinoの4番ピンに接続しております。
また、各センサは5V、GNDに接続しております。

![](../../../../images/agri/example/soracom/tech_stack/tech_stack_2.jpg#center)



## 部品

|    部品    | 販売先 |  金額 | 必要個数 |   合計金額   | リンク |
| ----------- | ------ | ---- | ------ | ---------- | ----- |
| Arduino Uno R3 | switch science | ¥3,960 | 1個 | ¥4,620 | [Link](https://www.switch-science.com/products/789) |
| DHT22（温湿度センサ） | Amazon | ¥1,241 | 2個 | ¥1,241 | [Link](https://www.amazon.co.jp/OSOYOO-DHT22-%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E6%B8%A9%E6%B9%BF%E5%BA%A6%E6%B8%AC%E5%AE%9A-Arduino%E3%82%84Raspberry-3%E9%9B%BB%E5%AD%90%E5%B7%A5%E4%BD%9C%E7%94%A8/dp/B07CSNMYN7) |
| DS18B20 防水型温度センサ サーミスタ温度コントローラ 長さ1M プローブ付き | Amazon | ¥988 | 5個 | ¥988 | [Link](https://www.amazon.co.jp/KKHMF-DS18B20-%E9%98%B2%E6%B0%B4%E5%9E%8B%E6%B8%A9%E5%BA%A6%E3%82%BB%E3%83%B3%E3%82%B5-%E3%82%B5%E3%83%BC%E3%83%9F%E3%82%B9%E3%82%BF%E6%B8%A9%E5%BA%A6%E3%82%B3%E3%83%B3%E3%83%88%E3%83%AD%E3%83%BC%E3%83%A9-%E3%83%97%E3%83%AD%E3%83%BC%E3%83%96%E4%BB%98%E3%81%8D/dp/B083TTCB9Q/ref=asc_df_B083TTCB9Q/?tag=jpgo-22&linkCode=df0&hvadid=553953805649&hvpos=&hvnetw=g&hvrand=9033018862678471370&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9175601&hvtargid=pla-1457190367600&psc=1&mcid=edc725e934593aaa8003b1b7c9289755) |
| LTE-M Shield for Arduino | SORACOM IoTストア | ¥7,128 | 1個 | ¥7,128 | [Link](https://www.switch-science.com/products/789) |
| 特定地域向け IoT SIM (plan-D)サイズ:ナノ | switch science | ¥902 | 1個 | ¥902 | [Link](https://soracom.jp/store/13380/) |


## サービス

|  サービス名   | 販売先 | 月額 | リンク |
| ----------- | ----- | ---- | ----- | 
| SORACOM Air for セルラー plan-D | SORACOM IoTストア | ¥330 | [Link](https://soracom.jp/services/air/) |
| SORACOM Lagoon（Makerプラン） | SORACOM IoTストア | ¥1,078 | [Link](https://soracom.jp/services/lagoon/) |
| SORACOM Harvest | SORACOM IoTストア | ¥300 | [Link](https://soracom.jp/services/harvest/) |