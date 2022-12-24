---
title: "BuildspaceのDapps作成チュートリアルのウォークスルー（途中まで）"
emoji: "🦄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["buildspace"]
published: true
---

これは[Web3 Devのカレンダー \| Advent Calendar 2022 \- Qiita](https://qiita.com/advent-calendar/2022/web3-dev)11日目の記事です。今日が何日かって？24日です。Better than never!

私はWeb3開発の経験がないので、チュートリアルでとにかく何かを作ってみることにしました。[buildspace](https://buildspace.so/)はブロックチェーンを使ったさまざまなチュートリアルを無料で提供しています。ありがたい。今日は __Intro to Solidity: Ship an Ethereum dApp__ というチュートリアルをやってみる。ちなみに2週間かかると書いてある。今から（16:55）どこまでできるか見ものです。

チュートリアルの詳しい内容についてはふれませんので、直接[buildspace](https://buildspace.so/)を確認ください。

# hardhatをインストールする

hardhatはイーサリアムのアプリをつくるなら絶対に使うであろう、コンパイル、デプロイ、テストなど何でもできる開発に欠かせないライブラリです。これをインストールします。

`npm install hardhat`

# プロジェクトを作成する

`npx hardhat` すると以下のように丁寧に作りたいプロジェクトを聞いてくれます。

```
➜  my-wave-portal npx hardhat
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.12.4 👷‍

? What do you want to do? …
❯ Create a JavaScript project
  Create a TypeScript project
  Create an empty hardhat.config.js
  Quit
```

今回はJavaScript Projectを選びます。

チュートリアルにサンプルのHardhat taskが記述してあるので、それをコピペします。サンプルのアカウントを作成・表示するものでした。`npx hardhat <サンプルタスク名>` を実行すると、サンプルのアカウントを表示してくれます。

# コンパイルして、テストしてみる

`npx hardhat compile` すると以下の文言が表示されて無事にcompileできました。

```
➜  my-wave-portal  npx hardhat compile
Downloading compiler 0.8.17
Compiled 1 Solidity file successfully
```

`npx hardhat test` でテストを流してみます。すべて通りました。これはプロジェクト作成時に自動で追加されたテストなので消しておきます。

ちなみに自動で作成されたディレクトリはこんなかんじ。

```
➜  my-wave-portal git:(main) ✗ tree  -I "node_modules"
.
├── README.md
├── artifacts
│   ├── build-info
│   │   └── 14f7eb130f02e4d9abcd68528a373e96.json
│   └── contracts
│       └── Lock.sol # <= Solidityファイル
│           ├── Lock.dbg.json
│           └── Lock.json
├── cache
│   └── solidity-files-cache.json
├── contracts
│   └── Lock.sol
├── hardhat.config.js
├── package-lock.json
├── package.json
├── scripts
│   └── deploy.js
├── test
│   └── Lock.js # <= テストコード
```

# Solidityを書いていく

チュートリアルに沿ってSolidityを書いていきます。
今回は、「ウォレットを接続して、Wave（手をふる）を送る」だけ、というシンプルなDappを作成します。

まずは、`console.log` で文字列が出るように確認します。

```solidity
contract HelloWorld {
    constructor() {
        console.log("Hello, web3");
    }
}
```

これをコンパイルして、ローカルのブロックチェーンにデプロイしていきます。

Solidityはブロックチェーンにデプロイしないといけないので、まずはその環境をローカルに作ります。というか、hardhatがいい感じに作ってくれます。

一つのjsファイルに、ローカル環境の構築、コンパイル、デプロイをするコードを書いていきます。

`hre` は `Hardhat Runtime Environment`の略で
、さまざまなテスト用環境を提供してくれます。

このファイルは `scripts` ディレクトリ配下に配置します。

コードを書いたら、`npx` コマンドでコンパイル、デプロイをします。

```
npx hardhat run scripts/run.js
```

実行してみると、`console.log` に書いたコードが出力されます。

```
➜  $ npx hardhat run scripts/run.js
Compiled 2 Solidity files successfully
Hello, web3
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

# 基本のコードを書いてみる

送られたWaveを保存(store)しておくコードと、Waveの数を取得するためのfunctionを記述します。

コードの詳細はbuildspace](https://buildspace.so/)を確認してください。

`msg.sender` というコードがでてきます。ここには、functionを呼んだ人のウォレットアドレスが入ります。

また、`function wave() public ~ ` というように `public` というワードが出てきます。これは、ブロックチェーン上で使えるようになる公開APIエンドポイントというふうに表現されていました。なるほど！

作成したエンドポイントを利用し、deploy先、deployもと、最初のWave数、Waveする関数の実行、実行後のWave数を出力するよう、 `run.js` スクリプトを書きかえて実行します。

```
$ npx hardhat run scripts/run.js
Compiled 1 Solidity file successfully
Hello, web3
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
Contract deployed by: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
We have 0 total waves!
0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 has waved!
We have 1 total waves!
```

動いている！イエーイ！

# ローカルのネットワークを作成する

なんとここまでのコードはただSolidityをスクリプトでながしていただけで、実際のネットワーク上にデプロイしたものではありませんでした。なのでここからはローカルにイーサリアムのネットワークを作成して、そこにコードをデプロイします！

```
npx hardhat node
```

というコマンドを別ウィンドウで実行します。
`node` は `Node.js` のことではないので注意です。

すると、ローカルのサーバーURLと20個のアドレスが生成されます。

このネットワークに新しくブロックを作成して、コードをデプロイします。わーい！

`deploy.js` ファイルを新規作成します。
コードの詳細はbuildspace](https://buildspace.so/)を確認してください。

`deploy.js` スクリプトを使って、ローカルのネットワークにデプロイします！

```
npx hardhat run scripts/deploy.js --network localhost
```

ドン！

```
$ npx hardhat run scripts/deploy.js --network localhost
Deploying contracts with account:  0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
Account balance:  10000000000000000000000
WavePortal address:  0x5FbDB2315678afecb367f032d93F642f64180aa3
```

`npx hardhat node` を実行したほうのターミナルには以下が出力されます。

```
  Contract deployment: WavePortal
  Contract address:    0x5fbdb2315678afecb367f032d93f642f64180aa3
  Transaction:         0xea5eff3e0a84f53408241b46486f0a9edbea6d84c18fc9cc5cf43918290e01d4
  From:                0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
  Value:               0 ETH
  Gas used:            345274 of 345274
  Block #1:            0x0e5cd5a982cf969e34b3e3b45e3a534f2bc9a59f6de5eec463ab59dc03c44da4

  console.log:
    Hello, web3
```

やった！！！！

console.logは別に出るんですね。

# おわりに

チュートリアルは、ここからReactアプリの制作と、MetaMaskのセットアップを行います。
私は最近小さく試したいアプリは[Glitch: The friendly community where everyone builds the web](https://glitch.com/)を使っているので、こちらにデプロイできるかどうか調べてみようと思っています。

Happy Christmas! 🎄 (23:01)
