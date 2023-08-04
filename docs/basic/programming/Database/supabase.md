<head>
  <link href="../css/extra.css" rel="stylesheet"></link>
</head>

# Supabase

## Supabaseとは
Supabaseとは、オープンソースのPostgreSQLのBaaS(Backend as a Service)です。
バックエンド機能をアプリケーションサーバー側が行ってくれるとても便利なサービスです。

[SupabaseのURL](https://supabase.com/)

## 初期設定
まず、「Start Your project」ボタンを押して、アカウントを登録しましょう。

![](../../../images/basic/Database/supabase/supabase_1.jpg#center)

「Sign Up Now」をクリックして、Emailとpasswordを入力して、アカウントを作成します。GitHubアカウントを持っている場合、GitHubアカウントと紐付けてログインできます。

![](../../../images/basic/Database/supabase/supabase_2.jpg#center)

ログインすると、以下のDashboardが表示されます。「New project」ボタンをクリックして、新しいプロジェクトを作成します。

![](../../../images/basic/Database/supabase/supabase_3.jpg#center)

「New project」-> 「Choose organization」を選択します。

![](../../../images/basic/Database/supabase/supabase_4.jpg#center)

「Name」「Type of organization」を入力して「Create organization」をクリックします。

![](../../../images/basic/Database/supabase/supabase_5.jpg#center)

次に、画面が「Create a new project」に遷移するため、データベースの設定を行います。「Name」「Database Password」「Region」「Procing Plan」を入力し、「Create new project」ボタンをクリックします。「Region」は住んでいる国のサーバーを選択するとパフォーマンスがよくなります。また、2プロジェクトまで無料で作成することができます。

![](../../../images/basic/Database/supabase/supabase_6.jpg#center)

セットアップが完了すると、以下の画面が表示されます。これで、初期設定は完了です。

![](../../../images/basic/Database/supabase/supabase_7.jpg#center)

## テーブル作成

画面左側の上から2番目の「Table Editor」アイコンをクリックします。

![](../../../images/basic/Database/supabase/supabase_8.jpg#center)

「Create a new table」ボタンをクリックして、テーブルを作成しましょう。

![](../../../images/basic/Database/supabase/supabase_9.jpg#center)

以下の画面が表示されるため、「Name」テーブル名を入力します。
Enable Row Level Security (RLS)でセキュリティの設定ができます。

![](../../../images/basic/Database/supabase/supabase_10.jpg#center)

「Columns」の項目を入力します。
デフォルトで「id」「created_at」は設定されております。「Add column」ボタンでカラムを追加することができます。テーブルが作成できたら、「Save」ボタンをクリックして、保存します。
![](../../../images/basic/Database/supabase/supabase_11.jpg#center)

少し待つと、以下のようにテーブルが完成します。GUIでテーブルを簡単に作成できるため、とても便利です。

![](../../../images/basic/Database/supabase/supabase_12.jpg#center)

また、「insert」ボタンでデータや別のカラムを追加することができます。

![](../../../images/basic/Database/supabase/supabase_13.jpg#center)

## Project APIの取得

左側のホームアイコンをクリックして、ページをスクロールすると、「Project URL」「API Key」を取得できます。

![](../../../images/basic/Database/supabase/supabase_14.jpg#center)
