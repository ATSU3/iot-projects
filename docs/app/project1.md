
# カーフチェッカーアプリを作ってみる (WIP)

#### ↓↓カーフチェッカーアプリ↓↓
[Kerf Check Parts Generator](https://kerf-generator-v2.vercel.app/)

[GitHub リポジトリ](https://github.com/ATSU3/kerf-generator_v2)

<br>

![](../images/app/project1/project1.jpg#center)

<br>

現在ラボでは新たに木材で家具を作成することができる大型CNCを導入し、レーザー加工機に加え、
[プレスフィットコンストラクション](http://japan.fabcloud.io/FabAcademy-Tutorials/jp/week03_computer_controlled_cutting/press_fit_jp.html)(接着剤、釘、ネジなどの留め具を使わずに、パーツ同士をはめ合わせる技術)を使用したプロダクト開発ができる環境が充実してきました。

2023年1月21日より、FabLab Kuriyamaが新しい施設でオープンし、現在施設で使用する椅子とテーブルを作成している段階ですが、これらの家具はプレスフィットコンストラクションを用いて作成するため、事前に適切なカーフ(素材同士をはめ合わせる最適なギャップ)を見つける必要があります。

また、毎回のオープンラボに参加してくださる利用者様も複数の厚みの異なるMDFを使用し、模型制作をプレスフィットコンストラクションを用いて行なっております。

そのような中でラボでカーフを確認することができるツールがあると便利だと気づきたためカーフチェッカーアプリを作成しようと思いました。

既にFabLab Minatomiraiの道用先生が[Kerf Check Parts Generator](https://doyolab.net/appli/kerf_check/kerf_check.html)を作成されており、シンプルでわかりやすいKerf Checkerを作成されており、私自身もFabAcademyでレーザー加工機を使用した段ボールでのプレスフィットコンストラクションを作成した週の課題([こちら](https://fabacademy.org/2022/labs/kamakura/students/atsufumi-suzuki/assignments/week03.html))と大型CNC(ShopBot)でスタンディングテーブルを作成した週の課題([こちら](https://fabacademy.org/2022/labs/kamakura/students/atsufumi-suzuki/assignments/week07.html))に利用させていただきました。

こちらのカーフチェッカーは非常に便利ですが、基準幅、刻み幅、繰り返しの3つのパラメータのみの変更のため、チェッカーの大きさや間隔、CNCを利用した際の[ドッグボーン](https://www.sanyumokuzai.com/wp/wp-content/uploads/2020/12/%E3%82%B7%E3%83%A7%E3%83%83%E3%83%97%E3%83%9C%E3%83%83%E3%83%88%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85.pdf)などに対応されていないため、これらの機能を有したツールの開発をしようと思いました。

CNCを用いた加工の際は加工材料にドリルの半径分の丸みが生じるという特性があります。そのため、プレスフィットコンストラクションのようなはめ合わせる技術を用いる場合は半径分の丸みが干渉してしまいます。そのため、一部の接合部分にドリルを逃せるような形状を作成して加工するため、ドッグボーンフィレットというフィレットを作ってあげる必要があります。



#### 環境
今回アプリを作成する上で使用する言語はJavaScript(Vue), Python
Flaskフレームワークを使用して作成しました。
サーバーはVercelを利用したいと思います。

[こちら](https://kerf-generator-v2.vercel.app/)のリンクからKerf Checkerに遷移できます。

```HTML
<!DOCTYPE html>
<html lang="ja">

<head>
  <title>Kerf Check</title>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link rel="stylesheet" href="../static/css/style.css">
  <!-- <script src="static/js/call_svc.js"></script> -->
  <script src="static/js/vue.js"></script>
  <script src="static/js/app.js" defer></script>

</head>

<body>
  <div class="container">
    <div class="mb-5">
      <h2>Kerf Check Parts Generator</h2>
    </div>
    <div id="app-1">
      <div class="h" id="js">
        <form name="par_form">
          <br>
          <table>
            <tr>
              <th>基準幅(start-width):</th>
              <th><input type="text" name="form_st" size="4" value="14"></th>
              <th>mm</th>
              <th></th>
            </tr>
            <tr>
              <th>刻み幅(pitch):</th>
              <th><input type="text" name="form_inc" size="4" value="0.05"></th>
              <th>mm</th>
            </tr>
            <tr>
              <th>隙間(gap):</th>
              <th><input type="text" name="form_gap" size="4" value="20"></th>
              <th>mm</th>
            </tr>
            <tr>
              <th></th>
            </tr>
            <tr>
              <th>個数(number-of-kerfs):</th>
              <th><input type="text" name="form_lp" size="4" value="4"></th>
              <th>個</th>
              <th></th>
            </tr>
            <tr>
              <th>刃径 (blade-diameter)</th>
              <th><input type="text" name="form_tbone" size="4" value="12.7"></th>
              <th>mm</th>
              <th></th>
            </tr>
          </table>
          <br>
          <br>
          <button type="button" v-on:click="createSVG">
            Create SVG
          </button>
        </form>
      </div>

      <div v-if="clicked && success">
        <h4>Click the image to download</h4>
        <a v-bind:href="downloadURL" download="kerf_check.svg"><img v-bind:src="downloadURL" /></a>
      </div>
      <div v-else-if="clicked">
        <hr />
        [[ errorMessage ]]
      </div>
    </div>
  </div>
</body>

</html>
```

```JavaScript
// Vue.js の mustache 記法 {{ }} を [[ ]] に変更する。これは Flask と併用する場合は必須。
Vue.options.delimiters = ["[[", "]]"];

var app1 = new Vue({
  el: "#app-1",
  data: {
    clicked: false,
    success: false,
    downloadURL: "#",
    errorMessage: "",
  },
  methods: {
    createSVG: function () {
      var self = this;
      self.clicked = true;

      var json = { 'stw': 0, 'inc': 0, 'gap': 0, 'lp': 0, 'tbone': 0 };
      json.stw = Number(par_form.form_st.value);
      json.inc = Number(par_form.form_inc.value);
      json.gap = Number(par_form.form_gap.value);
      json.lp = Number(par_form.form_lp.value);
      json.tbone = Number(par_form.form_tbone.value);

      (async () => {
        try {
          const response = await fetch("/kerf_check", {
            method: "POST",
            headers: {
              "Content-Type": "application/json; charset=utf-8",
            },
            body: JSON.stringify(json),
          });

          if (response.ok) {
            const data = await response.text();
            console.log(data);
            const blob = new Blob([data], { type: "image/svg+xml" });
            self.downloadURL = window.URL.createObjectURL(blob);
            self.success = true;
          } else {
            const data = await response.text();
            throw new Error(data);
          }
        } catch (error) {
          self.errorMessage = error.message;
          self.success = false;
        }
      })();
    },
  },
});

```

```Python
from flask import Flask
from flask import render_template
from flask import request

app = Flask(__name__)


@app.route("/")
def hello():
    return render_template("app.html")


@app.route("/kerf_check", methods=["POST"])
def kerf_check():
    json = request.json
    stw = json["stw"]
    inc = json["inc"]
    gap = json["gap"]
    lp = json["lp"]
    tbone = json["tbone"]

    if abs(inc) < 0.01:
        return "(pitch) is too small. (pitch) can only be set to 1/100 mm increments. (刻み幅)は1/100mm単位までしか設定できません。", 500

    txt = generate_svg(stw, inc, gap, lp, tbone)

    return txt, 200, {"Content-Type": "image/svg+xml"}


def generate_svg(stw, inc, gap, lp, tbone):
    # GAP = 16
    MARGIN = 10
    PARTS_HEIGHT = 50
    KERF_HEIGHT = 20

    if inc < 0:
        # inc = inc * (-1)
        inc = -inc
        stw = stw - inc * (lp - 1)

    cw = (stw + inc * lp + gap) * lp + gap + MARGIN * 2
    ch = PARTS_HEIGHT + MARGIN * 2
    orign_x = MARGIN
    orign_y = MARGIN

    st1 = f'<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="{cw}mm" height="{ch}mm" viewBox="0 0 {cw} {ch}">'
    ed1 = "</svg>"

    temp = f'<path stroke="red" stroke-width="0.1" fill="none" d="M{orign_x},{orign_y}'
    txt = ""

    x = orign_x
    y = orign_y
    kerf_width = stw

    y += PARTS_HEIGHT
    temp += f" V{y}"
    x += gap
    temp += f" H{x}"

    for i in range(lp):
        kerf_width = stw + inc * i
        # kerf_width=Math.round(kerf_width*100)/100
        kerf_width = round(kerf_width, 2)
        if len(str(kerf_width)) == 1:
            adjst = len(str(kerf_width)) - 1
        else:
            adjst = len(str(kerf_width)) - 2

        txt += f'<text x="{x - adjst}" y="25" font-size="3" fill="black">{kerf_width}</text>'

        y -= KERF_HEIGHT
        temp += f" V{y}"
        # Tボーンフィレット左側生成
        # t_bone = f" A 1 1 0 0 1 {x} {y-2} "
        t_bone = f" A {tbone / 2} {tbone / 2} 0 0 1 {x} {y - tbone}"
        temp += t_bone
        x += kerf_width
        temp += f" H{x}"
        # t_bone = f" A 1 1 0 0 1 {x} {y} "
        t_bone = f" A {tbone / 2} {tbone / 2} 0 0 1 {x} {y}"
        temp += t_bone
        y += KERF_HEIGHT
        temp += f" V{y}"
        x += gap
        temp += f" H{x}"

    y = y - PARTS_HEIGHT
    temp += f" V{y}"
    x = orign_x
    temp += f" H{x}"
    temp += '"/>'

    result = f"{st1}\n{temp}\n{txt}\n{ed1}"

    return result

```

## 参考資料
- [mozilla svg path](https://developer.mozilla.org/ja/docs/Web/SVG/Tutorial/Paths)
- [svg要素の基本的な使い方まとめ](http://defghi1977.html.xdomain.jp/tech/svgMemo/svgMemo_03.htm)
- [SVG の <circle> を <path> で描く](https://tyru.github.io/svg-circle-misc-algorithm/#whatsthis)
- [Kerf Check Parts Generator](https://doyolab.net/appli/kerf_check/kerf_check.html)



