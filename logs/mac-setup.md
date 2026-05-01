# Mac Development Environment Setup

新しく購入したMacBook Proを開発用マシンとして使用するため、基本的なCLIツール導入、Node.js環境構築、GitHub SSH接続設定を実施した。

---

## 目的

このセットアップの目的は、Mac上で以下の開発作業をスムーズに行えるようにすること。

- Homebrewによる開発ツール管理
- Git / GitHubを使ったソースコード管理
- Node.js / npmを使ったJavaScript・React開発
- GitHubへのSSH接続
- 開発用ディレクトリの整理
- モダンなターミナル環境の準備

---

## 1. Homebrew のインストール

Macで各種開発ツールを管理するため、パッケージマネージャであるHomebrewを導入した。

### 実行コマンド

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> ~/.zprofile
source ~/.zprofile
brew -v
```

### 確認結果

```bash
Homebrew 5.1.8
```

### 補足

Homebrewインストール直後は `brew` コマンドが認識されなかったため、`.zprofile` にHomebrewのPATH設定を追加し、`source ~/.zprofile` で反映した。

---

## 2. Git を Homebrew版へ統一

macOSには標準でApple Gitが入っていたが、Homebrewで管理するGitを優先して使用するように設定した。

### 最初の状態

```bash
git --version
```

```bash
git version 2.50.1 (Apple Git-155)
```

### Homebrew版Gitのインストール

```bash
brew install git
```

### Homebrew版Gitを優先するPATH設定

```bash
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zprofile
source ~/.zprofile
```

### 確認コマンド

```bash
which git
git --version
```

### 確認結果

```bash
/opt/homebrew/bin/git
git version 2.54.0
```

### 補足

`/usr/bin/git` のApple Gitではなく、`/opt/homebrew/bin/git` のHomebrew版Gitを使うようになった。

---

## 3. nvm のインストール

Node.jsのバージョンを柔軟に管理するため、nvmを導入した。

nvmはNode Version Managerの略で、プロジェクトごとに異なるNode.jsバージョンを切り替えられる。

### 実行コマンド

```bash
brew install nvm
mkdir ~/.nvm
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zprofile
echo '[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"' >> ~/.zprofile
source ~/.zprofile
```

### 確認コマンド

```bash
nvm --version
```

### 確認結果

```bash
0.40.4
```

---

## 4. Node.js / npm のインストール

JavaScript、React、Viteなどの開発に必要なNode.jsを、nvm経由でインストールした。

### 実行コマンド

```bash
nvm install --lts
```

### 実行結果

```bash
Installing latest LTS version.
Downloading and installing node v24.15.0...
Now using node v24.15.0 (npm v11.12.1)
Creating default alias: default -> lts/* (-> v24.15.0 *)
```

### 確認コマンド

```bash
node -v
npm -v
which node
```

### 確認結果

```bash
v24.15.0
11.12.1
/Users/your_username/.nvm/versions/node/v24.15.0/bin/node
```

### 補足

`which node` の結果が `.nvm` 配下になっているため、Node.jsがnvm管理下で動作していることを確認できた。

---

## 5. GitHub SSH認証設定

GitHubと安全に接続するため、SSH鍵を生成し、GitHubアカウントに公開鍵を登録した。

### SSH鍵の生成

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

### ssh-agent の起動

```bash
eval "$(ssh-agent -s)"
```

### 秘密鍵の登録

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

### 実行結果

```bash
Identity added: /Users/your_username/.ssh/id_ed25519 (your-email@example.com)
```

### 公開鍵のコピー

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

### GitHub側で実施したこと

GitHubの以下の画面からSSH Keyを登録した。

```text
Settings
→ SSH and GPG keys
→ New SSH key
```

### 登録したSSH Key Title

```text
My-MacBookPro
```

### GitHub SSH接続確認

```bash
ssh -T git@github.com
```

### 確認結果

```bash
Hi Hideaki-Umezawa-Private! You've successfully authenticated, but GitHub does not provide shell access.
```

### 補足

`You've successfully authenticated` が表示されたため、GitHubへのSSH認証は成功。

`GitHub does not provide shell access.` はGitHubの通常メッセージであり、エラーではない。

---

## 6. Gitユーザー情報設定

Gitのコミットに使用するユーザー名とメールアドレスを設定した。

Gitでは、コミットごとに「誰が変更したのか」という情報が記録されるため、`user.name` と `user.email` の設定が必要になる。

### 実行コマンド

```bash
git config --global user.name "your-name"
git config --global user.email "your-email@example.com"
```

### 確認コマンド

```bash
git config --global --list
```

### 確認結果の例

```bash
user.name=your-name
user.email=your-email@example.com
```

### 補足

`--global` を指定すると、このMac全体のGit設定として保存される。

個人用Macで同じGitHubアカウントを使う場合は、基本的に `--global` で設定して問題ない。

公開用の学習ログでは、実際のメールアドレスではなく `your-email@example.com` のようなサンプル値に置き換えて記載している。

---


## 8. Warp のインストール

モダンな開発用ターミナルとしてWarpをインストールした。

### 実行コマンド

```bash
brew install --cask warp
```

### 補足

Warpは、コマンド履歴や実行結果をブロック単位で扱えるモダンなターミナルアプリ。

今後、Git、npm、AWS CLI、Terraform、AI系CLIツールを使う際に作業しやすくなる。

---

## 9. Clipy のインストール予定

クリップボード履歴管理ツールとしてClipyを導入予定。

### 参考コマンド

```bash
brew install --cask clipy
```

### 補足

Clipyを使うと、過去にコピーしたテキストを履歴から呼び出せる。

コード、コマンド、プロンプト、URLなどを頻繁にコピーする開発作業では便利。

---

## 現在のセットアップ状況

| 項目 | 状態 |
|---|---|
| Homebrew | 完了 |
| PATH設定 | 完了 |
| Homebrew版Git | 完了 |
| nvm | 完了 |
| Node.js | 完了 |
| npm | 完了 |
| GitHub SSH認証 | 完了 |
| Gitユーザー情報設定 | 完了 |
| workディレクトリ | 完了 |
| Warp | インストール実施 |
| Clipy | 導入予定 |

---

## 現在利用できること

このセットアップにより、以下の作業が可能になった。

- `brew install` によるツール導入
- Homebrew版Gitの利用
- GitHubへのSSH接続
- GitHubリポジトリのclone / pull / push
- Node.js / npmを使ったJavaScript開発
- React / Viteプロジェクトの作成
- `~/work` 配下での開発プロジェクト管理
- Warpによる快適なターミナル操作



---

## 参考資料

今回のMac開発環境セットアップでは、以下の公式ドキュメント・技術記事を参考にした。

- Homebrew公式サイト  
  https://brew.sh/ja/

- Clipy Homebrew Cask  
  https://formulae.brew.sh/cask/clipy

- Qiita: Homebrewを活用したMac初期設定  
  https://qiita.com/ucan-lab/items/e02f2d3a35f266631f24

---



## 用語メモ

| 用語 | 意味 |
|---|---|
| Homebrew | Mac用のパッケージ管理ツール |
| PATH | ターミナルがコマンドを探す場所の一覧 |
| Git | ソースコードの変更履歴を管理するツール |
| Apple Git | macOS標準で入っているGit |
| Homebrew Git | HomebrewでインストールしたGit |
| nvm | Node.jsのバージョン管理ツール |
| Node.js | JavaScriptをブラウザ外で実行するための環境 |
| npm | Node.jsのパッケージ管理ツール |
| SSH | 鍵を使って安全に接続する方式 |
| 公開鍵 | GitHubなど外部サービスに登録する鍵 |
| 秘密鍵 | 自分のMac内だけに保存する鍵 |
| Warp | モダンなターミナルアプリ |
| Clipy | クリップボード履歴管理アプリ |

---

## まとめ

今回のセットアップにより、新しいMacBook Proで開発を始めるための基本環境が整った。

特に、Homebrew、Git、nvm、Node.js、GitHub SSH認証、Gitユーザー情報設定まで完了しているため、JavaScript / React系の開発やGitHub連携はすぐに開始できる状態になっている。
