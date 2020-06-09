# やりたいこと

- タイトルの通り
- ソースを開いて`video`タグを見つけるところまでできても、`ssrweb.zoom.us`で始まるソースのURLを開こうとすると`You don't have authorization to view this page.`になるケースを想定

# 方法

- YouTubeでどなたかが[説明している動画](https://www.youtube.com/watch?v=2ANwf9FJO3I)があるので参照
- 動画では以下のような流れ
  - ①ソースを開いて`video` 要素をコピー
  - ②`body`タグの直下に`download`属性を持たせた`a`要素として複製
  - ③作成された要素を右クリック→`Save Link As...`で保存
- これで十分役割は果たせる
- やや面倒なので、コンソールから次のコードを実行すれば①②はショートカット可能

```js
  const videoElm = document.querySelector('video')
  const videoSrc = videoElm.src
  document.body.insertAdjacentHTML('afterbegin', `<a href='${videoSrc}' download>click here</a>`)
```

- コンソールすら開かずにやりたい場合は、次のコード（内容は同じ）をブックマークレットとして保存しておいてポチッとすればOK

```js
javascript:(()=>{
  const videoElm = document.querySelector('video');
  const videoSrc = videoElm.src;
  document.body.insertAdjacentHTML('afterbegin', `<a href='${videoSrc}' download>click here</a>`);
})();
```

おわり