<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# ラボ内の在庫管理を自動化してみる (WIP)

![](../images/prototype/prototype1/idea_sketch.jpg#center)

ラボ内では多くの電子パーツあり、在庫管理に非常に多くの時間を要するため、自動化することができないかどうか考えた。
在庫はスプレッドシートで管理しているため、各棚と対象のスプレッドシートの各パーツの在庫数が同期されるような仕組みを作り自動化することができたら行もが大幅に効率化されるため、作成してみる。

## スプレッドシートをpythonで操作する

### 環境

#### Google Cloud Platformの設定

1. プロジェクトを作成
2. Google Drive APIを有効にする
3. Google Sheets APIを有効にする
4. 「APIとサービス」-> 「ダッシュボード」から有効化されていることを確認する。
5. 「認証情報」-> 「サービスアカウント名」-> 「役割: 編集者」, 「キータイプ: JSON」で作成する。


#### Spreadsheet設定
サンプルで部品名とその個数を入力するシートを作成する。
![](../images/prototype/prototype1/spreadsheet_1.jpg#center)



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
GPIO.setup(6, GPIO.IN, pull_up_down=GPIO.PUD_UP)

while True:
    while GPIO.input(6) == GPIO.LOW:
        val_1 = worksheet.acell("B2").value
        val_2 = worksheet.acell("B3").value
        print(GPIO.input(2))
        if GPIO.input(2) == GPIO.LOW:
            val1 = int(val_1) + 1
            worksheet.update('B2', val1)
            time.sleep(0.3)
        elif GPIO.input(3) == GPIO.LOW:
            val1 = int(val_1) - 1
            worksheet.update('B2', val1)
            time.sleep(0.3)
        elif GPIO.input(4) == GPIO.LOW:
            val2 = int(val_2) + 1
            worksheet.update('B3', val2)
            time.sleep(0.3)
        elif GPIO.input(5) == GPIO.LOW:
            val2 = int(val_2) - 1
            worksheet.update('B3', val2)
            time.sleep(0.3)
        time.sleep(0.3)

```

## 参考資料
- [Examples of gspread Usage](https://docs.gspread.org/en/latest/user-guide.html)
- [Google Sheets for Developers sheet API](https://developers.google.com/sheets/api)
