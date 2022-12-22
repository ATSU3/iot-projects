<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# ラボ内の在庫管理を自動化してみる (WIP)

![](../images/prototype/prototype1/idea_sketch.jpg#center)

ラボ内では多くの電子パーツあり、在庫管理に非常に多くの時間を要するため、自動化することができないかどうか考えた。
在庫はスプレッドシートで管理しているため、各棚と対象のスプレッドシートの各パーツの在庫数が同期されるような仕組みを作り自動化することができたら行もが大幅に効率化されるため、作成してみる。

### スプレッドシートをpythonで操作する

#### 環境
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


### 参考資料
- [Examples of gspread Usage](https://docs.gspread.org/en/latest/user-guide.html)
- [Google Sheets for Developers sheet API](https://developers.google.com/sheets/api)
