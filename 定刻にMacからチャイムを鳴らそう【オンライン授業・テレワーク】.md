# 生活リズム #とは

- 学校や職場だと周囲にいる人間たちをみて「休憩するか〜」とか「ちょっと散歩しよ」「ご飯たべよ」とかなることもあるが、部屋にいるとあまりに目の周りの移ろいがなさすぎる
- とりあえずMac先生に時間でも通知してもらおう…

# crontab

- 設定した時間にコマンドを実行してくれる

# 設定

```sh
$ crontab -e
```

- デフォルトのエディタで`crontab.XXXXXXXXXX`みたいなファイルが開いたら、以下のように設定します。

```sh
25 8 * * * afplay ~/Music/chime.mp3
20 10 * * * afplay ~/Music/chime.mp3
55 12 * * * afplay ~/Music/chime.mp3
50 14 * * * afplay ~/Music/chime.mp3
# Comment for the newline of crontab termination character. See man 5 crontab
```

- 簡単に説明すると左から「分」「時」「日」「月」「曜日」「実行コマンド」です
  - この設定だと毎日8:25、10:20、12:55、14:50にMusicディレクトリの`chime.mp3`が再生されます
  - ちなみに大学の授業の1〜4限のそれぞれ5分前という設定です
- 詳しくは[crontabの書き方 | server-memo.net](https://www.server-memo.net/tips/crontab.html)などが参考になると思います
- `afplay`はオーディオファイルを再生するコマンド。着想は[Macに現在時刻を喋らせる - 橋本商会](https://scrapbox.io/shokai/Mac%E3%81%AB%E7%8F%BE%E5%9C%A8%E6%99%82%E5%88%BB%E3%82%92%E5%96%8B%E3%82%89%E3%81%9B%E3%82%8B)からいただきました
- mp3は何でもいいですが、[soundsnap](https://www.soundsnap.com/)とかオススメ
- 最終行にコメントがないとなぜか実行がうまくいかなかったので付してあります（参考：[cronの最終行が実行されない原因と対処法 - Qiita](https://qiita.com/airtanker/items/72359c234f898e3255d2)）

以上です。