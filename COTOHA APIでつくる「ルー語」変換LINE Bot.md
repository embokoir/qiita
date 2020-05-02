# やりたいこと・経緯
- 入力した日本語文の一部をカタカナ表記の英語に変換（＝**ルー語**を生成）し返すLINE Botをつくってみる
- ルー語とは：日本語文の一部を単語単位でカタカナ表記の英語に置き換えたルー大柴さんの（流行ったのは十年以上前の）ネタ
- たとえば「言わぬがフラワー」「塵も積もればマウンテン」など
- 実はすでに[ルー語変換](https://lou5.jp/)というウェブサイトがあり、そのサイトの[開発経緯](https://e8y.net/blog/2006/12/31/p139.html)には「文章を形態素解析 → 単語を英訳 → 英語をカタカナ読みに翻訳 」していると書いてある
- 日本語文の形態素解析にはMeCabを利用しているそう。COTOHA APIを使って作ったらどうなるだろうか、という単純な思いつき

# COTOHA APIについて
- https://api.ce-cotoha.com/
- 文の形態素解析や固有表現抽出などの自然言語処理や音声処理をRESTなAPIでやってくれるAPI
- ひととおりに触ってみて、非常に精度がよく、アイディア次第で色々な面白いものがつくれそう
- 今回は「キーワード抽出」のAPIを利用しています

# つくったもの
[![Image from Gyazo](https://i.gyazo.com/1c488d6f2e2536320c6eba2582360b6e.gif)](https://gyazo.com/1c488d6f2e2536320c6eba2582360b6e)
最後のほうに友だち登録リンクものせました

# 仕様
- ユーザーからの日本語文入力を受け取る
    - COTOHA APIのキーワード抽出にかけ、キーワードを抽出
    - 最上位のキーワード（APIからは複数の返り値もありえる）をGoogle 翻訳で英語に変換
    - 英語に変換したキーワードを、カタカナに変換
- ユーザーからの入力のうちキーワードを変換されたカタカナに置き換え、返信

# 技術
- COTOHA APIの[「キーワード抽出」API](https://api.ce-cotoha.com/contents/reference/apireference.html#keyword)。例↓

```js

const BASE_URL = 'https://api.ce-cotoha.com/api/dev/'

async function extractKeywords(documentArray) {
    const urlEndPoint = 'nlp/v1/keyword'
    return axios({
        url: BASE_URL + urlEndPoint,
        method: 'POST',
        headers: {
            'Content-Type': 'application/json;charset=UTF-8',
            'Authorization': `Bearer ${await getAccessToken()}`
        },
        data: {
            'document': documentArray
        }
    }).then(res => {
        const keywords = res.data.result
        if (keywords.length === 0) return false
        return keywords
    }).catch(err => {
        console.log(err.response.status)
        console.log(err.response.data)
        return false
    })
}
```
- ソースコード（環境変数除く）

![img](https://gh-card.dev/repos/embokoir/cotoha-lou-linebot.svg?fullname=)
- サーバーはNode、全面的にasync-await / axios
- デプロイはGithubから[Glitch](https://glitch.com)へクローン
- Google翻訳部分はCloud Translation APIを使うのが精度も速度もいいとは思うものの面倒だったのでGASでそれっぽいAPIを自作 
- 英語→カタカナ変換の部分は[English to Katakana Converter](https://www.sljfaq.org/cgi/e2k.cgi)のAPI（非公式）を使わせていただきました *1

# 考察など
- ルー語は、文のうちどこをどう英語化するかが妙で、ルー大柴さんはその点が絶妙。片っ端から英訳しているわけでもないし、またいわゆる日本語英語みたいなものも織り交ぜている
- 当初はCOTOHA APIの構文解析APIを使用していたが、文からすべての名詞とかを抽出したところで**どれを英訳すると面白くなるのか**という部分で実装が難しくなってくることに気づき、キーワード抽出APIに乗り換えたところ、いい感じにワードを選んでくれた。ちなみにCOTOHA API的に「キーワード」とは「名詞」「主語」「目的語」「反復される語」などの属性を持ちやすい気がした
- ルー語化する文はことわざや早口言葉やお堅い文が向いている

# 結果比較
本家のものは[名言・格言『ルー大柴さんの気になる言葉』一覧リスト](https://iso-labo.com/labo/words_of_lou-Oshiba.html)を参照しました

|        元々の日本語        |                本家ルー語                |                このBot                 |                  ルー語変換サイト                  |
| :------------------------: | :--------------------------------------: | :------------------------------------: | :------------------------------------------------: |
|         言わぬが花         |             言わぬがフラワー             |          **言わぬがフラワー**          |                  言わぬがフラワー                  |
|         一寸先は闇         |              一寸先はダーク              |         **一寸先はダークネス**         |                一寸アフターはダーク                |
|          寝耳に水          |             寝耳にウォーター             |          **寝耳にウォーター**          |                      寝耳に水                      |
|         阿吽の呼吸         |               阿吽のブレス               |         **阿吽のブリージング**         |                    阿吽のブレス                    |
|          三日坊主          |             スリーデイズ坊主             |       **スリーデーズシェーブド**       |                 三デイクルーカット                 |
|        身を粉にする        |            身をパウダーにする            |         **身をパウダーにする**         |               ボディーをミールにする               |
| 二兎を追う者は一兎をも得ず | 二兎をチェイスする者は一兎をもゲットせず | **二兎を追う者はワンラビットをも得ず** | 二兎をランアフターするパースンは一ラビットをも得ず |
|      雨降って地固まる      |        レイン降ってグランド固まる        |       **雨降ってソリディファイ**       |              レイン降ってアース固まる              |

# 結論
- ルー語ってコンピュータ的に生成しようとすると実は結構奥深い
- COTOHA APIが抽出するキーワードは、ルー語の面白さに割と近づけているのでは…？
- 嵐の前のセレニティー、伝家のトレジャーソードあたりが個人的にツボ
- 気になる方は[こちら](line://ti/p/@675gmuga)かQRコードから友だち登録して遊んでみてください
![675gmuga.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/181283/e823e6d4-057f-e2b2-6ddb-087f147b7d34.png)

<br>

#### 注
*1 このBotをGlitchで動かしていたところ、運営者の方よりAmazon関係のサーバからのアクセスは基本的に禁止ということで一時ブロックされましたが、事情を説明し解除していただきました、ありがとうございます。（GlitchのうしろではAWSが動いているみたいですね）