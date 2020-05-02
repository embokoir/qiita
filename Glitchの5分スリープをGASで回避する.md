[Glitch](https://glitch.com)を利用する方向けのTipsです

- Glitchとは：Node・ExpressベースでちょっとしたAPIやWebサイトを公開するのに使える無料サービス
- 個人的にはLINEやSlackの[Bot](https://qiita.com/embokoir/items/419365542369d749654b)をつくったり、ちょっとしたWebサイトを公開したりするのに使っています

# Glitchのスリープ
- Glitchには「5分以上リクエストがない場合はスリープする」という[制限](https://glitch.com/help/restrictions/)がある
- Webサイトであれば「Waking up」といったローディング画面が出るのでまだ辛抱できるかもしれないが、WebhookのAPIなどに利用している場合、ユーザーへのレスポンスがかなり遅くなったり、他のサービスのAPIでタイムアウトになったり（Slackは特に厳しい）してしまう
[![Image from Gyazo](https://i.gyazo.com/7d250df9dd4f61fa2b90eda79cc34d86.png)](https://gyazo.com/7d250df9dd4f61fa2b90eda79cc34d86)
- 5分もインターバルができないほどリクエストがあるとは限らないことが多いはず
- 回避したい

###### 2020/4 追記

- Glitchが[有料プラン](https://glitch.com/pricing)を発表しました。有料プランに登録するとスリープはなくなり、上述の問題は解決されます
- 以下は無料プラン内でやりくりする場合の参考として残しておきます

###### 追記終わり

# 回避策
- Google Apps ScriptでGlitchのURLにリクエストを送る関数を定期実行する
- GASには定期実行のインターバルが何種類もあるが、とりあえず5分おき（12回/時）に実行するようにする。GlitchのRequestリミットは1プロジェクトにつき4000回/時なので問題なさげ

コード

```js
// 5分おきに呼び出される
function requestGlitch() {
  const urls = [
    'https://XXXXXX.glitch.me',
    'https://YYYYYY.glitch.me',
    'https://ZZZZZZ.glitch.me/?client=gas' // 後述
  ]
  urls.forEach(url => {
    const res = UrlFetchApp.fetch(url, {
      muteHttpExceptions: true
    })
    console.log(res)          
  })
}

// 設定されているトリガー確認
function showTriggers() {
  const triggers = ScriptApp.getProjectTriggers()
  triggers.forEach(trigger => {
    console.log('trigger source id: ' + trigger.getTriggerSourceId())
    console.log('handler function: ' + trigger.getHandlerFunction())
    console.log('event type: ' + trigger.getEventType())
    console.log('----------')
  })
}

// 1回だけ実行
// トリガー作成
function setTrigger() {
  ScriptApp.newTrigger('requestGlitch')
  .timeBased()
  .everyMinutes(5)
  .create()
}
```

`setTrigger()`からの`showTriggers()`を実行すると、トリガーが設定されていることが確認できます
[![Image from Gyazo](https://i.gyazo.com/9d22375ec84b16c315417f355d55538b.png)](https://gyazo.com/9d22375ec84b16c315417f355d55538b)

# さらに工夫する
- Glitchプロジェクト側で他サービスのAPIを呼び出すなどしている場合、このGASからのリクエストはリクエストボディが不正でエラーになったり、不必要に他サービスのAPIを呼び出したりすることになってしまう
- それを避けるために、パラメータに`client=gas`を付与
- Glitch側でパラメータを取得して`client=gas`の場合はそこで処理を止めればよい

## expressを使っている場合
```js
app.get('/', async (req, res) => {
  
    // gasからのリクエストの場合
    const client = req.query.client
    if (client === 'gas') {
        res.send('Glitch woke up')
        return
    }

    // それ以外の場合、処理を継続
    // ...
}
```

 Enjoy your Glitch life!