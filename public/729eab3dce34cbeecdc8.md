---
title: Node.jsでよく使うツールの入口
tags:
  - Node.js
  - Express
  - ffmpeg
  - Sharp
private: true
updated_at: '2026-05-10T20:51:11+09:00'
id: 729eab3dce34cbeecdc8
organization_url_name: null
slide: false
ignorePublish: false
---

# Node.jsでよく使うツールの入口

Node.js周辺でよく出てくるツールを、用途ごとに短く整理します。最初は「何に使う道具か」を押さえるだけでも十分です。

## Express

Expressは、Node.jsでHTTPサーバーやAPIを作るための軽量なWebフレームワークです。ルーティング、リクエスト処理、レスポンス返却をシンプルに書けます。

```ts
import express from 'express'

const app = express()

app.get('/', (req, res) => {
  res.send('Hello, World!')
})

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000')
})
```

JSONリクエストボディを扱う場合は、ルーティングより前に `express.json()` を設定します。これにより、POSTされたJSONを `req.body` から読めるようになります。

```ts
app.use(express.json())
```

## sharp

`sharp` はNode.jsで画像を処理するためのライブラリです。リサイズ、フォーマット変換、圧縮、切り抜き、合成などを高速に行えます。

| 処理 | 例 |
|---|---|
| リサイズ | `.resize(400, 400)` |
| 切り抜き | `.extract({ left, top, width, height })` |
| フォーマット変換 | `.toFormat('webp')` |
| JPEG圧縮 | `.jpeg({ quality: 80 })` |
| 回転 | `.rotate(90)` |
| 合成 | `.composite([{ input: 'overlay.png' }])` |
| モノクロ化 | `.grayscale()` |

```ts
import sharp from 'sharp'

await sharp('input.png')
  .resize(800)
  .webp({ quality: 80 })
  .toFile('output.webp')
```

公開画像を扱う場合は、EXIFなどのメタデータに位置情報や撮影機材情報が含まれることがあります。sharpは通常、メタデータを引き継がずに出力します。必要な場合だけ `keepMetadata()` や `withMetadata()` を使う、と覚えると安全です。

## ffmpeg

FFmpegは、動画や音声を変換・圧縮・切り出しできるCLIツールです。Node.jsからは子プロセスやラッパーライブラリ経由で利用できます。

```bash
ffmpeg -i input.mov -vf scale=1280:-1 output.mp4
```

動画処理はCPU負荷が高く、実行環境の依存も出やすいため、ローカル開発、CI、本番のバージョン差に注意します。
