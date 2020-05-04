# やりたいこと

- タイトル通り
- 想定するユースケース：定期的に発生、かつ開始時間やインターバルが決まっているイベント
- オンライン授業とか、週次会議とか、休憩とか

# 方針

- crontab：コマンドを定時に実行させるコマンド
- 手元の環境はmacOS Catalina v10.15.4

# 方法

- crontabコマンドの使い方チュートリアル：[crontabの書き方](https://www.server-memo.net/tips/crontab.html)がおすすめ
- afplay（ターミナルからオーディオファイルを再生するコマンド）を併用
- 予め任意のmp3ファイルを好きなパス（ここでは`~/Music/chime.mp3`）に置いておく
  - Webで探すなら[soundsnap](https://www.soundsnap.com/)がオススメ



- crontabの編集

```sh
$ crontab -e
```

- デフォルトのエディタで`crontab.XXXXXXXXXX`みたいなファイルが開かれる
- 好みの設定を記述して、ファイルを保存・エディタquit

```sh
25 8 * * mon-fri afplay ~/Music/chime.mp3
20 10 * * mon-fri afplay ~/Music/chime.mp3
55 12 * * mon-fri afplay ~/Music/chime.mp3
50 14 * * mon-fri afplay ~/Music/chime.mp3
# Comment for the newline of crontab termination character. See man 5 crontab
```

- 月曜〜金曜の8:25・10:20・12:55・14:50に`chime.mp3`が再生される
- 最終行にコメントがないとなぜか実行がうまくいかなかったので付してある
  - [cronの最終行が実行されない原因と対処法](https://qiita.com/airtanker/items/72359c234f898e3255d2)

# 参考

- [Macに現在時刻を喋らせる - 橋本商会](https://scrapbox.io/shokai/Mac%E3%81%AB%E7%8F%BE%E5%9C%A8%E6%99%82%E5%88%BB%E3%82%92%E5%96%8B%E3%82%89%E3%81%9B%E3%82%8B)



おわり