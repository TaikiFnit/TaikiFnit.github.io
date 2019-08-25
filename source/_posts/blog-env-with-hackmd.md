---
title: Hexo + HackMD + CircleCI + Github Pagesで最高のブログ執筆環境を作る
date: 2019-08-26 03:18:00
tags: Hexo, HackMD, CircleCI, ブログ, GithubPages
---


# Hexo + HackMD + CircleCI + Github Pagesで最高のブログ執筆環境を作る

自分なりの最高のブログ環境を作ってみました

## 用語
* [Hexo](https://hexo.io/)
: 静的サイトジェネレーター. MarkdownからHTMLを生成する
* [HackMD](https://hackmd.io/)
: オンラインMarkdownエディター. 最近Githubへのpushへ対応.
* [Github pages](https://pages.github.com/): Github上で簡単にウェブサイトを構築できる

## 執筆環境
簡単に言えばHackMDで書いて, CircleCIでHexoを実行(コンパイル)して, Github PagesのリポジトリにそのままPushする感じ

### もっとかみくだくと
0. Github上にリポジトリを2つ用意
    1. Github pagesとして公開するためのリポジトリ(xxx.github.ioのmaster branchなど)
    2. HexoやMarkdownファイルを保存するリポジトリ(xxx.github.ioのdevelop branchなど)
1. Hexoの環境設定をしてGithub pagesにdeployできるように設定しておく
2. ブログの執筆環境は[HackMD](https://hackmd.io/). エディター画面右上の項目\*1からVersionsを選ぶとGithubにpushできる*2ため, HexoのMarkdownファイルが保存されるディレクトリを保存先として指定する\*3

\*1
 ![](https://i.imgur.com/E9LnSYB.png)
\*2
![](https://i.imgur.com/lpmJyfj.png)

\*3
![](https://i.imgur.com/O0t4gdS.png)

Hexoでブログ記事のメタ情報を与えるときは, 

```
---
title: 
date: 2019-00-00 00:00:00
tags:
---
```

このようなヘッダ情報をつけますが, これもコピペをするのがめんどくさいと思うので, HackMDのテンプレートとして登録しておくと便利です.

3. Circle CIでHexoやMarkdownファイルを保存しているリポジトリへpushをトリガーとしてインスタンスを起動. nodejsなどのインスタンス上でHexoのインストールとコンパイルを行ない, 成果物をGithub pagesとして公開するためのリポジトリへPush
    * 例えばこんな`circleci.yml`

```circleci.yml
defaults: &defaults
  docker:
    - image: circleci/node:10.9.0
  working_directory: ~/repo

version: 2
jobs:
  build:
    <<: *defaults
    branches:
      only: develop
    steps:
      - checkout
      - run: npm install hexo-cli
      - run: npm install
      - run: npx hexo deploy -m "[skip ci] site updated"

```

## 何がいいのか
結局Github PagesでHexoを公開しているだけなので, ローカルでいいじゃんかってなるかもしれないけど, ローカル環境でテキストファイルでブログ記事を管理するのはいかんせん大変でブログを書こうと思ってから書き始めるまでのステップ数が長かったり(ターミナル開いて`hexo new ...`して...), ローカルなのでデータが飛んだり他のデバイスでシームレスに書けなかったりするのでHackMD.ioを利用すれば複数端末リアルタイムですぐにブログを書きはじめることができるのがいい点ではないかと思います.
