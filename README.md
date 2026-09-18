# STREME — 店内サポートチャットbot

Spremeの店員にインスパイアされた、超高速・ぶっきらぼうなショッピングモール向けチャットbotです。
単一自己完結HTML（`streme.html`、依存ゼロ）で、WordpressのCustom HTMLにそのまま貼り付けられます。

## 振る舞い（全ルール、キーワード判定で確定）

| 入力 | 返答 |
|---|---|
| 最初に開いたとき | 「うす。」 / 「何？」 |
| 在庫系（ある・在庫・値段・色…、**ピンズ以外**） | 「ない。」 |
| サイズ指定（L / size / サイズ…） | 「全部ない。」 |
| ピンズ | 「ピンズはレジ。」 |
| 表示の服に触る（クリック／ドラッグ） | 「それディスプレイなんで触んないでもらっていいすか」 |
| その他の質問 | 「知らない。」 |
| 3ターン目以降 | 返答 +「もういい？」で会話を切り上げ |
| 8秒放置 | 「……何？」 / 「……うす」（立ち去らせない） |

超高速 = 1文字26msで打字表示（reduced-motion時は即表示）。WebAudioで短いブザー（`音:`でON/OFF、localStorage保持）。

## Wordpressで公開する（2案）

### 案A：iframe埋め込み（推奨・一番確実）
JS/CSSを触る必要がなく、WPエディタの改行・エスケープに邪魔されません。

1. `streme.html` をアップロード（メディアライブラリ or FTPで `wp-content/uploads/streme/` 配下）。
2. 記事／ページで **Custom HTML ブロック** を追加し、以下を貼る：

```html
<div style="max-width:440px;margin:0 auto">
  <iframe src="/wp-content/uploads/streme/streme.html"
          style="width:100%;height:640px;border:0;display:block"
          title="STREME 店内サポート"></iframe>
</div>
```
（`src`は実際の配置先パスに書き換えてください）

### 案B：中身をCustom HTMLにそのまま貼る
`streme.html` の `<style>…</style>`〜`<script>…</script>` 部分（`<div class="streme-wrap">…` の中身含む）を
Custom HTML ブロックに貼り付け。外部依存がないのでそのまま動きます。
※ `<style>` と `<script>` はそれぞれブロック内に1つにまとめて貼ってください。

## 調整ポイント（`streme.html` 内の `L` と `RE` 変数）
- 言い回しバリエーション → `L`（hello/stock/size/pin/touch/dontknow/over/over2/idle）
- 分類キーワード → `RE`（size/touch/stock/pin の正規表現）
- 打字速度 → `addBot` 内 `setTimeout(tick, 26)`
- 締めターン → `respond` 内 `T.turn >= 3`
- アイドル発火 → 最下部の `setInterval`（8000 / 16000 ms）
