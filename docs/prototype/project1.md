<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# ラボ内の在庫管理を自動化してみる (WIP)

ラボ内では多くの電子パーツあり、在庫管理に非常に多くの時間を要するため、自動化することができないかどうか考えました。
在庫はスプレッドシートで管理しているため、各棚と対象のスプレッドシートの各パーツの在庫数が同期されるような仕組みを作り自動化することができたら作業が大幅に効率化されると考えました。

![](../images/prototype/prototype1/idea_sketch/idea_sketch.jpg#center)

今回はラボ内の電子パーツが限られているため、第一段階目として、上記のように収納箱の前面に+ボタン、-ボタンを取り付け、使った分+,-のボタンを押すことでスプレッドシートに数を反映させるプロトタイプを作成します。

![](../images/prototype/prototype1/idea_sketch/idea_sketch_2.jpg#center)

## スプレッドシートをpythonで操作する

### 環境

#### Google Cloud Platformの設定

[こちら](https://amg-solution.jp/blog/26703)の記事を参考にGoogle Cloud Platoformを設定し、スプレッドシートに反映させました。

<!-- 
- プロジェクトを作成する。

Google Cloud のコンソール画面を開いたらまず最初に新しいプロジェクトを作成します。
ナビゲーションメニューから以下の黄色い枠で囲われた部分をクリックします。
![](../images/prototype/prototype1/gcp/gcp_1.jpg#center)

プロジェクト選択・新規作成のウィンドウが表示されるため、新しいプロジェクトをクリックします。
![](../images/prototype/prototype1/gcp/gcp_2.jpg#center)

新しいプロジェクト作成ページに遷移するため、プロジェクト名を入力して作成ボタンをクリックします。
![](../images/prototype/prototype1/gcp/gcp_3.jpg#center)

プロジェクトができると、ナビゲーションバーの通知からプロジェクトを確認できるため、「プロジェクトを選択」をクリックして、作成したプロジェクトに移動しましょう。
![](../images/prototype/prototype1/gcp/gcp_4.jpg#center)

移動すると、対象のプロジェクトのダッシュボードページに遷移します。
![](../images/prototype/prototype1/gcp/gcp_5.jpg#center)

2. Spreadsheet APIとGoogle Drive APIを有効にする。

ハンバーガーメニューをクリックして「APIとサービス」-> 「ライブラリ」を選択します。
![](../images/prototype/prototype1/gcp/gcp_6.jpg#center)

以下のライブラリ検索ページが表示されるため、検索ボックスに「spreadsheet」と入力して「Google Sheets API」を表示します。
![](../images/prototype/prototype1/gcp/gcp_7.jpg#center)

Google Sheets APIを有効にするため、「有効にする」ボタンをクリックします。
![](../images/prototype/prototype1/gcp/gcp_8.jpg#center)


次に、上記と同様にGoogle Drive APIを検索して、有効化します。
![](../images/prototype/prototype1/gcp/gcp_9.jpg#center)

![](../images/prototype/prototype1/gcp/gcp_10.jpg#center)

「APIとサービス」-> 「有効なAPIとサービス」から有効化されていることを確認することができます。
![](../images/prototype/prototype1/gcp/gcp_11.jpg#center)

![](../images/prototype/prototype1/gcp/gcp_12.jpg#center)

5. 「認証情報」-> 「サービスアカウント名」-> 「役割: 編集者」, 「キータイプ: JSON」で作成する。 -->


#### Spreadsheet設定
今回はプロトタイプのため、部品名とその個数を入力するシートを作成します。また対応する2つの収納箱を作成します。
![](../images/prototype/prototype1/spreadsheet/spreadsheet_1.jpg#center)

#### 電子回路

電子回路は以下の図のような構成にしております。RapsberrypiのGPIO2, 3, 4, 5を各タクトスイッチと接続しております。また、タクトスイッチはRaspberryPi内部のプルアップ抵抗とも接続しております。

![](../images/prototype/prototype1/circuit/circuit_1.jpg#center)

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

### Pythonプログラム
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

#### ボディ設計

Fusion360で以下のように収納ケースを作成しました。

![](../images/prototype/prototype1/fusion360/fusion360_1.jpg#center)

![](../images/prototype/prototype1/fusion360/fusion360_2.jpg#center)

作成したデータはDXF形式で出力して、加工はMDF2.5mmを使用しレーザーカッターを用いて行いて行いました。

電子回路は以下のように半田付けして格納しております。
![](../images/prototype/prototype1/circuit/circuit_2.jpg#center)

#### プロトタイプ

以下の動画のように+,-ボタンを押した回数分、カウントアップ、カウントダウンし、値がリアルタイムでスプレッドシートに反映されます。

<video width="100%" height="400px" id="center" controls>
  <source src="../videos/test/project1/prototype_1.mp4" type="video/mp4">
</video>


## データ
- [case.ai](../files/prototype1/case.ai)
- [case.pdf](../files/prototype1/case.pdf)

## 参考資料
- [Examples of gspread Usage](https://docs.gspread.org/en/latest/user-guide.html)
- [Google Sheets for Developers sheet API](https://developers.google.com/sheets/api)
- [GoogleスプレッドシートにAPIで書き込む！GCP設定編](https://amg-solution.jp/blog/26703)

