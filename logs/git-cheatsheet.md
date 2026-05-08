---
title: Gitコマンドチートシート
tags:
  - Git
  - GitHub
private: true
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# Gitコマンドチートシート

Gitでよく使う操作を、目的別に整理します。危険な操作には注意点も添えています。

## コミットメッセージの接頭辞

| 接頭辞 | 用途 |
|---|---|
| `feat` | 新機能追加 |
| `fix` | バグ修正 |
| `docs` | ドキュメント変更 |
| `test` | テスト追加・修正 |
| `refactor` | 動作を変えない改善 |
| `style` | フォーマットやLint修正 |
| `chore` | 雑務的な変更 |
| `ci` | CI/CD設定変更 |
| `build` | ビルド関連変更 |
| `revert` | 変更の取り消し |

## 基本操作

| コマンド | 用途 |
|---|---|
| `git status` | 変更状況を確認する |
| `git diff` | 未ステージの差分を見る |
| `git add <file>` | 特定ファイルをステージする |
| `git add .` | カレントディレクトリ以下の変更をステージする |
| `git commit -m "message"` | コミットを作成する |
| `git log --oneline` | 履歴を1行で確認する |

## ブランチ操作

| コマンド | 用途 |
|---|---|
| `git branch` | ブランチ一覧を確認する |
| `git switch main` | `main` ブランチへ移動する |
| `git switch -c feature/login` | 新しいブランチを作って移動する |
| `git push -u origin feature/login` | 新規ブランチを初回pushし、upstreamを設定する |
| `git push origin --delete old-branch` | リモートブランチを削除する |

新しい環境では、ブランチ移動には `switch`、ファイルの復元には `restore` を使うと覚えやすいです。古い記事では `checkout` がよく出てきますが、役割が広いため最初は混乱しやすいです。

## マージとリベース

| コマンド | 用途 |
|---|---|
| `git merge <branch>` | 他ブランチの変更を統合する |
| `git rebase main` | 現在のブランチの起点を `main` の先頭へ移す |

共有済みブランチをrebaseしてforce pushすると、他の人の作業に影響することがあります。チーム運用では、事前にルールを合わせておくのが安全です。

## やり直し

| コマンド | 用途 | 注意 |
|---|---|---|
| `git restore <file>` | 作業ディレクトリの変更を戻す | 未保存の変更は消える |
| `git restore --staged <file>` | ステージから外す | ファイルの中身は残る |
| `git reset --soft HEAD^` | 直前のコミットだけ取り消す | 変更はステージに残る |
| `git reset --mixed HEAD^` | 直前のコミットとステージを戻す | 変更は作業ディレクトリに残る |
| `git reset --hard HEAD^` | コミット・ステージ・作業内容を戻す | 変更が消えるため危険 |
| `git revert <commit>` | 打ち消しコミットを作る | 共有済み履歴では安全 |

`git reset HEAD^` は `git reset --mixed HEAD^` とほぼ同じ意味です。初心者のうちは、挙動がわかりやすいように `--soft`、`--mixed`、`--hard` を明示すると安全です。

迷ったら、共有済みの履歴では `reset --hard` より `revert` を検討します。`reset --hard` は未コミットの変更も消すため、実行前に `git status` で状態を確認します。

## 一時保存

```bash
git stash
git stash list
git stash apply stash@{0}
git stash pop
```

`apply` はstashを残したまま適用し、`pop` は適用後にstashから削除します。

## リモート操作

| コマンド | 用途 |
|---|---|
| `git clone <URL>` | リポジトリを取得する |
| `git fetch` | リモートの更新情報を取得する |
| `git pull origin main` | リモートの変更を取り込む |
| `git push origin <branch>` | ブランチをリモートに送る |
| `git remote -v` | リモートURLを確認する |
