
# Google Cloud PlatformでPythonを用いてスプレッドシートを操作する方法

## 行うこと
- Pythonを用いてスプレッドシートを操作できるように設定します。
    - Google Cloud Platform(GCP)の設定
    - スプレッドシートを作成しGCPとの紐付け
    - Pythonを用いてスプレッドシートのセルの操作


### Google Cloud Platform(GCP)の設定

Google Cloud のコンソール画面を開いたらまず最初に新しいプロジェクトを作成します。
ナビゲーションメニューから以下の黄色い枠で囲われた部分をクリックします。

<br>
![](../../images/prototype/prototype1/gcp/gcp_1.jpg#center)

<br>

プロジェクト選択・新規作成のウィンドウが表示されるため、新しいプロジェクトをクリックします。

<br>

![](../../images/prototype/prototype1/gcp/gcp_2.jpg#center)

<br>

新しいプロジェクト作成ページに遷移するため、プロジェクト名を入力して作成ボタンをクリックします。

<br>

![](../../images/prototype/prototype1/gcp/gcp_3.jpg#center)

<br>

プロジェクトができると、ナビゲーションバーの通知からプロジェクトを確認できるため、「プロジェクトを選択」をクリックして、作成したプロジェクトに移動しましょう。

<br>

![](../../images/prototype/prototype1/gcp/gcp_4.jpg#center)

<br>

移動すると、対象のプロジェクトのダッシュボードページに遷移します。

<br>

![](../../images/prototype/prototype1/gcp/gcp_5.jpg#center)

<br>

Spreadsheet APIとGoogle Drive APIを有効にする。

ハンバーガーメニューをクリックして「APIとサービス」-> 「ライブラリ」を選択します。

<br>

![](../../images/prototype/prototype1/gcp/gcp_6.jpg#center)

<br>

以下のライブラリ検索ページが表示されるため、検索ボックスに「spreadsheet」と入力して「Google Sheets API」を表示します。

<br>

![](../../images/prototype/prototype1/gcp/gcp_7.jpg#center)

<br>

Google Sheets APIを有効にするため、「有効にする」ボタンをクリックします。

<br>

![](../../images/prototype/prototype1/gcp/gcp_8.jpg#center)

<br>

次に、上記と同様にGoogle Drive APIを検索して、有効化します。

<br>

![](../../images/prototype/prototype1/gcp/gcp_9.jpg#center)

<br>

![](../../images/prototype/prototype1/gcp/gcp_10.jpg#center)

<br>

「APIとサービス」-> 「有効なAPIとサービス」から有効化されていることを確認することができます。

<br>

![](../../images/prototype/prototype1/gcp/gcp_11.jpg#center)

<br>

![](../../images/prototype/prototype1/gcp/gcp_12.jpg#center)

<br>
次にAPIとサービスから「認証情報」-> 「認証情報を作成」->「サービスアカウント」を選択します。
<br>

![](../../images/prototype/prototype1/gcp/gcp_13.jpg#center)

<br>
「サービスアカウントの作成」ページに遷移するため、「サービスアカウント名」「サービスアカウントID」を入力し「完了」ボタンを押します。
<br>

![](../../images/prototype/prototype1/gcp/gcp_14.jpg#center)

<br>
「このサービスアカウントにプロジェクトへのアクセスを許可する」の「ロール」を「編集者」に設定します。
<br>

![](../../images/prototype/prototype1/gcp/gcp_15.jpg#center)

<br>
「ユーザーにこのサービスアカウントへの合うセスを許可」は省略して構いません。
「完了」ボタンを押します。
<br>

![](../../images/prototype/prototype1/gcp/gcp_16.jpg#center)

<br>
以下の画面のように「サービスアカウント」の「メール」
にメールアドレスが追加されていることが確認できます。
<br>

![](../../images/prototype/prototype1/gcp/gcp_17.jpg#center)

<br>
「メール」の対象のメールアドレスのリンクをクリックすると以下の画面に遷移するため、画面右側の「操作」のメニューから「鍵を管理」を選択します。
<br>

![](../../images/prototype/prototype1/gcp/gcp_18.jpg#center)

<br>

![](../../images/prototype/prototype1/gcp/gcp_19.jpg#center)

<br>

![](../../images/prototype/prototype1/gcp/gcp_20.jpg#center)

<br>

![](../../images/prototype/prototype1/gcp/gcp_21.jpg#center)


### スプレッドシートを作成しGCPとの紐付け


### Pythonを用いてスプレッドシートのセルの操作

参考: [gspread documentation](https://docs.gspread.org/en/v5.7.0/)

今回はpythonでスプレッドシートを操作するため、gspreadと、Oauth認証関連のoauth2clientを使用します。

```bash
$ pip install gspread oauth2client
```

Pythonファイルを作成して以下のようにgspread、oauth2clientのライブラリをインポートします。

```python
import gspread
from google.oauth2.service_account import Credentials
```

ServiceAccountCredentialsはここではGCPのスプレッドシート、Googleドライブへアクセスできる変数を生成します。
ServiceAccountCredentialsのfrom_json_keyfile_nameメソッドを用いてjsonファイルを変数cに代入し、設定しております。

```python

scope = ['https://spreadsheets.google.com/feeds','https://www.googleapis.com/auth/drive']

c = ServiceAccountCredentials.from_json_keyfile_name('対象のjsonファイル', scope)
```

OAuth2.0の資格情報を使用してGCP SpreadSheet APIにログインします。
```python
gs = gspread.authorize(c)
```

設定・共有したスプレッドシートキーを変数 SPREADSHEET_KEYに格納します。
```Python
SPREADSHEET_KEY = 'スプレッドシートキー'
```

スプレッドシートキーと紐づいているspreadsheetのワークシート名がstockのシート情報を取得して変数worksheetに格納します。
```python
worksheet = gs.open_by_key(SPREADSHEET_KEY).worksheet("stock")
```

acellメソッドを用いると引数でセットしたセルの値を文字列(string)で取得できます。
```
worksheet.acell("A1").value
```

[ラボ内の在庫管理を自動化してみるのプログラム](../../prototype/project1.html#python_2)では、セルの値は文字列(string)で取得されているため、各セルの数値を計算するためにint関数で整数に変換して取得しております。
```python
val_1 = worksheet.acell("B2").value
val1 = int(val_1) + 1
```

updateメソッドを用いることでセル内の値を更新することができます。
```python
worksheet.update('B2', val1)
```
