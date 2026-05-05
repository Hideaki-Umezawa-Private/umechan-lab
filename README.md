# umechan-lab

学習ログを `logs/` 配下のMarkdownとして管理するリポジトリです。

## Qiita投稿

`main` ブランチに `logs/**/*.md` の変更が入ると、GitHub ActionsでQiitaへ投稿します。

事前にGitHub ActionsのRepository secretへ `QIITA_TOKEN` を登録してください。Qiitaのアクセストークンには `read_qiita` と `write_qiita` が必要です。

初回投稿時に意図せず全体公開しないよう、記事のfront matterは `private: true` にしています。公開したい記事だけ `private: false` に変更してください。
