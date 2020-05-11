# なやみ

- Mac版Google Chromeでは`Command + L`を押下するとアドレスバーにフォーカスするデフォルトのショートカットがある
- が、いったんフォーカスすると入力待ち状態になったまま抜け出せない…！
- これは特に[Vimium]ユーザにとって深刻な問題で、`j`や`k`で上下移動をしたり、Vomnivarを出したりしようとしてキーを押すとアドレスバーへの入力として認識されアドレスバーが`jjjjjjjj`みたいになる

[![Image from Gyazo](https://i.gyazo.com/107322839881688beaab0536df350692.gif)](https://gyazo.com/107322839881688beaab0536df350692)

# 解決策

- 色々調べて行き着いたのは結局、Vimiumのイシュー。[同様のなやみに対するハック](https://github.com/philc/vimium/issues/840#issuecomment-17948885)がとても良かったので紹介
- 要するに「Google ChromeのSearch Engineで空のJavaScriptを実行するキーワードを設定しておく」もので、手順は
  - Google Chromeの設定ページ→`Manage Search Engine`（`chrome://settings/searchEngines`）へ行く
  - `Other Search Engine`から`Add`で新規作成、任意のタイトルとショートカットとなるキーワード（`unfocus`の頭文字でここでは`u`）とURLの代わりの`javascript::`を入力・保存
  - [![Image from Gyazo](https://i.gyazo.com/2e642639f539fbafb0b46c9ea35fbed5.png)](https://gyazo.com/2e642639f539fbafb0b46c9ea35fbed5)

- これでアドレスバーにフォーカスが当たっている状態で`u`→`Enter`を押下すると`javascript::`というスクリプトが実行される
  - そしてフォーカスがページの内容に戻り、無事Vimiumのキーバインドが使える
  - しかも`tab`でフォーカスを外したときのように、ページ内の何らかの要素にフォーカスするという副作用が起きない

[![Image from Gyazo](https://i.gyazo.com/e83ec1089b6146c85136ef5b11486ac1.gif)](https://gyazo.com/e83ec1089b6146c85136ef5b11486ac1)

ベンリ！！！