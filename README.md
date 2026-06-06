# 裏スコボみたいなメモ書き EX

![本番適用状況](https://github.com/thex-score/thex-score/actions/workflows/deploy.yml/badge.svg)

## 目次

- [裏スコボみたいなメモ書き EX](#裏スコボみたいなメモ書き-ex)
  - [目次](#目次)
  - [稼働ページ](#稼働ページ)
  - [セットアップ](#セットアップ)
  - [新規記録追加](#新規記録追加)
  - [新規作品追加](#新規作品追加)
  - [UI自体を変更したい場合](#ui自体を変更したい場合)
  - [テスト用CI一覧](#テスト用ci一覧)
  - [Author](#author)


## 稼働ページ

[here!](https://thex-score.net/)

## セットアップ

最初に[docker](https://docs.docker.com/engine/install/)を入れておく。
devcontainerを使用する都合上、開発環境である前提として現在のユーザをdockerグループに入れておくことを推奨する。
ターミナルを開き、以下を入力する。
```bash
$ git clone https://github.com/thex-score/thex-score.git
```
vscodeで開き、`Ctrl + Shift + P`でコマンドを開き `devcontainer` と打ち、 `開発コンテナー: コンテナーでリビルドして開く` で開く。
devcontainerで再度vscodeが起動するのでその状態で `Ctrl + Shift + P` でコマンドを開き、 `create new terminal` で `新しいターミナルを開く` で新しいターミナルを開き、ターミナル上に以下を入力する。
```bash
$ corepack pnpm exec nuxt generate
$ corepack pnpm exec nuxt preview
```
以降 http://localhost:3000 でお試しウェブサーバが動く。停止したければターミナル上で `Ctrl + C` とかで停止させること。

## 新規記録追加

- 全てカレントディレクトリはリポジトリのトップとする
```bash
$ cd thex-score
```

1. `git checkout main` した後, `git pull origin main` し、 `git branch {後述するブランチ名}` としてブランチを切ってから `git checkout {今さっきのブランチ名}` をしてブランチを移動する
   1. ブランチの命名は以下のようにすること
      1. 新しくプレイヤーを登録するときは `feature/init-record-{プレイヤー名}` とする
         1. これは1人のプレイヤーの複数の記録を追加してよいものとする
         2. 例: `feature/init-record-WEF`
      2. 既存のプレイヤーの特定部門の記録を追加するときは `feature/add-record-{プレイヤー名}-{ゲームID}-{機体名}-{スコア}` とする
         1. これは1人のプレイヤーの記録であっても機体毎に 1. の手順から順にブランチを作成して行うこと
         2. 例: `feature/add-record-WEF-th13-Marisa-600404860`
      3. 既存のプレイヤーが記録を更新した場合は `feature/update-record-{プレイヤー名}-{ゲームID}-{機体名}-{スコア}とする`
         1. これは1人のプレイヤーの記録であっても機体毎に 1. の手順から順にブランチを作成して行うこと
         2. 例: `feature/update-record-WEF-th13-Marisa-600404860`
      4. 既存のプレイヤーの既存の記録のリプレイが不明だったが、今回リプレイが見つかったので登録したい場合は `feature/update-record-find-replay-{プレイヤー名}-{ゲームID}-{機体名}-{スコア}` とする
         1. これは1人のプレイヤーの記録であっても機体毎に 1. の手順から順にブランチを作成して行うこと
         2. 例: `feature/update-record-find-replay-WEF-th13-Marisa-600404860`
      5. 既存のプレイヤーの記録が間違っていて修正しなければいけない場合はfeature/modify-record-{プレイヤー名}とする
         1. これは1人のプレイヤーの記録であっても機体毎に 1. の手順から順にブランチを作成して行うこと
         2. プレイヤーの記録ごと消さなければいけない場合はスコアの部分をnullとする
         3. 例: `feature/modify-record-WEF-th13-Marisa-600404860`
         4. 例: `feature/modify-record-WEF-th13-Marisa-null`
      6. 既存のプレイヤー自体を消さなければいけないときはfeature/del-record-{プレイヤー名}とする
         1. 例: `feature/del-record-WEF`
   2. コマンド
      ```bash
      $ git checkout main
      $ git pull origin main
      $ git branch feature/{1-1の手順のブランチ名}  # git branch feature/add-redord-init-WEF など
      $ git checkout feature/{1-1の手順のブランチ名}  # git checkout feature/add-redord-init-WEF など
      ```
2. `composables/ScoreRecords.ts` に記録を追加/更新する
   1. `th06` といったゲームIDや `ReimuA` といった機体IDは `composables/Game.ts` を参照すること
   2. `status` は超大台を超えたものは `great` , 大台を超えたものは `good` と記載する
   3. `date` は[RFC3339](https://tex2e.github.io/rfc-translater/html/rfc3339.html)にて定義されるタイムスタンプフォーマットを使用すること。ただしこのウェブページの仕様として日付までしか表示されないので、時や分は0などの適当な値にしておくこと。
   4. `replay` は入手できずウェブアプリとしてユーザにダウンロードさせることができないものは `null` を記載すること. 入手できたものは `public/replays/{ゲームID}/{リプレイファイル名}` に保存し、 `composables/ScoreRecords.ts` 側にはリプレイファイル名を記載すること
   5. `detail` は備考であり、記載することがなければ `-` と入れる
3. `composables/ReleaseNotes.ts` に変更履歴を記載する
   1. 先頭要素に追加すること
   2. `version` は上から3番目の桁を1上げる
   3. `date` は `yyyy-mm-dd` 形式とする
   4. `changes` は文字列配列として追加した記録を書く
      1. 要素1の文字列配列になる
4. お試しウェブサーバを local で動かして大丈夫そうか確認する
   1. コマンド
      ```bash
      $ corepack pnpm exec nuxt generate
      $ corepack pnpm exec nuxt preview
      # 以降 http://localhost:3000 でお試しウェブサーバが動く.
      # 停止したければ Ctrl + C とかで停止させること.
      ```
5. `git add composable/ScoreRecords.ts composables/ReleaseNotes.ts` で変更したファイルを追加したのち `git commit -m "{ブランチ名のfeature/以降の文字}"` でコミットした後 `git push origin {1.で作ったブランチ名}` でpushする
   1. コマンド
      ```bash
      $ git add composable/ScoreRecords.ts composables/ReleaseNotes.ts
      $ git commit -m "{ブランチ名のfeature/以降の文字}"  # git commit -m "init-record-WEF" など
      $ git push origin feature/{1.で作ったブランチ名}  # git push origin feature/init-record-WEF など
      ```
6. githubの画面に行くとプルリクエストが作れるようになっているので作成する。タイトルはプレイヤー名を新しく追加/削除した場合は『{プレイヤー名}を{追加/削除}』、プレイヤーの新しい記録を追加/更新/修正した場合は『{プレイヤー}の{ゲーム名}の{機体名}のスコア{スコア}を{追加/更新/修正}』といった日本語で書く
7. プルリクエストを作成したら自動でテスト用CIが走るので、テストが通ったら最終チェック実施後mainへマージする
   1. テスト用CIは[テスト用CI一覧](#テスト用ci一覧)を参照
8. mainへマージされるとデプロイのCIが回るので、少し待つと反映される
9. mainへマージし反映を確認後プルリクエストの画面からブランチを削除する


## 新規作品追加

- 全てカレントディレクトリはリポジトリのトップとする
```bash
$ cd thex-score
```

1. `git checkout main` した後, `git pull origin main` し、 `git branch feature/add-game-{ゲームID}` としてブランチを切ってから `git checkout feature/{今さっきのブランチ名}` をしてブランチを移動する
   1. ブランチ名の例: `feature/add-game-th20`
   2. コマンド
      ```bash
      $ git checkout main
      $ git pull origin main
      $ git branch feature/add-game-{ゲームID}  # git branch feature/add-game-th20 など
      $ git checkout feature/add-game-{ゲームID}  # git checkout feature/add-game-th20 など
      ```
2. `composables/Games.ts` にゲームを追加する
   1. 最初のキーは追加する作品のナンバリングとする
      1. 紅魔郷なら `th06` , 風神録なら `th10` , 妖精大戦争なら `th128`
   2. `name` はその作品の名称である
   3. `color` はテーブル上でその作品がどのように表示されるかのデザインを定義する色であり、16進数方式で定義される
      1. `bg` は背景色
      2. `txt` は文字色
   4. `shot_types` はその作品に含まれている機体を定義する
      1. 最初のキーは機体IDを示す
      2. 機体IDの下には `name` だけがキーの辞書があり、`name` には機体名を入れる
3. `composables/ReleaseNotes.ts` に変更履歴を記載する
   1. 先頭要素に追加すること
   2. `version` は上から2番目の桁を1上げる
   3. `date` は `yyyy-mm-dd` 形式とする
   4. `changes` は文字列配列として追加したゲームを書く
      1. 1個のみゲームを追加するので要素1の文字列配列になる
4. お試しウェブサーバを local で動かして大丈夫そうか確認する
   1. コマンド
      ```bash
      $ nvm use 22
      $ corepack enable
      $ pnpm install
      $ pnpm exec nuxt generate
      $ pnpm exec nuxt preview
      # 以降 http://localhost:3000 でお試しウェブサーバが動く.
      # 停止したければ Ctrl + C とかで停止させること.
      ```
5. `git add composable/ScoreRecords.ts composables/ReleaseNotes.ts` で変更したファイルを追加したのち `git commit -m "add-game-{ゲームID}"` でコミットした後 `git push origin {1.で作ったブランチ名}` でpushする
   1. コマンド
      ```bash
      $ git add composable/ScoreRecords.ts composables/ReleaseNotes.ts
      $ git commit -m "add-game-{ゲームID}"  # git commit -m "add-game-th20" など
      $ git push origin feature/add-game-{ゲームID}  # git commit -m feature/add-game-th20 など
      ```
6. githubの画面でプルリクエストを作成する。タイトル名は『{ゲーム名}を追加』といった日本語で書く
7. プルリクエストを作成したら自動でテスト用CIが走るので、テストが通ったら最終チェック実施後mainへマージする
   1. テスト用CIは[テスト用CI一覧](#テスト用ci一覧)を参照
8. mainへマージされるとデプロイのCIが回るので、少し待つと反映される
9. mainへマージし反映を確認後プルリクエストの画面からブランチを削除する


## UI自体を変更したい場合

この作業はnuxtへの深い理解が必要であるという時点でgitへの理解も十分にあるものと考え、定まった手順は作成しないものとする.
フロントエンドに疎い場合は[オンボーディング](docs/onboarding.md)を読んで勉強すること


## テスト用CI一覧

1. `check-replays` はリプレイの重複と存在しないリプレイの参照をチェックしている。これは通らないとマージできない
2. `check-generatable` はエラーを吐かずにウェブページのレンダリングができるかをチェックしている。これは通らないとマージできない
3. `check-releases` は `composables/ReleaseNotes.ts` が確かに更新されているかどうかを `git diff` を取って確認している。これは通らなくてもマージできる


## Author

Created by [Wefmaika](https://wefma.net)