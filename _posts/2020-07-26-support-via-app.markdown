---
layout: post
title: VIA対応やってみました
date: 2020-07-26 10:00:00 +0900
tag: [hardware, keyboard]
image: via_rs_01.jpg
---

## おしらせ

2020/07/28 TS32, TS60の一部キーが認識しないjsonファイルの問題を修正しました。また、TS48はrev1に対応しました。  
不具合があればtwitterにておしらせくださいませ。  

2020/09/10  

- TS60のキーマップのレイヤー層を増やしました（VIAは最低4レイヤーを求めているため）
- Rhymestoneのキーマップを最新にアップデートしました
- Leftover30のVIAを追加しました

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
**2020/09/10修正**  
中身は変わっていませんがQMK本体にマージしたdefaultキーマップを適用してhexファイルを作り直しました。  

[Rhymestone hex and json](https://drive.google.com/file/d/1mWxFfMBXfMIeIO63UFZAvbzZFwOLTGM5/view?usp=sharing)  

![img](/assets/photos/via_rs_01.jpg)  

### Treadstone48

<strike>とりあえずrev2だけ。rev1とrev2を一つにまとめられそうになさそうなので、rev1が欲しい場合はtwitterにリプください。  
[Treadstone48 hex and json](https://drive.google.com/file/d/1yepdyliOKARiSLDqLJn1XScemyemXTvA/view?usp=sharing)  
</strike>

**2020/07/28修正**  
rev1に関してはやはりちゃんとjson側も対応が必要なようで作成しなおしました。  
rev1所有者がもし以前のバージョンをDLしているひとはDLしなおし、rev1のファームを書き直し、rev1用jsonを読み込んで使用してください。  
zip内のテキストもよくお読みください。  

[Treadstone48 rev1/2 hex and json](https://drive.google.com/file/d/1SjPfF1Zn62hrh-4dlqDhBi2JojINiZj5/view?usp=sharing)  

![img](/assets/photos/via_ts48r2_01.jpg)  

### Treadstone32 / 32lite

32と32liteはhexファイルが別なので注意してください。jsonファイルは共通です。  
**2020/07/28修正**  
一部キーが効かないため、jsonファイルを修正しました。  

[Treadstone32 hex and json](https://drive.google.com/file/d/18WXRdKAcKxy4h4aVAJUJYI28Z0Z8kbxn/view?usp=sharing)  

![img](/assets/photos/via_ts32_01.jpg)  

### Treadstone60β/γ

α版はレイアウトは違うものの動作はするかと思います。
**2020/07/28修正**  
一部のキーが効かなかったため、jsonファイルを修正しました。  
**2020/09/10修正**  
空白のレイヤ層を増やして表示がおかしいの修正しました。  

[Treadstone60β hex and json](https://drive.google.com/file/d/1JeW5edUm1cAlcQXy9B9frmpbzyPHl0y5/view?usp=sharing)  

![img](/assets/photos/via_ts60_01.jpg)  

### Leftover30

QMKにマージしたdefaultを適用しています。  
QMK Configuratorでは現状ではロータリーエンコーダーが効かなくなってしまうのですが、VIAの方はデフォルトのプログラムで動作します（動作のカスタマイズは出来ません）。  

[Leftover30 hex and json](https://drive.google.com/file/d/1V-Eqp08cPe4KrC_mvXAuQMPM8Bmn9xCL/view?usp=sharing)  

![img](/assets/photos/via_leftover30_01.jpg)  

## 蛇足

　以下蛇足…  
<strike>
　ここまでの簡単になってしまうと、設計者とユーザーの溝が深くなってしまうなぁなどと思ったりしています。  
　これまでユーザーに開発環境を整えコードを眺めて自分の思うようなキーマップに修正するというソフトウェア側の試練と、ハンダ付けと組み立てというハードウェア側の試練を乗り越える必要があってある意味フィルタリングがかかっていたんですが、ソフトウェア側の試練がほぼ無くなってしまうのでちょっとその辺がどうなるか。これはまぁPCBAしたキットにも言えることかもしれませんが…杞憂に終わると良いですが…。  
</strike>
　どうも私の認識がかなり古臭くなっているようで、基本的にQMKはmakeして利用してほしいなと考えてたうえでの発言だったのですが、最近はQMK Configuratorでの挙動を前提としたキーボード作りを求められている雰囲気があります。  
　ただ、私が作成している30%キーボードではレイヤー移動用の長押し判定時間と、通常の長押し判定時間に差を作って(90WPM程度で)誤判定がないようにしているのですが、これがQMK Configuratorでは動作させることが出来ずにいます（これが動かないとTS32なんかはちょっと厳しい）。  
　VIAの場合は登録したVIA用キーマップのファームウェアの、キーマップの領域だけをカスタム出来る作りのため、前述の長押し判定の独自コードがそのまま使えたり、ロータリーエンコーダーもカスタム済みのものが動作するようになっていて、その点ではVIAを使うほうがメリットがあります。  
　ただ、まぁどちらのキーマップ編集ツールもQMKの機能に全部はアクセス出来ないため、まだもう少し様子見ではあるのかなというのが正直な感想です。  
