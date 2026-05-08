---
title: React Testing Libraryの要素取得と非同期テストの基本
tags:
  - React
  - TestingLibrary
  - Vitest
  - TypeScript
private: true
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# React Testing Libraryの要素取得と非同期テストの基本

React Testing Libraryでは、実装の都合ではなく「ユーザーがどう認識するか」に近い形で要素を取得するのが基本です。特に `getByRole` と `name` オプションを使うと、アクセシビリティを意識したテストを書きやすくなります。

## クエリの使い分け

| 種類 | 失敗時の挙動 | 主な用途 |
|---|---|---|
| `getBy...` | 見つからなければ例外 | すでに表示されている要素を取得する |
| `queryBy...` | 見つからなければ `null` | 要素が存在しないことを検証する |
| `findBy...` | Promiseで再試行する | 非同期で表示される要素を待つ |

非同期でDOMが変わる場合は、`render()` の直後に `getBy...` で取得しようとすると間に合わないことがあります。その場合は `findBy...` を使います。

```tsx
render(<UserList />)

expect(await screen.findByRole('heading', { name: 'ユーザー一覧' })).toBeInTheDocument()
```

## `getByRole` と `name`

`getByRole(role, { name })` の `name` は、アクセシブルネームを見ています。アクセシブルネームは、画面上のテキスト、`label`、`aria-label`、`aria-labelledby`、画像の `alt` などから決まります。

```tsx
render(<button>保存</button>)

expect(screen.getByRole('button', { name: '保存' })).toBeInTheDocument()
```

フォームでは `label` と入力要素を関連付けておくと、テストも実装も読みやすくなります。

```tsx
render(
  <>
    <label htmlFor="email">メールアドレス</label>
    <input id="email" />
  </>,
)

expect(screen.getByRole('textbox', { name: 'メールアドレス' })).toBeInTheDocument()
```

## よく使う暗黙のrole

| HTML | role |
|---|---|
| `<button>` | `button` |
| `<a href="...">` | `link` |
| `<input type="text">` | `textbox` |
| `<input type="search">` | `searchbox` |
| `<input type="number">` | `spinbutton` |
| `<input type="checkbox">` | `checkbox` |
| `<input type="radio">` | `radio` |
| `<img alt="...">` | `img` |
| `<h1>`から`<h6>` | `heading` |
| `<ul>` / `<ol>` | `list` |
| `<li>` | `listitem` |

`input type="password"` には暗黙の `textbox` role がありません。そのため、パスワード入力欄は `getByLabelText` で取得すると意図が伝わりやすいです。

## `within` で検索範囲を絞る

同じ役割やラベルを持つ要素が複数ある場合は、先に親要素を取得してから `within` で範囲を絞ります。

```tsx
const list = await screen.findByLabelText('選手一覧')
const player = within(list).getByLabelText(playerId)

expect(within(player).getByRole('img', { name: 'camera' })).toBeInTheDocument()
```

例えば一覧の中に同じボタンが複数ある場合でも、対象の行やカードを先に選ぶと、意図した要素だけを安全に取得できます。

## `userEvent` と `fireEvent`

原則は `userEvent` を使います。クリック、入力、タブ移動など、実際のユーザー操作に近い流れを再現できるためです。

```tsx
const user = userEvent.setup()

await user.click(screen.getByRole('button', { name: '保存' }))
```

`fireEvent` はDOMイベントを直接発火したいとき、または `userEvent` が対応していない特殊なイベントを扱うときに使います。

## Fake Timersの基本

ポーリング、デバウンス、タイムアウトなど、時間に依存する処理はFake Timersで実時間を待たずに検証できます。

```tsx
try {
  vi.useFakeTimers()
  render(<PollingComponent />)

  await vi.advanceTimersByTimeAsync(5000)

  expect(await screen.findByText('更新されました')).toBeInTheDocument()
} finally {
  vi.useRealTimers()
}
```

タイマーを進めることとReactの再描画が完了することは別です。DOM反映まで待ちたい場合は、`findBy...` や `waitFor` を併用します。
