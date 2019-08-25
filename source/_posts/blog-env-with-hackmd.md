# Hexo + HackMD + Github Pages + CircleCI で最高のブログ執筆環境を作る

自分なりの最高のブログ環境を作ってみました

## 用語
* Hexo: 静的サイトジェネレーター. MarkdownからHTMLを生成する
* HackMD: オンラインマークダウンエディター. 最近Githubへのpushへ対応.
* Github pages: Github上で簡単にウェブサイトを構築できる

## 執筆環境
簡単に言えばHackMDで書いて, CircleCIでHexoを実行(コンパイル)して, Github PagesのリポジトリにそのままPushする感じ

### もっとかみくだくと
0. Github上にリポジトリを2つ用意
    1. Github pagesとして公開するためのリポジトリ(xxx.github.ioのmaster branchなど)
    2. HexoやMarkdownファイルを保存するリポジトリ(xxx.githubioのdevelop branchなど)
1. Hexo initしてGithub pagesにdeployできるように設定しておく
2. ブログの執筆環境は[HackMD](https://hackmd.io/). エディター画面右上の項目からVersionsを選ぶとGithubにpushできるため, HexoのMarkdownファイルが保存されるディレクトリを保存先として指定する
 ![](https://i.imgur.com/E9LnSYB.png)
3. Circle CIでHexoやMarkdownファイルを保存しているリポジトリへpushをトリガーとしてインスタンスを起動. nodejsなどのインスタンス上でHexoのインストールとコンパイルを行ない, 成果物をGithub pagesとして公開するためのリポジトリへPush

## 何がいいのか
結局Github PagesでHexoを公開しているだけなので, ローカルでいいじゃんかってなるかもしれないけど, ローカル環境でテキストファイルでブログ記事を管理するのはいかんせん大変でブログを書こうと思ってから書き始めるまでのステップ数が長かったり(ターミナル開いて`hexo new ...`して...), ローカルなのでデータが飛んだり他のデバイスでシームレスに賭けなかったりするのでHackMD.ioを利用すれば複数端末リアルタイムですぐにブログを書きはじめることができるのがいい点ではないかと思います.