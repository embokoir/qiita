# 困ったこと

- 普段、サブスクリプション＆ストリーミングの音楽サービスはSpotifyをプレミアム会員として使っている
- が、SNSなどで他人からプレイリストが共有されるのはSpotifyとは限らない（個人的にはApple Musicであることが多い）
- プレビューで数十秒間聴くことはできるが、Spotifyであれば広告もなくフル尺で聴けるし、できればプレイリストを移植したい
- プレイリスト移植ができなくてもせめて「一曲ごとSpotifyでキーボードを叩いて検索して」再生・プレイリストに追加する手間をなくしたい
- [Soundiiz](https://soundiiz.com/)というサービスは、異なるストリーミングプラットフォーム間のプレイリストを変換してくれる
  - が、そのためにはApple Musicの方でも有料会員になっていて、Soundiizに接続する必要がある
  - さらに一括変換などの便益を受けたければSoundiizにも課金しなければならない
  - 現状そこまでしなくてもいいかな、感

# 解決の方向

- 前提として
  - Apple MusicもSpotifyもPC版ネイティブアプリが使いやすいが、ともにWebでも使うことができる
  - Spotifyの楽曲・アルバム・アーティスト等検索は`https://open.spotify.com/search/[検索語句]`のURLで可能

- これを利用して、Web（PC版）で開いたApple Musicプレイリストの楽曲タイトルを「Spotifyでその楽曲名で検索するリンク」に一括で置き換える
- 検索した後はSpotify側で再生したり、プレイリストに追加したりすればOK。これによって「一曲ごとにSpotifyでキーボードを叩いて検索」する手間は省ける
- JavaScriptの`replaceChild`メソッドを使う
  - `parentNode.replaceChild(newChild, oldChild)`で親要素のうち指定した要素を新しい要素で置換できる
  - [Node.replaceChild - Web API](https://developer.mozilla.org/ja/docs/Web/API/Node/replaceChild)

# デモ＆スクリプト

[![Image from Gyazo](https://i.gyazo.com/bd86a71697e7ba0d3025e0edef9e19a3.gif)](https://gyazo.com/bd86a71697e7ba0d3025e0edef9e19a3)

- すぐに試したい場合は、Apple Musicの適当なプレイリスト（たとえば[はじめてのBillie Eilish](https://music.apple.com/jp/playlist/%E3%81%AF%E3%81%98%E3%82%81%E3%81%A6%E3%81%AE-%E3%83%93%E3%83%AA%E3%83%BC-%E3%82%A2%E3%82%A4%E3%83%AA%E3%83%83%E3%82%B7%E3%83%A5/pl.b5deb01c5d1541ba881b9418f79b5ea6)とか）を開いて、次のスクリプトをコンソールで実行 or アドレスバーに貼り付けて（先頭の`javascript:`が消えたら付け足して）Enterキー押下すれば実現可能（ブックマークレットにすればコンソールを開くことなく毎回実行できるのでおすすめ）

```js
javascript:(()=>{  const songNameWrapperDivElms = [...document.getElementsByClassName('song-name-wrapper')];  songNameWrapperDivElms.forEach(wrapperElm => {    const songNameRawElm = wrapperElm.children[0];    const songName = songNameRawElm.innerText;    const songNameLinkedElm = document.createElement('a');    songNameLinkedElm.setAttribute('href', `https://open.spotify.com/search/${songName}`);    songNameLinkedElm.setAttribute('target', '_blank');    songNameLinkedElm.style.color = 'rgb(28, 185, 92)';    songNameLinkedElm.innerText = songName;    wrapperElm.replaceChild(songNameLinkedElm, songNameRawElm);  });})();
```

- 解説付き（コメント以外は同じ）

```js
javascript:(()=>{
  // 曲名などが子要素に含まれる要素（配列の長さはプレイリストの曲数に一致）
  const songNameWrapperDivElms = [...document.getElementsByClassName('song-name-wrapper')];
  songNameWrapperDivElms.forEach(wrapperElm => {
    // 曲名テキストだけの要素
    const songNameRawElm = wrapperElm.children[0];
    const songName = songNameRawElm.innerText;
    // Spotify検索へのリンクつきの曲名の要素
    const songNameLinkedElm = document.createElement('a');
    songNameLinkedElm.setAttribute('href', `https://open.spotify.com/search/${songName}`);
    songNameLinkedElm.setAttribute('target', '_blank');
    songNameLinkedElm.style.color = 'rgb(28, 185, 92)';
    songNameLinkedElm.innerText = songName;
    // 「曲名テキストだけの要素」を「Spotify検索へのリンクつきの曲名の要素」で置換
    wrapperElm.replaceChild(songNameLinkedElm, songNameRawElm);
  });
})();
```



# 注意など

- 楽曲名が汎用的すぎると（「I love you」など）狙い通りの楽曲が出てこないかもしれない
  - アーティスト名も含めた検索にしてもいいかも
- Apple Musicのプレイリストに入っている曲数が多い場合は、いったん下までスクロールし切ってから実行する
- SpotifyのAPIはかなり強力なので、活用すればさらにパワーアップしてプレイリストの一括置換までいけるはず
- 拡張機能にするのもいいかもしれない…

今回はここまで。Enjoy your Spotify Life!