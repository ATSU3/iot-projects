<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 楽器を作ってみる (WIP)

本プロジェクトは[木箱-kibaco-さん](https://kitorina.com/wp/kibaco/)にご協力いただき進めているプロジェクトになります。

## ステッピングモータを楽器として利用する
ステッピングモーターは時計の秒針のように、一定の角度ずつ回転するモーターです。
ステッピングモーターが回転する際に発生する振動を利用して音を鳴らすことができます。

<!-- ステッピングモーターから発生する音で楽器を作成する。 -->

<!-- kibakoの西村さんとのアイディアスケッチを参考に作成していく。 -->

<!--  -->

### 評価

- 


### チェックリスト
- 本プロトタイプはステッピングモータを利用して


## データ

## 部品
|    部品    | 販売先 |  金額 | 必要個数 |   合計金額   | リンク |
| ----------- | ------ | ---- | ------ | ---------- | ----- |
| Arduino Uno R3 | switch science | ¥3,960 | 1個 | 1個 | [Link](https://www.switch-science.com/products/789) |
| CNC Shield | Amazon | ¥689 |   1個   |    1個     | [Link](https://www.amazon.co.jp/KKHMF-%E6%8B%A1%E5%BC%B5%E3%83%9C%E3%83%BC%E3%83%89-A4988-%E3%83%89%E3%83%A9%E3%82%A4%E3%83%90%E3%83%BC%E3%83%9C%E3%83%BC%E3%83%89-Arduino%E3%81%A8%E4%BA%92%E6%8F%9B/dp/B088FLTQ2K/ref=asc_df_B088FLTQ2K/?tag=jpgo-22&linkCode=df0&hvadid=589001997787&hvpos=&hvnetw=g&hvrand=3902595438584672939&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1009052&hvtargid=pla-1655365521301&psc=1) |
| ステッピングモータ(Stepper Motor) Nema 17 Bipolar 40mm | Amazon | ￥3,378 | 3個 | ￥10134 | [Link](https://www.amazon.co.jp/%E3%82%B9%E3%83%86%E3%83%83%E3%83%94%E3%83%B3%E3%82%B0%E3%83%A2%E3%83%BC%E3%82%BF-Stepper-Motor-Bipolar-Printer/dp/B00PNEQI7W?th=1) |
| DRV8825 4層 ステッパ モータ ドライバ モジュール | Amazon | ￥180 | 3個 | 3個 | [Link](https://www.amazon.co.jp/KOZEEY-%E3%83%92%E3%83%BC%E3%83%88%E3%82%B7%E3%83%B3%E3%82%AF%E4%BB%98%E3%81%8D-DRV8825-4%E5%B1%A4-%E3%82%B9%E3%83%86%E3%83%83%E3%83%91-%E3%83%A2%E3%83%BC%E3%82%BF-%E3%83%89%E3%83%A9%E3%82%A4%E3%83%90-%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB-3D%E3%83%97%E3%83%AA%E3%83%B3%E3%82%BF%E5%AF%BE%E5%BF%9C-%E4%BA%A4%E6%8F%9B%E6%80%A7-%E4%BE%BF%E5%88%A9%E6%80%A7-%E9%AB%98%E6%80%A7%E8%83%BD-%E5%AE%89%E5%AE%9A%E6%80%A7/dp/B075462FBC) |
| AC to DC 12V 2A アダプター | Amazon | ￥1,398 | 1個 | 1個 | [Link](https://www.amazon.co.jp/%E6%B1%8E%E7%94%A8AC%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC-%E6%9C%80%E5%A4%A7%E5%87%BA%E5%8A%9B24W-%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81%E3%83%B3%E3%82%B0%E5%BC%8F-%E9%9B%BB%E6%BA%90%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC-%E3%83%86%E3%83%BC%E3%83%97%E3%83%A9%E3%82%A4%E3%83%88/dp/B07D74RXPK/ref=sr_1_2_sspa?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=HIZU9BZDZHOH&keywords=ac+to+ac%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC+12v+2a&qid=1677467574&sprefix=ac+to+d%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC+12v+2a%2Caps%2C219&sr=8-2-spons&psc=1&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFDWVY5UTc5R1lGQzImZW5jcnlwdGVkSWQ9QTAzMjM1MzgzQlgwV1BLOFlURklXJmVuY3J5cHRlZEFkSWQ9QTI5TTZBTkVWMkk4Wlcmd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl) |


## インストールするソフト・ライブラリ一覧
- [FLIP](https://www.microchip.com/en-us/development-tool/flip#)

- [mocolufa](https://github.com/kuwatay/mocolufa)

- [Arduino-MIDI-Stepper-Motor-Instrument](https://github.com/jzkmath/Arduino-MIDI-Stepper-Motor-Instrument)


## 参考資料
- [ステッピングモーターの概要と特徴](https://www.orientalmotor.co.jp/products/stepping/overview_1/)

- [FORUM Arduino(CNC shield V3 A4988 Motors Z and A not move)](https://forum.arduino.cc/t/cnc-shield-v3-a4988-motors-z-and-a-not-move/484088)

- [【Arduino】Arduino UNOをMIDIコントローラにする](https://monorepi.jp/archives/1404)

- [Arduino MIDI Stepper Synth](https://www.hackster.io/JonJonKayne/arduino-midi-stepper-synth-d291ae)

- [Arduino-MIDI-Stepper-Motor-Instrument](https://github.com/jzkmath/Arduino-MIDI-Stepper-Motor-Instrument)

- ["楽器"としてのステッピングモーター入門](https://tlo-olb.hatenablog.com/entry/2019/03/22/191524)

