- [Glitch](https://glitch.com/)：Webサイト・APIなどをデプロイできるサービス
- [Puppeteer](https://github.com/puppeteer/puppeteer)：ヘッドレスブラウザを立ち上げてスクレイピングなどに使えるNodeライブラリ。GoogleのChrome DevToolsチームが開発してる

ローカルのNodeで開発したPuppeteer利用APIをGlitchにデプロイして起きたエラーの解消メモ

# コード（失敗時）
一般的な以下のような書き方で実行すると…

```javascript
const puppeteer = require('puppeteer')

(async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://example.com')
  await page.screenshot({path: 'example.png'})

  await browser.close()
})()
```

# エラー
次のようなエラーがログに吐き出される

```
UnhandledPromiseRejectionWarning: Error: Failed to launch the browser process!
No usable sandbox! Update your kernel or see https://chromium.googlesource.com/chromium/src/+/master/docs/linux_suid_sandbox_development.md for more information on developing with the SUID sandbox. If you want to live dangerously and need an immediate workaround, you can try using --no-sandbox.
```

# コード（成功時）
エラーメッセージに出ているように、`--no-sandbox`オプションをつけて`puppeteer.launch()`すればOKなので

```javascript
(async () => {
  const browser = await puppeteer.launch({
  	args: ['--no-sandbox']
  })
  const page = await browser.newPage()
  await page.goto('https://example.com')
  await page.screenshot({path: 'example.png'})

  await browser.close()
})()
```

で無事実行可能に。[Glitchのディスカッション](https://support.glitch.com/t/puppeteer-headless-browser-in-node-app/2804/14)をみると、Glitchの各プロジェクトはDockerコンテナ内で実行されるためsandboxをrunできない、とのことだった