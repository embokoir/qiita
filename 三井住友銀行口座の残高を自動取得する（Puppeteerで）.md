# やりたいこと・背景
- [三井住友銀行のインターネットバンキング SMBCダイレクト](https://direct.smbc.co.jp/aib/aibgsjsw5001.jsp)で口座にログインし、現在の口座残高を取得
- 他行では個人向けのAPIが公開されていたりする（[個人で動作を試せる銀行系オープンAPIまとめ](https://qiita.com/Puerto/items/c80ecfdd82f070ed2063)等参照）が、三井住友銀行では現在法人向けしかないようす
- MoneyForwardやZaim等のFinTechサービスのAPIを使って取得することはできるかもしれませんが、それらのサービスを日常的には利用していないため、調べていません
- 三井住友銀行のLINE公式アカウントに友だち登録してID連携すると、チャット形式で残高が取得できます。この記事ではよりプログラマブルに取得したい場合を想定しています。たとえば「オレオレ家計簿APIみたいなものをつくりたい」や「残高が一定金額以上（以下）になったら通知させたい」といった応用ができます

# 使用するもの
- Node（v12.16.1）
- npm (v6.13.4)
- [Puppeteer](https://github.com/puppeteer/puppeteer) (v2.1.1)：ヘッドレスブラウザを立ち上げてスクレイピングなどに使えるNodeライブラリ

# 手順
![img](https://gh-card.dev/repos/embokoir/puppeteer-smbc.svg?fullname=)

- すぐに自分自身の口座で試したい場合はソースコードからREADMEに沿って進めればOKです
- 以下はコマンドで進める場合

```shell
$ mkdir puppeteer-smbc
$ cd puppeteer-smbc
$ npm init -y
$ npm install puppeteer dotenv
$ touch server.js .env
```

`.env`（`000...`を自分のものに書き換える）

```
BRANCH_CODE=000 #支店名3桁
ACCOUNT_NUMBER=0000000 #口座番号7桁
PASSWORD=0000 #第一認証4桁
```


`server.js`（そのまま）

```js
const puppeteer = require('puppeteer')
const dotenv = require('dotenv')
dotenv.config()

const BRANCH_CODE = process.env.BRANCH_CODE
const ACCOUNT_NUMBER = process.env.ACCOUNT_NUMBER
const PASSWORD = process.env.PASSWORD;

(async () => {
    // validate .env
    if (!BRANCH_CODE || !ACCOUNT_NUMBER || !PASSWORD) {
        console.log('Invalid Try. Make sure to create a file ".env" and write your BRANCH_CODE / ACCOUNT_NUMBER / PASSWORD')
        return
    }

    // launch browser
    console.log('launching browser...')
    const browser = await puppeteer.launch({
        headless: false,
        args: ['--no-sandbox']
    })

    // go to page
    const page = await browser.newPage()
    await page.setViewport({ width: 1440, height: 2000 })
    await page.setExtraHTTPHeaders({
        'Accept-Language': 'ja'
    })
    const LOGIN_URL = 'https://direct.smbc.co.jp/aib/aibgsjsw5001.jsp'
    await page.goto(LOGIN_URL, { waitUntil: 'domcontentloaded' })

    // set input-data then submit
    await page.type('input[name=S_BRANCH_CD]', BRANCH_CODE)
    await page.type('input[name=S_ACCNT_NO]', ACCOUNT_NUMBER)
    await page.type('input[name=PASSWORD]', PASSWORD)
    await Promise.all([
        page.waitForNavigation({ waitUntil: 'networkidle0' }),
        page.click('input[type=submit]')
    ]).catch(async err => {
        console.log(err.response)
        process.exit(1)
    })

    // redirect to balance-page then get balance
    await page.goto('https://direct3.smbc.co.jp/servlet/com.smbc.SUPRedirectServlet')
    const balance = await page.$eval('.fRight', elm => elm.textContent.replace(/\s/g, ''))
          .catch(async err => {
              console.log(err)
              process.exit(1)
          })
    console.log(`Your current balance is ${balance}`)

    // close browser
    await browser.close()
})()
```

実行（`headless: false`なのでブラウザが立ち上がる）

```shell
$ node server.js
```

以下の赤枠が自動で入力され、
[![Image from Gyazo](https://i.gyazo.com/dee5e9cbc995e9d58cca67f29ab4ea8f.png)](https://gyazo.com/dee5e9cbc995e9d58cca67f29ab4ea8f)

残高部分を取得してコンソールに表示します
[![Image from Gyazo](https://i.gyazo.com/15f03ce33d8c22c788d8020f22d9c4d5.jpg)](https://gyazo.com/15f03ce33d8c22c788d8020f22d9c4d5)

[![Image from Gyazo](https://i.gyazo.com/fa9daa031c6571fdf1e778938b2df856.png)](https://gyazo.com/fa9daa031c6571fdf1e778938b2df856)

# 注意
- 正しく`.env`を設定する。第一認証を一定回数以上間違えるとインターネットバンキングのロックがかかるようです
- 入力部分のセレクタは2020/3/24現在のものです。サイトのHTML構造が変更したら適宜こちらも書き換える

おわり