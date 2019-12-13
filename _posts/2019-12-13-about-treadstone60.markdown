---
layout: post
title: 60%トレイマウントケースでTS配列 - Treadstone60 -
date: 2019-12-13 12:00:00 +0900
tag: [hardware, keyboard]
image: 20191026-IMGP4795.jpg
---

　この記事は[キーボード #2 Advent Calendar 2019](https://adventar.org/calendars/4332)の13日目の記事です。  
　私の記事は去年と同じく作ってみた記事です。60%トレイマウントケースにハマる独自配列（TS配列）のキーボードというニッチすぎるネタですが、御笑覧いただけると幸いです。  

## 思い立ち

　今年の春に届いた[KUMO]({% post_url 2019-05-20-kumo-keyboard %})に[MKUltra](https://mkultra.click/collections/40-foam)のMiniVan専用フォーム入れてTreadstone48/32と平行して使っているのですが、フォームを入れると内部の反響音が少なくなってソリッドな打鍵音になりトレイマウントも良いなってなって、Treadstoneな配列で入らないかな？と思ったのがきっかけ。で試行したのが↓  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">KUMOのケースサイズに入るTreadstone40 <a href="https://t.co/v5DDmFskTV">pic.twitter.com/v5DDmFskTV</a></p>&mdash; ᙢᗩᖇḰ (@marksard) <a href="https://twitter.com/marksard/status/1157109928089157632?ref_src=twsrc%5Etfw">August 2, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　ただKUMOのケースを使っちゃうと気に入って使っているKUMO自体が使えなくなっちゃうのと、汎用の40%ケースってそんなにないので買うなら60%かな…と思って試行したのが↓  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">60%トレイマウントに合うカタチにしたTreadstone60… <a href="https://t.co/G42c0ow0ZM">pic.twitter.com/G42c0ow0ZM</a></p>&mdash; ᙢᗩᖇḰ (@marksard) <a href="https://twitter.com/marksard/status/1157100646203396101?ref_src=twsrc%5Etfw">August 2, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　15U x 5行の大きさに合わせて作ってみたくらいの感じです。  
　Treadstoneの60%版はこれ以前にもなんとなく考えては数字行やっぱり要らないよなぁとかコストアップするなぁとなってお蔵入りしてたんですが、汎用の60%トレイマウントを利用できるとケースバリエーションで遊べるよなぁちょっと興奮してきたなということで、PCBを試作してみることにしました。  

## 合わせこみ

　海外のキーボードショップでは60%のトレイマウントケースが販売されていて、[サイズとネジ脚の位置はVortex Pok3rを起源とするもの](https://deskthority.net/wiki/Custom_Keyboard_Construction)がデファクトスタンダードのようです。サイズは15U x 5行で、ケースから脚がニョキっと出ているところにPCBをビス止めします。（マウントプレートはスイッチを保持して浮いている状態になります）このケースから脚が生えている部分にスイッチなどの部品が干渉せずビス留め出来ることがまず大前提です。  

　次に使用するキーキャップも頭に入れて配置を検討していきます。フラットなDSAプロファイルなどはブランクキーキャップも入手しやすいのでレイアウトの自由度は増すんですが、GMKプロファイルやMT3プロファイル、SAプロファイルのような行によって高さの違うステップスカルプチャ系のキーキャップを使うことを考えると、少なくともGMK Baseに付属しているキーキャップ（BaseにはFull,WKL,TKL,1800系,メジャーな60%に対応するキーキャップが含まれています）を使うことを想定するほうが組みやすいです。そんな感じでたどり着いたのが以下↓  

![img](/assets/photos/treadstone60.png)  

　BackSpace/Deleteの位置は40%セットの1.75Uのものも使えるように選択（ソケットの関係で排他です）で使えるようにしました。CapsLock位置はsteppedキーキャップに対応するように1.25U/1.5Uを用意。  
　最下段は当初tsanganレイアウト（1.5/1/1.5/7/1.5/1/1.5）にしたかったんですが、スプリットスペースバーを考慮すると最下段のネジ脚と干渉しそうだったので通常の6.25Uを使うタイプにしました。スプリットスペースバーは一般的な2.25U/1.25U/2.75Uの組み合わせ。次に作るなら1.5/1/1.25/6.25/1.25/1.25/1.5でHHKB/WKLぽいレイアウトにも出来るようにしたいトコロです。  

## PCBレイアウト

　上記のレイアウトを元にPCBを作りました。    

![img](/assets/photos/treadstone60pcb.png)  

　[ai03氏のvoyager60](https://github.com/ai03-2725/Voyager60)のPCBのアウトラインをベースにビス穴を気持ち小さくしたり補強のためのスルーホールパッドを小さくしたりしとソケット対応しています。  
　ただし一か所、MXソケットだとHキーと中央のビス脚がケースによって干渉しそうです。とりあえず自分用だし加工でなんとかなるかと思ってここはこのままとしました。売り物にするならこのキーだけでも逆向き（north facing）にする必要があるでしょうね…DZ60HotSwapもここは加工が必要だというの話を聞いたのですが…  

　MCUとその周辺はtreadstone32の部品を流用。PCBAするならもっと小さい方が良いらしいので、次回作るならPCBAするために小さくするかな…  

## ここまでやってやっぱり気になったので…

　ここまでやっといてやっぱりレイアウトの右上周りが気になったので、キーキャップとケース込みの3Dでグリグリやってみたいとなりました。Blender入れて使い方を少し参照しつつ作ったのが↓

<blockquote class="twitter-tweet"><p lang="und" dir="ltr"><a href="https://t.co/8NOApKDsPv">pic.twitter.com/8NOApKDsPv</a></p>&mdash; ᙢᗩᖇḰ (@marksard) <a href="https://twitter.com/marksard/status/1184100957442805760?ref_src=twsrc%5Etfw">October 15, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　Kicadでプレートをdxfでエクスポートし、Blenderにインポートしてそれを覆うようなケース作って[SAキーキャップ素材](https://www.thingiverse.com/thing:2178185)を乗っけただけです。  
　観るに絶えないものでもないかと思い切って一式注文しました。  

## パーツ到着

![img](/assets/photos/APC_1392.jpg)  

　ケースはKBDFansの5°のフロストケース。候補だったブラックとフロストをカートに入れてて、ブラックを選んでたつもりだったのに間違ってフロストを買ってしまってました💦でもフロストが来てよかったかも。中々キレイです。  

![img](/assets/photos/APC_1393.jpg)  

　PCBはai03氏のvoyagerレンダに倣って赤色にしてみました。ベタGNDを必要な箇所だけにしたので配線した箇所とそうでない箇所がはっきりわかるのと、配線していない箇所の濃い赤がキレイです。  

![img](/assets/photos/IMG_9651.jpg)  

　プレートの作成は[ai03氏のPlate Generator](https://kbplate.ai03.me/)を使わせていただきました。吐き出されたsvgをKicadにインポートして、サリチルさんから頂いたスイッチ穴のフットプリントに差し替えたり模様を適当に入れたりしています。  

## 作成・完成

　部品がそろったらKicadのデータを見ながらPCBの導通チェックして、はんだ付けして完成です（雑  

　事前の予想通り、Hキーのソケットの端子とケースの脚が予想通り干渉したのではんだ付け後ソケット端子をカットして対応。  
![img](/assets/photos/APC_1414.jpg)  

　一か所、PCBの業者ミスでリセットスイッチのマスクが出来てなかったので削ってリカバーしました。  
![img](/assets/photos/IMG_9653.jpg)  

配列が独特すぎて、キーキャップと脚位置の関係上どうしても隙間が出来てしまうのは仕方ないのですが、ギリギリ許容範囲内ってところでしょうか。  
![img](/assets/photos/20191026-IMGP4795.jpg)  

PCB自体の幅は15Uでほぼ一緒なのですがTreadstone48と比べるとかなり大きいです。
![img](/assets/photos/IMG_9665.jpg)  
![img](/assets/photos/IMG_9814.jpg)  

この写真はお気に入り  
![img](/assets/photos/20191026-IMGP4900.jpg)  

## キーマップ

![img](/assets/photos/treadstone60.png)  

　レイヤーはBase、LowerとAdjustの3つ。LowerレイヤーはBaseからあふれたキーを割り当ててます。  
　レイヤ切り替えはLowerはスペース長押しで行います。無理なくどちらの指からでも押せるのでかなり便利でスプリットスペースバーにする必要を感じないほど。  
　Adjustはスペースバー右のalt位置で落ち着きました。Adjustレイヤーにマウスキーを入れてて時々使うので、ギリギリ親指で推しやすい位置に置きました。  

## 使い心地・まとめ

　自分のキーボード遍歴は100%→TKL→60%→40%→30%という感じでキー数が減っていってて、もう数字キーもモデファイア専用キーも要らなかったんや！ってなってたんですが、60%を使い始めると数字キーもモデファイアもあると良いね、60%いいじゃんみたいなお気持ちです。  
　個人的にはGMK買っても放置になってたロングスペースバー、SteppedなCapsLock、文字キー上の数字キーたちを使ってあげられるのが嬉しいトコロ。30%だとBaseキット買っても1/4くらいしか使わなくてめっちゃ勿体ない気がしてたので…  

　打鍵感はケースがしっかりしているためしっかりしています（語彙）。HolyPandaをガンガン底打ちしているとちょっと疲れます…PCB厚み1.2mmを試してみたい気もするし、G,Hキー周辺にスリット開けてあげたいなという気もします。  

　音はケースの中で反響するので、PCBとケースの間、PCBとマウントプレートの間にゴムシートを挟んだりして空間を埋めて反響を抑えたり、重量を増すため（共振防止もかねて）鉛シートをPCBやケースに貼って調整しています。この効果は中々高いです。  
　あとはスタビの下に絆創膏貼ってボトムアウト時の音を抑え、スタビ自身にたっぷりグリス仕込んで出来るだけスタビの存在を消すような対策を施して、キースイッチとキーキャップの音がなるくらいのイメージになるように試みています。 

　HolyPandaとGMKキーキャップの組み合わせでコチコチコチコチとした少し大きめの打鍵音をさせつつも、クリッキーのようなノイジーな音が無いのが気に入りました。  

　好きなキーキャップのプロファイルがCherry Profile、ANSI系のEnterキーが好き、でも好きなキーマップはJIS系ロジカルペアリング、というなんともひねくれた状態の人になっているので、GMKのレジェンドとBASEキーマップのレジェンドが合わなくなるのがすこーしだけ残念といえば残念…そもそもキーマップと乖離するから関係ないじゃんって言われるとそこまでなんですが…  

　レイアウトなどまだ少し調整したい箇所があったりするんですが、今回の試作品でほぼほぼ満足いっている自分も居るので、さて今後どうするかなぁトコロも含めて今後の課題としたいと思います。  

※この記事はTreadstone60 + Holy Panda Switches(Krytox205 lubed) + EPBT 2048 Extended Keycap Setで書きました。  
