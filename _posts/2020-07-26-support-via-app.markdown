---
layout: post
title: VIA対応やってみました
date: 2020-07-26 10:00:00 +0900
tag: [hardware, keyboard]
image: via_rs_01.jpg
---

## VIAってなに？

　QMK対応キーボードのキーマップを書き換えるアプリケーションです。  
　通常、QMK対応キーボードのキーマップを書き換えるには、  

- QMKの開発環境を整えてからC言語のキーマップファイルを編集、自分のパソコンでmakeする
- QMK Configuratorというwebアプリでキーマップを編集しbakeする

のどちらかを行ってhexファイルを作成したあと、書き込みアプリのQMK Toolbox等で書き込む必要があるのですが、VIAに対応した場合、  

- VIAアプリケーションをPCにインストール
- ベースとなる作成済みのhexファイルをQMK Toolbox等で書き込む
- VIAを起動したら（公式対応していない場合はjsonファイルをインポートしたのち）キーボードを自動認識するのでキーマップを編集。編集はその場でキーボードに反映される

といった感じで、一度hexファイルをキーボードに書いてしまえばあとはVIAアプリからキーマップを直接編集可能になる優れものです。  
ただし、キーボード作者もしくは有志がVIA用のhexファイルとjsonファイルを作成するためのキーマップをあらかじめ作成していることが条件となります。  

　キーボード設計者的には、keymap.cにコーディングしているようなそのキーボード特有の機能をQMK Configuratorでは使えないわりに、対応作業や確認作業がちょっと面倒くさかったりしてイマイチだなぁと感じていたのですが、VIAの場合はQMK上で作り込んだキーマップをVIAキーマップ用のフラグを立てて使うので、keymap.cの中の機能はそのままにVIAからキーマップを変更出来るようになります。

　また、VIA用のHexファイルとjsonファイルを用意するだけで済むので、これまでQMK開発環境をユーザーに整えやすくするためにQMK本体にプルリクしていた手間を省けます（でも作者的には一度はプルリクして自分のコードのダメさをレビューしてもらったほうが良いかも…？）

　ユーザー側はQMK toolboxとVIAを入れるだけで済み、実際面倒くさい開発環境の構築の手間が無くなるのですごく易しくなります。  

　実は存在自体は以前から知っていたのですが、どうせ対応が面倒だったり公式にPRしないとつかえないでしょ？みたいに思って調べもしてなかったんですが、先日サリチル酸さんがVIAの記事を出されてて読んだら結構便利やん？意外とやることは少ないやん？と思ったので連休中に対応してみました。  

　ということで、使い方などは丁寧に書かれたサリチル酸さんの記事をおよみください。  
[（初心者編）VIAを使ってキーマップを書き換えよう](https://salicylic-acid3.hatenablog.com/entry/via-manual)

　キーボード設計者の方の対応方法については以下を。  
[（設計者向け）VIA対応のファームウェアを作ろう](https://salicylic-acid3.hatenablog.com/entry/via-support)

## 対応リスト

　とりあえず過去に頒布した実績がありかつ最新のリビジョンのものは対応してみました。キーマップは最近自分が使っているdefault(US配列)ベースにちょっとカスタムしているものになります。  
　とりあえずなのでファイルは期間限定です。リンク先が無くなってたらそういうことです。  

### Rhymestone

最近コードを書き直したりPRしたり写真撮ったりと、ちょっと見直してきているのもあって対応しました。再販は考えてませんが、海外を探すと売ってたりします（敢えてURLは載せませんので探してみてください）。  

[Rhymestone hex and json](https://drive.google.com/file/d/1mWxFfMBXfMIeIO63UFZAvbzZFwOLTGM5/view?usp=sharing)  

![img](/assets/photos/via_rs_01.jpg)  

### Treadstone48

<strike>とりあえずrev2だけ。rev1とrev2を一つにまとめられそうになさそうなので、rev1が欲しい場合はtwitterにリプください。  
[Treadstone48 hex and json](https://drive.google.com/file/d/1yepdyliOKARiSLDqLJn1XScemyemXTvA/view?usp=sharing)  

rev1の対応をしました。スプリットバックスペースも対応(rhymestone連携機能の実装は大変なのでオミットしました)  
rev1と2はhexファイルが別なので注意してください。jsonファイルは共通です。
</strike>

**2020/07/28修正**  
rev1に関してはやはりちゃんとjson側も対応が必要なようで作成しなおしました。  
rev1所有者がもし以前のバージョンをDLしているひとはDLしなおし、rev1のファームを書き直し、rev1用jsonを読み込んで使用してください。  
zip内のテキストもよくお読みください。  

[Treadstone48 rev1/2 hex and json](https://drive.google.com/file/d/1SjPfF1Zn62hrh-4dlqDhBi2JojINiZj5/view?usp=sharing)  

![img](/assets/photos/via_ts48r2_01.jpg)  

### Treadstone32 / 32lite

32と32liteはhexファイルが別なので注意してください。jsonファイルは共通です。  

[Treadstone32 hex and json](https://drive.google.com/file/d/18WXRdKAcKxy4h4aVAJUJYI28Z0Z8kbxn/view?usp=sharing)  

![img](/assets/photos/via_ts32_01.jpg)  

### Treadstone60β

次版のγにも対応しています。  
α版は選ばれしもののみへの頒布だったので対応見送ろうかと思っていますが、欲しい場合はtwitterにリプください。  

[Treadstone60β hex and json](https://drive.google.com/file/d/1JeW5edUm1cAlcQXy9B9frmpbzyPHl0y5/view?usp=sharing)  

![img](/assets/photos/via_ts60_01.jpg)  

## 蛇足

　以下蛇足…  

　ここまでの簡単になってしまうと、設計者とユーザーの溝が深くなってしまうなぁなどと思ったりしています。  
　これまでユーザーに開発環境を整えコードを眺めて自分の思うようなキーマップに修正するというソフトウェア側の試練と、ハンダ付けと組み立てというハードウェア側の試練を乗り越える必要があってある意味フィルタリングがかかっていたんですが、ソフトウェア側の試練がほぼ無くなってしまうのでちょっとその辺がどうなるか。これはまぁPCBAしたキットにも言えることかもしれませんが…杞憂に終わると良いですが…。  
