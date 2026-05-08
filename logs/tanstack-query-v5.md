---
title: TanStack Query v5の状態管理を整理する
tags:
  - React
  - TanStackQuery
  - TypeScript
private: true
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# TanStack Query v5の状態管理を整理する

TanStack Queryは、サーバー状態を扱うためのライブラリです。APIレスポンス、ローディング、エラー、キャッシュ、再取得などを、自前の `useState` と `useEffect` だけで組み立てるより宣言的に扱えます。

```tsx
const { data, isPending, isFetching, isError } = useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers,
})
```

## クライアント状態とサーバー状態

| 種類 | 例 | 管理方法 |
|---|---|---|
| クライアント状態 | モーダル開閉、入力フォーム、選択中タブ | `useState`、`useReducer` など |
| サーバー状態 | APIで取得したユーザー一覧、詳細データ | TanStack Query |

TanStack QueryはUI状態の管理ツールではありません。サーバーから取得したデータのキャッシュ、再取得、同期を担当します。

## v5で押さえる状態フラグ

TanStack Query v5では、初回データなしの状態は `pending` と表現されます。`isLoading` だけを見ると混乱しやすいので、まずは役割を分けて覚えるのが安全です。

| フラグ | 意味 | よく使う場面 |
|---|---|---|
| `isPending` | まだ表示できるデータがない | 初回の大きなローディング |
| `isFetching` | 現在リクエスト中 | 再取得中の小さなインジケーター |
| `isLoading` | 初回取得中 | `isPending && isFetching` と同じ |
| `isSuccess` | クエリが成功状態 | データ描画の条件 |
| `isError` | エラー状態 | 初回失敗や再取得失敗の分岐 |
| `isRefetching` | 初回ではない再取得中 | バックグラウンド更新表示 |

## 実務で使いやすい描画分岐

```tsx
if (isPending) {
  return <FullScreenLoading />
}

if (isError) {
  return <ErrorView />
}

return (
  <>
    {isFetching && <SmallSpinner />}
    <UserList users={data} />
  </>
)
```

再取得中も前回のデータを表示し続けたい場合、`isFetching` で画面全体を消さず、小さな更新中表示にとどめると体験が崩れにくくなります。

`isPending` が終わったあとでも、再取得が走ると `isFetching` は `true` になります。初回表示とバックグラウンド更新を分けて考えると、画面のちらつきを減らせます。

## `isSuccess` を副作用のトリガーにしない

`isSuccess` は状態です。成功した瞬間だけを表すイベントではありません。トースト表示や画面遷移などの副作用を実行したい場合は、更新系の `useMutation` で `onSuccess` を使うのが自然です。

```tsx
const mutation = useMutation({
  mutationFn: saveUser,
  onSuccess: () => {
    toast.success('保存しました')
    queryClient.invalidateQueries({ queryKey: ['users'] })
  },
})
```

## Mutationのコールバック

| コールバック | タイミング | 主な用途 |
|---|---|---|
| `onMutate` | 通信前 | 楽観的更新、直前状態の退避 |
| `onSuccess` | 成功時 | トースト、画面遷移、キャッシュの無効化 |
| `onError` | 失敗時 | エラー通知、ロールバック |
| `onSettled` | 成否どちらでも最後 | 共通の後処理 |
