
# カーフチェッカーアプリを作ってみる (WIP)

#### ↓↓カーフチェッカーアプリ↓↓
[Kerf Check Parts Generator](https://kerf-generator-v2.vercel.app/)

<br>

現在ラボでは新たに木材で家具を作成することができる大型CNCを導入し、レーザー加工機に加え、
[プレスフィットコンストラクション](http://japan.fabcloud.io/FabAcademy-Tutorials/jp/week03_computer_controlled_cutting/press_fit_jp.html)(接着剤、釘、ネジなどの留め具を使わずに、パーツ同士をはめ合わせる技術)を使用したプロダクト開発ができる環境が充実してきました。

2023年1月21日より、FabLab Kuriyamaが新しい施設でオープンし、現在施設で使用する椅子とテーブルを作成している段階ですが、これらの家具はプレスフィットコンストラクションを用いて作成するため、事前に適切なカーフ(素材同士をはめ合わせる最適なギャップ)を見つける必要があります。

また、毎回のオープンラボに参加してくださる利用者様も複数の厚みの異なるMDFを使用し、模型制作をプレスフィットコンストラクションを用いて行なっております。

そのような中でラボでカーフを確認することができるツールがあると便利だと気づきたためカーフチェッカーアプリを作成しようと思いました。

既にFabLab Minatomiraiの道用先生が[Kerf Check Parts Generator](https://doyolab.net/appli/kerf_check/kerf_check.html)を作成されており、シンプルでわかりやすいKerf Checkerを作成されており、私自身もFabAcademyでレーザー加工機を使用した段ボールでのプレスフィットコンストラクションを作成した週の課題([こちら](https://fabacademy.org/2022/labs/kamakura/students/atsufumi-suzuki/assignments/week03.html))と大型CNC(ShopBot)でスタンディングテーブルを作成した週の課題([こちら](https://fabacademy.org/2022/labs/kamakura/students/atsufumi-suzuki/assignments/week07.html))に利用させていただきました。

こちらのカーフチェッカーは非常に便利ですが、基準幅、刻み幅、繰り返しの3つのパラメータのみの変更のため、チェッカーの大きさや間隔、CNCを利用した際の[ドッグボーン](https://www.sanyumokuzai.com/wp/wp-content/uploads/2020/12/%E3%82%B7%E3%83%A7%E3%83%83%E3%83%97%E3%83%9C%E3%83%83%E3%83%88%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85.pdf)などに対応されていないため、これらの機能を有したツールの開発をしようと思いました。

CNCを用いた加工の際は加工材料にドリルの半径分の丸みが生じるという特性があります。そのため、プレスフィットコンストラクションのようなはめ合わせる技術を用いる場合は半径分の丸みが干渉してしまいます。そのため、一部の接合部分にドリルを逃せるような形状を作成して加工するため、ドッグボーンフィレットというフィレットを作ってあげる必要があります。


## 作成する上で必要な知識

### SVGの構造の理解
### SVGをプログラミングで操作できる
### 作成したSVGデータをダウンロードすることができる

### SVGの構造の理解

### SVGをプログラミングで操作できる
SVGを操作するのはHTMLで作成したフォームの値を用いて、JavaScriptで値を可変的に変更できるようにして作成するのが良さそうなため以下の環境でWebアプリの作成をしていきたいと思います。

#### 環境
今回アプリを作成する上で使用する言語はHTML, CSS, JavaScript, Python
Flaskフレームワークを使用して作成しました。
サーバーはVercelを利用したいと思います。

[こちら](https://kerf-generator-v2.vercel.app/)のリンクからKerf Checkerに遷移できます。

```HTML
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>

</body>

</html>
```

```SCSS
// scss
```

```JavaScript
// JavaScript
```

```Python
```

## 参考資料
- [mozilla svg path](https://developer.mozilla.org/ja/docs/Web/SVG/Tutorial/Paths)
- [svg要素の基本的な使い方まとめ](http://defghi1977.html.xdomain.jp/tech/svgMemo/svgMemo_03.htm)
- [SVG の <circle> を <path> で描く](https://tyru.github.io/svg-circle-misc-algorithm/#whatsthis)
- [Kerf Check Parts Generator](https://doyolab.net/appli/kerf_check/kerf_check.html)



