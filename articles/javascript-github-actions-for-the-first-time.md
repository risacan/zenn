---
title: "初めて GitHub Actions (with JavaScript) を作るウォークスルー"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["GitHubActions"]
published: true
---

この記事は、GitHub Actions の公式チュートリアルをやってみた、という日記です。
チュートリアルそのものではないので、注意してください。

GitHub Actions はひな形が用意してあります。便利〜。

[actions javascript -action: Create a JavaScript Action with tests, linting, workflow, publishing, and versioning](https://github.com/actions/javascript-action)

![](https://storage.googleapis.com/zenn-user-upload/0aa7q157lu70twnoigs4y4gbksj6)
`Use this template` をクリックするとレポジトリ作成に進みます。

レポジトリの名前を入力すると、レポジトリができます。

![](https://storage.googleapis.com/zenn-user-upload/mbyn0jxxbsk6280fepay2sfp6m0h)
作りたいものはなんとなくイメージしているけど、アクションを作るのはこれが初めて。

 `Hello World!` を出力するアクションを作るチュートリアルと、完成形のレポジトリが公開されているので、これに沿って一度手を動かして使ってみます。チュートリアルを一度なぞるだけで、理解度が変わってくるので、初めてなら（退屈そうに見えたとしても）手を動かすことをおすすめします。

サンプルのレポジトリを見てみましょう。

[actions/hello-world-javascript-action: A template to demonstrate how to build a JavaScript action.](https://github.com/actions/hello-world-javascript-action)

`README.md`を見ると、このアクションの使い方がわかります。

![](https://storage.googleapis.com/zenn-user-upload/ev1bsrcbzvjae162likrmjsm3uz7)

`This action prints "Hello World" or "Hello" + the name of a person to greet to the log`

> このアクションは、"Hello World" または "Hello" + 指定した名前 をログに表示します。

 `Input` に `who-to-greet` に挨拶する相手の名前を指定します。
アウトプットとして、 `time` 挨拶をした時間　がわかるみたいです。

アクションの作り方は日本語でも紹介されています。 yatta~

[JavaScript アクションを作成する](https://docs.github.com/ja/free-pro-team@latest/actions/creating-actions/creating-a-javascript-action)

いよいよ初めての GitHub Actions を作っていきます。
nodeの12の利用が推奨されているので、ローカル環境にnodeをインストールします。

最初は `action.yml` の更新です。
テンプレートから作成したので、すでにサンプルの内容が書かれていますが、チュートリアルに沿って書き換えます。
`action.yml`

```jsx
name: 'こんにちは、世界'
description: 'あいさつして、挨拶した時間を表示します'
inputs:
  who-to-greet:
    description: 'あいさつする相手'
    required: true
    default: '世界'
outputs:
  time:
    description: 'あいさつした時間'
runs:
  using: 'node12'
  main: 'dist/index.js'
```

次に、ツールキットをインストールします。ツールキットは、GitHub Actions につかえる便利なモジュールです。コンソールで以下のコマンドを実行します。

```
npm install @actions/core @actions/github
```

いよいよActionを書くぞ〜！

`index.js` に書いていきます。
サンプルのコードを書くときはどこか一箇所でも変えてみると楽しいです。例えば今回は、あいさつを日本語にしてみました。

```jsx
const core = require("@actions/core");
const github = require("@actions/github");

try {
  const nameToGreet = core.getInput("who-to-greet");
  console.log(`こんにちは ${nameToGreet}!`); // ログに出力
  const time = new Date().toTimeString();
  core.setOutput("time", time); // アウトプット

  const payload = JSON.stringify(github.context.payload, undefined, 2);
  console.log(`The event payload: ${payload}`);
} catch (error) {
  core.setFailed(error.message);
}
```

処理ができたので、変更内容をコミットします。

```bash
git add action.yml index.js
git commit -m ":sparkles: Add actions"
```

`action.yml` に `main: 'dist/index.js'` と記述したので実行される `index.js` は `dist` 配下のものになります。  `npm run prepare` を実行すると、 `./dist/index.js` が追加されます。

```bash
git add ./dist
git commit -m ":wrench: npm run prepare"
```

完成したパッケージを試すためのコードを追加します。

`.github/workflows/main.yml`

```jsx
on: [push]　# アクションを実行するタイミング

jobs:
  hello_world_job:
    runs-on: ubuntu-latest
    name: あいさつするよ〜
    steps:
      # プライベートリポジトリで実行する場合は、以下の二行が必要
      - name: Checkout
        uses: actions/checkout@v2
      # アクションを実行する（あいさつログを出力）
      - name: Hello world action step
        uses: ./ # ルートディレクトリのアクションを実行する
        id: hello
        with:
          who-to-greet: '世界'
      # アウトプットを取得する
      - name: Get the output time
        run: echo "時間は {{ steps.hello.outputs.time }}"
```

```bash
git add .github/workflows/main.yml
git commit -m ":memo: Add script to execute action"
```

GitHub にプッシュします

```bash
git push
```

GitHub を開いて、 `Action` タブを確認します。

![](https://storage.googleapis.com/zenn-user-upload/5ik4txdgbuu7y7280r4ja43xn4tr)

すると、 `.github/workflows/main.yml` の実行結果が確認できます。

![](https://storage.googleapis.com/zenn-user-upload/zl30ph1vcfa33hppoqpe28ng38tl)
アクションが実行されています！中身を見てみましょう。コミットのタイトルをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/l1eyywqbuc6nm2cqvtnn5r1kgvds)

あいさつはログに出力されています！

時間はどうでしょうか。

![](https://storage.googleapis.com/zenn-user-upload/houe9xhgdfdry9vad0rwj12ds2rx)

時間を保存したキーがうまく展開されていないみたいなので、修正します。 `.github/workflows/main.yml` の`{{ steps.hello.outputs.time }}` を `${{ steps.hello.outputs.time }}` に修正し `git push` します。
`steps` や `hello` は `.github/workflows/main.yml` の キーを表しています。
`index.js` に記述した `core.setOutput("time", time);` によって、 `outputs.time` で時間が取得できます。

変更をプッシュすると、Actionは自動的に実行されます。

![](https://storage.googleapis.com/zenn-user-upload/y780nk799fz2tp2552fiobpyafux)

Yay! はじめての GitHub Actions が完成しました 🎉
GitHub Actions をマーケットプレイスに公開する場合は、 [GitHubマーケットプレイスでのアクションの公開-GitHubDocs](https://docs.github.com/en/free-pro-team@latest/actions/creating-actions/publishing-actions-in-github-marketplace) を参考にしましょう。