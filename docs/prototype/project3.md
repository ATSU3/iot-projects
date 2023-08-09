<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 温湿度計を作ってみる(IoT農業)

本プロジェクトでは以下のプロダクトを作成しました。
こちらは完成形になります。

![](../images/prototype/prototype3/prototype3.jpg#center)

## デバイスについて

### 基本機能
このデバイスは温湿度を測定し、ディスプレイに表示、またmicroSDカードにより、設定した時間ごとに温湿度を現在時刻と共にcsv形式で保存することができます。

### 追加機能
2つのボタンを追加しており、1つはLCDディスプレイのバックライトのOn/Offの操作、もう一方は、microSDカードを再マウントした際に、再度読み込み用のボタンになります。


## ボディ
- [ボディ_1(.stl)](../files/prototype3/ボディ_1.stl)

- [ボディ_2(.stl)](../files/prototype3/ボディ_2.stl)

![](../images/prototype/prototype3/body/body_1.jpg#center)

## プログラム

プログラム(コード解説含む)は以下のページから確認できます。
<br>
- [温湿度のデータを現在時刻と紐づけてSDカードに記録するプログラム](https://github.com/ATSU3/iot-agri_arduino/blob/main/arduino/prototype_vi/prototype_v1.ino)


## 電子回路

### 必要なパーツ
|    部品    |  個数  | URL |
| --------- | ------ | --- |
| マイクロＳＤカードスロットＤＩＰ化キット | 1 | [Link](https://akizukidenshi.com/catalog/g/gK-05488/)|
| マイクロＳＤカード | 1 | [Link](https://akizukidenshi.com/catalog/g/gS-15844/)|
| LCD ディスプレイモジュール | 1 | [Link](https://www.amazon.co.jp/WayinTop-%E3%83%87%E3%82%A3%E3%82%B9%E3%83%97%E3%83%AC%E3%82%A4%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB-16%C3%972%E3%82%AD%E3%83%A3%E3%83%A9%E3%82%AF%E3%82%BF-%E3%83%90%E3%83%83%E3%82%AF%E3%83%A9%E3%82%A4%E3%83%88%E4%BB%98%E3%81%8D-%E3%82%B7%E3%83%AA%E3%82%A2%E3%83%AB%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A7%E3%82%A4%E3%82%B9/dp/B07WTMXRTQ/ref=sr_1_25?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=23Z1OJ2Q8C0RA&keywords=%E3%83%9F%E3%83%8BLCD%E3%83%87%E3%82%A3%E3%82%B9%E3%83%97%E3%83%AC%E3%82%A4%2BArduino&qid=1683865746&sprefix=%E3%83%9F%E3%83%8Blcd%E3%83%87%E3%82%A3%E3%82%B9%E3%83%97%E3%83%AC%E3%82%A4%2Barduino%2Caps%2C182&sr=8-25&th=1) |
| DHT22 温度 湿度 センサー モジュール | 1 |[Link](https://www.amazon.co.jp/OSOYOO-DHT22-%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E6%B8%A9%E6%B9%BF%E5%BA%A6%E6%B8%AC%E5%AE%9A-Arduino%E3%82%84Raspberry-3%E9%9B%BB%E5%AD%90%E5%B7%A5%E4%BD%9C%E7%94%A8/dp/B07CSNMYN7/ref=sr_1_4?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=2XXJYMKFQDG41&keywords=DHT22&qid=1684202130&s=industrial&sprefix=dht22%2Cindustrial%2C190&sr=1-4) |
| ESP-WROOM-32E | 1 |[Link](https://akizukidenshi.com/catalog/g/gM-15673/) |
| タクトスイッチ | 2 | [Link](https://www.switch-science.com/products/38)|
| RTCモジュール| 1 |[Link](https://www.amazon.co.jp/WINGONEER-%E5%B0%8F%E5%9E%8BDS3231-AT24C32-I2C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%ABArduino%E7%94%A8%E3%81%AE%E9%AB%98%E7%B2%BE%E5%BA%A6%E3%83%AA%E3%82%A2%E3%83%AB%E3%82%BF%E3%82%A4%E3%83%A0%E3%82%AF%E3%83%AD%E3%83%83%E3%82%AF%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB/dp/B01H5NAFUY/ref=zg_bs_3332452051_sccl_1/357-7482134-7464160?psc=1) |

### 回路図
![](../images/prototype/prototype3/circuit/circuit_1.jpg#center)


### デバイス設置

ビニールハウスでトマトを育てている農家さんにご協力いただき、デバイスを設置させていただきました。ビニールハウス内の30分おきの温度湿度のデータを取得することができるか確認しました。

![](../images/prototype/prototype3/photo/photo_1.jpg#center)

![](../images/prototype/prototype3/photo/photo_2.jpg#center)

データは8/5 14:56から8/9 13:26までのデータを取得させていただきました。

- [data(.csv)](../files/prototype3/data.csv)



