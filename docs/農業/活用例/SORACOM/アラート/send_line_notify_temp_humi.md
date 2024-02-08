# 温度が設定した閾値を超えた場合Lineへ通知する

以下に温度が25度を超えた場合Lineに通知するまでの流れを説明します。

本アラート設定をする上で、以下のプログラムを使用します。

[[1分間おきにSoracom Harvestに温湿度のデータを送信する]](https://github.com/ATSU3/Wio_LTE_Sample/blob/main/measure_temp_humi/measure_temp_humi.ino)

また、事前の設定はこちらをご確認ください
<br>

- [[温湿度センサ：一定の間隔で温湿度をSORACOM Harvestに送信する]](../../../../農業/活用例/SORACOM/センサ/temp_humi_sensor.md)

- [[Line Notifyへのアラート事前設定]](../アラート/send_line_notify.md)

まずは、どのデバイスのどのセンサの値を取得し、閾値とどのように比較しアラートするのか、などの設定をしていきます。
そのため、「Alerting」=> 「Alert rules」で「Alert rules」の設定ページを開きます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_1.jpg#center)

その後「New alert rule」ボタンをクリックします。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_2.jpg#center)

以下のページが新規で開かれるため、各項目を設定していきましょう

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_3.jpg#center)

初期の状態では、デフォルトで「A」「B」「C」のように名前がつけられております。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_4.jpg#center)

まずは、「A」の設定を以下のように変更しましょう。


![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_5.jpg#center)

|  項目<br>変更前  |  項目<br>変更後  | 説明  |
| ---- | ---- | ---- |
|  A  |  temp  | 温湿度センサの温度の値を取得するための命名  |
|  Choose(未選択)  |  Air  | SORACOM Airで登録しているSIMカードの名前が表示されるため、対象の名前を選択します。  |
|  Choose(未選択)  |  Standard  | 「Time series」などのグラフの表示がされます。  |
|  Choose(未選択)  |  temp  | 今回は温度のセンサデータを取得するため対象の変数（temp）を選択しております。 |

次に、「B」の設定を以下のように変更しましょう。こちらは、上記で設定した値に対して、どのような計算方法を適用させるかを決定します。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_6.jpg#center)

|  項目<br>変更前  |  項目<br>変更後  | 説明  |
| ---- | ---- | ---- |
|  B  |  temp reduce  | 温湿度センサの温度の値を取得するための命名  |
|  Reduce(デフォルト)  |  Reduce  | 集約計算を行うことができます。  |
|  Function(未選択)  |  Mean  | 「平均(Mean)を計算することができます。今回の設定では、特定の時間範囲における平均温度を計算します。  |
|  Input(未選択)  |  temp  | 「A」の項目で設定した名前を選択します。 |
|  Mode(未選択)  |  Strict  | 数値でない場合は NaN として扱う設定ができます。 |

次に、「C」の設定を以下のように変更しましょう。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_7.jpg#center)

|  項目<br>変更前  |  項目<br>変更後  | 説明  |
| ---- | ---- | ---- |
|  C  |  Condition  | 閾値などの比較する対象の状態を設定するためConditionという名前に設定しております。  |
|  Input(未選択) |  temp reduce  | 「B」で設定した名前を選択します。  |
|  ---  |  「IS BELOW」「24」  | 温度が24度以下の時にアラート通知をしたいため、このように設定します。  |

「Preview」ボタンをクリックすることで、今回の設定では24度以下の場合アラートを出すような設定をしたいため、赤い色で「Firing」と表示されます。（「Status」が「Normal」から「Firing」になる。）

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_8.jpg#center)

次に「Alert evaluation behavior」の設定をしていきます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_9.jpg#center)

|  項目  |  説明  |
| ---- | ---- | 
|  [Evaluate every]  | 5m: 5分を表し、こちらが評価間隔になります。 | 
|  [for]  |  0： Alert が作成されるまでの待機時間です。「0」を入力すると、Alert rule を満たすとすぐに (0 秒で) Alert が作成されます。 | 


次に、「Add details for your alert」の設定をしていきます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_10.jpg#center)

|  項目  |  説明  |
| ---- | ---- | 
|  [Rule name]  | Alert rule の名前です。 | 
|  [Folder]  |  Alert rule を保存する Folderの名前です。 | 
|  [Group]  | Alert rule の Group の名前です。 | 

設定が完了したら、「Save」ボタンをクリックして保存します。ここまでで「Alert rules」の設定は一旦完了になります。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_11.jpg#center)

次に「Contact points」の設定をしていきます。「New contact point」ボタンをクリックして、新規で送信先を決定していきましょう。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_12.jpg#center)

今回は「LINE Notify」を使用しますので、「Contact point type」の項目を「LINE」に設定します。
「Token」に[[Line Notifyへのアラート事前設定]](../アラート/send_line_notify.md)で設定した「Token」文字列を入力します。2ヶ所の入力後に「Test」ボタンをクリックして、テスト通知を行います。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_13.jpg#center)

「Send test notification」ボタンをクリックすると、LINE Notifyに通知を送ることができます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_14.jpg#center)

以下のように、手動テストによって通知が送られていることが確認できます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_15.jpg#center)

次に、「Notification policies」の設定を行います。「Notification policies」の項目を選択して、「New policy」ボタンをクリックします。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_16.jpg#center)

デフォルトの状態では、Root policyで設定する設定が適用されます。
![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_19.jpg#center)

今回は「Add matcher」をクリックして特定のフォルダに属している場合にのみ、アラートルールが適用設定していきます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_17.jpg#center)

以下の設定では、「grafana_folder = testFolder」という条件を設定しておりますが、SORACOM Lagoonで管理されているダッシュボードが「testFolder」というフォルダに属している場合にのみ、アラートルールが適用されることを意味しています。（SORACOM Lagoonはgrafanaをベースにしているため）つまり、このルールにマッチしたアラートが発生したとき、設定された連絡先（この場合はLINE Notify）に通知が送られます。また、「Override grouping」がオンになっている場合は、このアラートポリシーで設定された「Group by」の条件が優先され、親ポリシーのグループ化設定は無視されます。

![](../../../../images/agri/example/soracom/alart/alert_rules/alert_rules_18.jpg#center)

「Save policy」ボタンをクリックして、完了です。
ここまでで、大まかなアラート設定は完了できました。



以下のSORACOMのドキュメンテーションが大変参考になります。

[SORACOM Alert ドキュメンテーション 詳細](https://users.soracom.io/ja-jp/docs/lagoon-v3/setup-simple-alert/)
