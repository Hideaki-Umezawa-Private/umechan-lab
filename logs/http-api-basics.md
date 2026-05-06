---
title: HTTPメソッドとパラメータの使い分け
tags:
  - HTTP
  - API
  - JavaScript
  - Express
private: true
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# HTTPメソッドとパラメータの使い分け

Web APIでは、HTTPメソッドとパラメータの置き場所を分けると、リクエストの意図が読みやすくなります。ここでは、`fetch` と Express の例で基本を整理します。

## HTTPメソッド

| メソッド | 主な用途 | 冪等性の目安 |
|---|---|---|
| `GET` | データを取得する | あり |
| `POST` | 新しいリソースを作成する | なし |
| `PUT` | リソース全体を置き換える | あり |
| `PATCH` | リソースの一部を更新する | 実装次第 |
| `DELETE` | リソースを削除する | あり |

冪等性とは、同じリクエストを複数回送っても最終的な状態が同じになる性質です。例えば `GET /books/1` は何度呼んでも取得するだけですが、`POST /books` は同じ内容を複数回送ると複数件作られる可能性があります。

## クエリパラメータ

検索、絞り込み、ページングなど、リソースの取得条件を表すときに使います。

```txt
/api/items?status=active&page=1&search=apple
```

```ts
const params = new URLSearchParams({
  status: 'active',
  page: '1',
  search: 'apple',
})

const res = await fetch(`/api/items?${params.toString()}`)
const data = await res.json()
```

```ts
app.get('/api/items', (req, res) => {
  const { status, page, search } = req.query
  res.json({ status, page, search })
})
```

## パスパラメータ

特定のリソースを指すときに使います。

```txt
/api/users/42/posts/100
```

```ts
const userId = 42
const postId = 100

const res = await fetch(`/api/users/${userId}/posts/${postId}`)
const data = await res.json()
```

```ts
app.get('/api/users/:userId/posts/:postId', (req, res) => {
  const { userId, postId } = req.params
  res.json({ userId, postId })
})
```

## リクエストボディ

作成や更新で、まとまったデータを送るときに使います。JSONを送る場合は `Content-Type: application/json` を付け、`JSON.stringify` で文字列化します。

```ts
const newItem = { name: 'apple', price: 100 }

const res = await fetch('/api/items', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(newItem),
})

const result = await res.json()
```

```ts
app.use(express.json())

app.post('/api/items', (req, res) => {
  const { name, price } = req.body
  res.status(201).json({ name, price })
})
```

## 使い分けの早見表

| 目的 | 置き場所 | 例 |
|---|---|---|
| 検索・絞り込み | クエリパラメータ | `/items?status=active` |
| 特定リソースの指定 | パスパラメータ | `/items/123` |
| 作成・更新データの送信 | リクエストボディ | `{ "name": "apple" }` |

## 注意点

`GET` リクエストに秘密情報をクエリパラメータで載せるのは避けます。URLはブラウザ履歴、サーバーログ、アクセス解析などに残りやすいためです。認証情報はヘッダーや安全な認証フローで扱います。
