<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# ラボ内の在庫管理を自動化してみる (WIP)

ラボ内では多くの電子パーツあり、在庫管理に非常に多くの時間を要するため、自動化することができないかどうか考えました。
在庫はスプレッドシートで管理しているため、各棚と対象のスプレッドシートの各パーツの在庫数が同期されるような仕組みを作り自動化することができたら作業が大幅に効率化されると考えました。

![](../images/prototype/prototype1/idea_sketch/idea_sketch.jpg#center)

今回はラボ内の電子パーツが限られているため、第一段階目として、上記のように収納箱の前面に+ボタン、-ボタンを取り付け、使った分+,-のボタンを押すことでスプレッドシートに数を反映させるプロトタイプを作成します。

![](../images/prototype/prototype1/idea_sketch/idea_sketch_2.jpg#center)

## スプレッドシートをRaspberryPi&タクトスイッチを用いてpythonで操作する

### Google Cloud Platformの設定

[Google Cloud PlatformでPythonを用いてスプレッドシートを操作する方法](/basic/setting/gcp_spreadsheet.html#google-cloud-platformgcp)に詳細な設定方法を記載しております。

### Spreadsheet設定
今回はプロトタイプのため、部品名とその個数を入力するシートを作成します。また対応する2つの収納箱を作成します。
![](../images/prototype/prototype1/spreadsheet/spreadsheet_1.jpg#center)

### 電子回路

電子回路は以下の図のような構成にしております。RapsberrypiのGPIO2, 3, 4, 5を各タクトスイッチと接続しております。また、タクトスイッチはRaspberryPi内部のプルアップ抵抗とも接続しております。

![](../images/prototype/prototype1/circuit/circuit_1.jpg#center)

### 配線接続
|    GPIO   | タクトスイッチ | 
| --------- | ----------- |
| 2 | タクトスイッチ1 (+ボタン) |
| 3 | タクトスイッチ2 (-ボタン) |
| 4 | タクトスイッチ3 (+ボタン) |
| 5 | タクトスイッチ4 (-ボタン) |

実際の写真は以下のものになります。
![](../images/prototype/prototype1/circuit/circuit_5.jpg#center)

![](../images/prototype/prototype1/circuit/circuit_3.jpg#center)

RaspberryPiの公式ドキュメンテーションよりGPIOのピン番号が確認できます。
![](../images/prototype/prototype1/circuit/circuit_4.jpg#center)
出典: [Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/computers/os.html)

### Python

#### Python環境構築

- Raspberry Pi 3B
    - ディストリビューション: Raspbian GNU/Linux 11 (bullseye)
    - python version 3.9.2

対象のルートディレクトリに仮想環境を作成する。
```bash
$ python3 -m venv .env
```

venvをactivate
```bash
$ source .env/bin/activate
```

今回はpythonでスプレッドシートを操作するため、gspreadと、Oauth認証関連のoauth2clientを使用します。

```bash
$ pip install gspread oauth2client
```

#### Pythonプログラム

以下のPythonプログラムの解説に関してはこちらの記事に記載しております。

- [Pythonを用いたスプレッドシートのセルの操作](/basic/setting/gcp_spreadsheet.html#python#python)

- [Raspberry Pi GPIOに関して](/basic/programming/RaspberryPi/raspberrypi.html#gpio)

```python
import gspread
import time
import RPi.GPIO as GPIO
from oauth2client.service_account import ServiceAccountCredentials

scope = ['https://spreadsheets.google.com/feeds','https://www.googleapis.com/auth/drive']
c = ServiceAccountCredentials.from_json_keyfile_name('対象のjsonファイル', scope)

gs = gspread.authorize(c)

SPREADSHEET_KEY = 'スプレッドシートキー'
worksheet = gs.open_by_key(SPREADSHEET_KEY).worksheet("stock")

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BCM)
GPIO.setup(2, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(3, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(4, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(5, GPIO.IN, pull_up_down=GPIO.PUD_UP)

while True:
    val_1 = ""
    val_2 = ""
    if GPIO.input(2) == GPIO.LOW:
        val_1 = worksheet.acell("B2").value
        val1 = int(val_1) + 1
        worksheet.update('B2', val1)
        time.sleep(0.1)
    elif GPIO.input(3) == GPIO.LOW:
        val_1 = worksheet.acell("B2").value
        val1 = int(val_1) - 1
        worksheet.update('B2', val1)
        time.sleep(0.1)
    elif GPIO.input(4) == GPIO.LOW:
        val_2 = worksheet.acell("B3").value
        val2 = int(val_2) + 1
        worksheet.update('B3', val2)
        time.sleep(0.1)
    elif GPIO.input(5) == GPIO.LOW:
        val_2 = worksheet.acell("B3").value
        val2 = int(val_2) - 1
        worksheet.update('B3', val2)
        time.sleep(0.1)
```


### ボディ設計

Fusion360で以下のように収納ケースを作成しました。

![](../images/prototype/prototype1/fusion360/fusion360_1.jpg#center)

![](../images/prototype/prototype1/fusion360/fusion360_2.jpg#center)

作成したデータはDXF形式で出力して、加工はMDF2.5mmを使用しレーザーカッターを用いて行いて行いました。データは[こちら](../files/prototype1/case.pdf)からダウンロードできます。

電子回路は以下のように半田付けして格納しております。
![](../images/prototype/prototype1/circuit/circuit_2.jpg#center)

### 評価

以下の動画のように+,-ボタンを押した回数分、カウントアップ、カウントダウンし、値がリアルタイムでスプレッドシートに反映することができました。

<video width="100%" height="400px" id="center" controls>
  <source src="../videos/test/project1/prototype_1.mp4" type="video/mp4">
</video>

ここまでで第一段階のプロトタイプの作成を終了します。

## データ
- [case.ai](../files/prototype1/case.ai)
- [case.pdf](../files/prototype1/case.pdf)

## 部品
|    部品    | 販売先 |  金額 | 必要個数 |   合計金額   | リンク |
| --------- | ------ | ---- | ------ | ---------- | ----- |
| RaspberryPi3B | digikey | 4804円 | 1個 | 4804円 | [Link](https://www.digikey.jp/ja/products/detail/raspberry-pi/RASPBERRY-PI-3/6152799?s=N4IgTCBcDaIIwDYCcAGAtHFW0DkAiIAugL5A&src=raspberrypi) |
| タクトスイッチ | 共立エレショップ | 55円 | 4個 | 220円 | [Link](https://eleshop.jp/shop/g/gF8K123/) |
| ジャンパワイヤ(オス - メス) PRT-12794 | digikey | 288円| 1セット | 288円 | [Link](https://www.digikey.jp/ja/products/detail/sparkfun-electronics/PRT-12794/5993859?utm_adgroup=Jumper%20Wir&utm_source=google&utm_medium=cpc&utm_campaign=Google%20Shopping_Product_Prototyping%2C%20Fabrication%20Products&utm_term=&productid=5993859&gclid=Cj0KCQiA8t2eBhDeARIsAAVEga2WpJF2fZCiK6KCYi6duY9cQ6ShpnppK59_f4vvOBICh0MZELYKF7waAqUkEALw_wcB) |
| ブレッドボード | digikey | 542円 | 1個 | 542円 | [Link](https://www.digikey.jp/ja/products/detail/adafruit-industries-llc/65/7241428?utm_adgroup=Solderless%20Breadboards&utm_source=google&utm_medium=cpc&utm_campaign=Google%20Shopping_Product_Prototyping%2C%20Fabrication%20Products&utm_term=&productid=7241428&gclid=CjwKCAiAleOeBhBdEiwAfgmXf_ufodj00MLtWjrT7go4rg3LYam2UNdP2BIpQ75UDdoD-dOHOHZnERoCb1YQAvD_BwE) |

## 参考資料
- [Examples of gspread Usage](https://docs.gspread.org/en/latest/user-guide.html)
- [Google Sheets for Developers sheet API](https://developers.google.com/sheets/api)
- [GoogleスプレッドシートにAPIで書き込む！GCP設定編](https://amg-solution.jp/blog/26703)

