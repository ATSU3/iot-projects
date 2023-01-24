
# カーフチェッカーアプリを作ってみる (WIP)

現在ラボでは新たに木材で家具を作成することができる大型CNCを導入し、レーザー加工機に加え、
[プレスフィットコンストラクション](http://japan.fabcloud.io/FabAcademy-Tutorials/jp/week03_computer_controlled_cutting/press_fit_jp.html)(接着剤、釘、ネジなどの留め具を使わずに、パーツ同士をはめ合わせる技術)を使用したプロダクト開発ができる環境が充実してきました。

2023年1月21日より、FabLab Kuriyamaが新しい施設でオープンし、現在施設で使用する椅子とテーブルを作成している段階ですが、これらの家具はプレスフィットコンストラクションを用いて作成するため、事前に適切なカーフ(素材同士をはめ合わせる最適なギャップ)を見つける必要があります。

また、毎回のオープンラボに参加してくださる利用者様も複数の厚みの異なるMDFを使用し、模型制作をプレスフィットコンストラクションを用いて行なっております。

そのような中でラボでカーフを確認することができるツールがあると便利だと気づきたためカーフチェッカーアプリを作成しようと思いました。

## 参考資料
- [mozilla svg path](https://developer.mozilla.org/ja/docs/Web/SVG/Tutorial/Paths)
- [svg要素の基本的な使い方まとめ](http://defghi1977.html.xdomain.jp/tech/svgMemo/svgMemo_03.htm)
- [SVG の <circle> を <path> で描く](https://tyru.github.io/svg-circle-misc-algorithm/#whatsthis)
- [Kerf Check Parts Generator](https://doyolab.net/appli/kerf_check/kerf_check.html)



