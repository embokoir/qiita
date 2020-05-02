ほぼ備忘録。よく使うメソッドがArrayを破壊するかどうか
ググるときは日本語なら`配列 [メソッド名] 破壊的`、英語なら`array [メソッド名] mutating`あたりがよい

# 破壊する

- `fill()`
- `pop()`
- `push()`
- `reverse()`
- `shift()`
- `sort()`
- `splice()`
- `unshift()`

# 破壊しない

- `concat()`
- `entries()`
- `every()`
- `filter()`
- `find()`
- `forEach()`
- `includes()`
- `indexOf()`
- `join()`
- `keys()`
- `map()`
- `reduce()`
- `some()`

# 参考情報

- 手元の環境はNode v12.16.1
- [Array - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array#)：built-inメソッドの一覧、ブラウザ・Nodeのバージョンごとの対応状況も載ってる
- [Does it mutate?](https://doesitmutate.xyz/)：サンプルコードなどをまとめたサイト