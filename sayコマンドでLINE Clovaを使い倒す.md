# やりたいこと

- 声で操作すべきデバイス（LINE Clova）を自分の肉声を出さずに操作する
- 機械どうしが意思をもって語り合っているかのような雰囲気を醸し出す

# 方法

- macOSデフォルトの`say`コマンドを使う
- 声は日本女性の`Kyoko`さんにする
- LINE Clova側の設定で、声認証をONにしていると自分の声でないmacOSの音声には反応しないので、予めOFFにしておく。スマホアプリの`声認証`項目参照

```shell
$ say -v Kyoko クローバ
```

（Clova 「ポンっ（緑に光る）」）

```shell
$ say -v Kyoko 明日の天気は
```

（Clova「明日は曇りのち雨でしょう。降水確率は…」）

# 工夫

- macOSの`crontab`で`say`コマンドの発動を設定しておけば、LINE Clovaを実質的なアラームとして使うことができる（特にMac miniユーザーとかよさそう）
- 毎回`say -v Kyoko XXX`を打つのは面倒なので、シェルのエイリアスで`alias say=say -v Kyoko`などの設定をしておくといいかも
- LINE Clobaの[マイコマンド](http://clova-blog.line.me/ja/archives/17164378.html)機能との合わせ技。先述の`crontab`と組み合わせれば「毎朝7時に勝手にその日の天気と予定を伝え、好きなプレイリストの音楽を流し始めるLINE Clova」とかがつくれる

おわり