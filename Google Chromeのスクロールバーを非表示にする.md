# やりたいこと

- Google Chromeでサイト閲覧中、右側にでてくるスクロールバーをなくしたい

[![Image from Gyazo](https://i.gyazo.com/94fb1e6d179869848e43b0e96e3de8f7.gif)](https://gyazo.com/94fb1e6d179869848e43b0e96e3de8f7)

- ここ1〜2週間くらい（2020/5/4現在）どのサイトでも表示されるようになったような気がする。前からそうだったっけ…？（そうだったのかもしれない）
- 以前は`chrome://flags`から非表示にする方法もあったみたい（[Chromeでスクロールバーを拡張機能なしで自動非表示にする方法](https://xn--qck4bi9b5gsd.com/2122)）だが、現在ではこのオプションがみつからない

# 方針

- 別の記事では専用のChrome拡張機能をインストールしている方もいるよう
- 誰か（もちろん自分でも可能）がつくったUserCssをどのサイトにも適用できるChrome拡張機能[Stylus](https://chrome.google.com/webstore/detail/stylus/clngdbkpkpeebahjckkjfobafhncgmne)を普段から使っているので、今回はこれを使って解決する
- Stylusの使い方は[ブラウザー拡張機能 Stylus で GitHub Markdown を見やすくする](https://qiita.com/peaceiris/items/31dd73d24b486799e7a4)など参照

# 方法

- Stylusの管理ページ（拡張機能インストール後、アイコン右クリック→`Open Stylus Manager`）で`Write new style`からスタイルの新規作成

[![Image from Gyazo](https://i.gyazo.com/e3bd04d2851ab8f03adad5c6ba550b0a.png)](https://gyazo.com/e3bd04d2851ab8f03adad5c6ba550b0a)

- 今回は特定のドメイン配下でなく全てに適用させたいので、デフォルトで書き込まれている`@-moz-document domain("example.com") {`以下を削除し、以下を記入

```css
::-webkit-scrollbar {
    display: none;
}
```

[![Image from Gyazo](https://i.gyazo.com/c7e13fef172fa9e391279b3ecd493bd0.png)](https://gyazo.com/c7e13fef172fa9e391279b3ecd493bd0)

- これで任意のサイトを開けばスクロールバーが消えるはず！

[![Image from Gyazo](https://i.gyazo.com/0260ffd488ef43c1fda154c576a7e35b.png)](https://gyazo.com/0260ffd488ef43c1fda154c576a7e35b)

おわり