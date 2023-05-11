<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# IoT農業(春~秋)

![](../images/prototype/prototype3/field/field_1.JPG#center)

本プロジェクトは栗山町で農業を営んでいる國本さんにご協力いただきプロジェクトを進めさせていただいております。農業は天候に大きく影響されるため、天候が許す限り効率的に作業を行わなければならないことがあります。これにより、農業従事者は天候に合わせて急遽作業計画を変更し、多くの時間を作業に費やすことがとても多いことを問題視しております。

本プロジェクトを通して最終的に栗山町の農家さんが自由に使うことができる時間を増やしていきたいと思います。

## アイディアスケッチ

このプロジェクトは4つのスパイラルに分けることができます。

1stスパイラルでは、農業をする上での環境データの時間ごとの可視化ができるようにしたいと思います。

プロトタイプは以下の要件を満たします。

- SDカードで取得した環境データをスプレッドシートにデータを反映させて、グラフで温度変化を確認することができる。

- 防滴ができるレベルのケースを作成し電子機器を保護する、

![](../images/prototype/prototype3/idea_sketch/idea_sketch_1.jpg#center)



2ndスパイラルでは、遠隔地から農場の環境データをリアルタイムで取得できるようにしたいと思います。
![](../images/prototype/prototype3/idea_sketch/idea_sketch_2.jpg#center)

- 取得したデータはグラフを描画したWebアプリ上で確認できるようにします。


3rdスパイラルでは環境センサをトリガーとして、ビニールハウスの自動巻き取りなどアクチュエーターを動作させたいと思います。

![](../images/prototype/prototype3/idea_sketch/idea_sketch_3.jpg#center)

4thスパイラルでは[デジタルツイン](https://www.ntt.com/bizon/glossary/j-t/digital-twin.html#:~:text=%E3%83%87%E3%82%B8%E3%82%BF%E3%83%AB%E3%83%84%E3%82%A4%E3%83%B3%EF%BC%88DigitalTwin%EF%BC%89%E3%81%A8%E3%81%AF,%E3%81%99%E3%82%8B%E6%8A%80%E8%A1%93%E3%81%AE%E3%81%93%E3%81%A8%E3%81%A7%E3%81%99%E3%80%82)の実現をしていきたいと思います。

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


## 部品
|    部品    | 販売先 |  金額 | 必要個数 |   合計金額   | リンク |
| --------- | ----- | ---- | ------- | ----------- | ----- |
|   Sample  | Sample | 1000円 |   1  |     1000円   | ----- |

## 参考資料
- [スマート農業/IoT導入 農業ICTクラウドサービス アグリネットアドバンス](https://pr.nepon.co.jp/lp/a02)

- [IoT ガイド IoT向け無線通信技術 LPWA](https://jp.rs-online.com/web/generalDisplay.html?id=ideas-and-advice/iot-internet-of-things)
- [sigfoxとは](https://www.kccs.co.jp/sigfox/service/)
- [ESP32でのアナログ入力とSDカード装置](http://marchan.e5.valueserver.jp/cabin/comp/jbox/arc202/doc21104.html)
- [ESP32でmicroSDカードを読み書き](https://kanpapa.com/today/2023/01/esp32-otafab-study-microsd.html)
