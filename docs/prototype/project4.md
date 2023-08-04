<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# 温湿度計を作ってみる(IoT農業)

本プロジェクトではproject3で作成した温湿度を測定するデバイスをインターネット環境下して使用できるようにアップデートしたものになります。
こちらは完成形になります。

![](../images/prototype/prototype4/prototype4.jpg#center)

## デバイスについて

### 追加機能
- データベースの保存
- データの可視化
- csv形式データのダウンロード

### プログラム
プログラムは以下のページから確認できます。

#### ESP32書き込みプログラム

| Version |Function| URL |
| ------- | ------ | --- |
|    2.1  | 湿度と温度を読み取り、Supabaseに保存 | [Link](https://github.com/ATSU3/iot-agri_arduino/blob/main/arduino/prototype_v2.1/prototype_v2.1.ino) |

#### Webアプリケーション
- [温湿度のデータをリアルタイムで取得してデータベースに保存し、アプリ上で表とグラフで可視化するプログラム](https://github.com/ATSU3/supabase_DHT22)

#### Supabase (データベース)
こちらにSupabaseの設定をまとめております。

- [Supabaseの設定](../basic/programming/Database/supabase.md)