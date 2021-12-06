---
layout: post
title: Drevo Calibur V2 TEのSONiX QMK化
date: 2021-12-07 11:45:00 +0900
tag: [qmk, keyboard]
image: 20210919-IMGP7370.jpg
---

## 今年ももう終わりなのか…

[「キーボード #1 Advent Calendar 2021」](https://adventar.org/calendars/6246)の7日目。  
6日目はyfukuさんの[拡張プレートシステムを作った話](https://note.com/yfuku_/n/n7cb9a55d0753)でした。yfukuさんは以前から完成度の高いキーボードキットを製作されていますが、にしても拡張プレートの完成度もすごく高くてとてもいいですね。  

私は沼人の活動を横で観てただけで今年はほとんどなんもやってませんが、市販品のキーボードのファームウェアを改造ファームに入れ替えたお話でも。  

![img](/assets/photos/20210919-IMGP7370.jpg)  

[2020年の記憶をまとめてロンダリング]({% post_url 2021-04-19-matome2020 %})に書いた、格安キーボードを買って打鍵音と打鍵感を改善する改造をするためのネタとして買ったDrevo Calibur V2 TE。  
専用のソフトウェアでマクロ割り当てなどのカスタマイズが出来るものの、普段使っているキーマップが完全に染み付いてしまってるがために使い勝手がいま一歩で…でもせっかくカスタムしたのになーとネットをさまよっていたら、SONiX QMKというプロジェクトが今年の春先に出来てて、このキーボードに使われているMCUも対応してそうで、これなら私でもちょっと調べれば出来るかも？ということでやってみました。  

![img](/assets/photos/20201026-IMGP6197.jpg)  
![img](/assets/photos/20210829-IMGP7309.jpg)  

### MCU再確認、データシートの確保、VID/PID確認

まずは基板とMCUを確認してデータシートをDL。データシートはピンアサインの確認や、トラブった時に何かと見ることになるので…ちなみにDrevo Calibur V2 TEはHFD2201KBAが使われているけど、これはSONiX SN32F248B互換ということなのでそのデータシートをDL。キーボードを接続してVID/PIDの確認しました。  

### オリジナルファームウェアの確保（念のため）

オリジナルファームウェアをDL。メーカーにバージョンアップ用のものをDL出来るものがなく…MCUのフラッシュ領域からファームウェアを吸いだせる可能性もありますが、そっちに取り掛かる前にもう少しネットで足掻いてみると同じ71keyレイアウト、ケース形状の同じ基板だと思わしきキーボードを2機種（Monstergear Ninja71、ROYAL KLUDGE RK71）見つけました。試しにこの2機種の専用ソフトを立ち上げたらDrevo Calibur V2 TEがそれぞれのキーボードとして認識されました。OEM品ぽいのを確信してMonstergear Ninja71の最新ファームウェアを入れたら正常動作したので、これをバックアップとして確保することにしました。  

### ピンアサインとスイッチマトリクスの解析

![img](/assets/photos/drevo_calibur_v2_layout1.png)  
![img](/assets/photos/drevo_calibur_v2_matrix1.png)  

スイッチとMCUのピンのつながりを一つ一つテスターの導通モードで確認してスイッチマトリクスを解析。Drevo Calibur V2 TEは自作キーボードでもおなじみの普通のマトリクスだったので、[https://kbfirmware.com/](https://kbfirmware.com/) でcolumnとrowとスイッチの関係を設定していけて便利でした。  
SN32F248BのデータシートのGPIO名がP3.11という表記になっていて、PICとかAVRとかARMにみるようなPB1みたいな表記ではないですが、  

- P3.11→P#.$$と分解して
- #の0～をA～に変換
- .$$は数字だけにして繋ぎ治す

と3.11→D11となり、QMKのconfig.hに登録可能なピン設定になります。  
ここまででひとまずキーボードとして動作させるのに必要な情報がそろうのでファームウェア作成に入ります。  

### ファーム作成

[https://github.com/SonixQMK/Mechanical-Keyboard-Database](https://github.com/SonixQMK/Mechanical-Keyboard-Database) からSONiX QMKで動作しているキーボードで、SONiX QMKのkeyboardsフォルダに存在するキーボード名のフォルダをコピーし、それをベースにして書いていきました。  
基本はQMKのものと同じだけど一部SONiX用と思わしきヘッダファイルやコードが存在するので、そのあたりも注意を向けてそっちのコードも読みつつ、調査した設定値に書き換えて自分の好きにキーマップを作成し、ファームを完成させます。  

### ファーム書き込み方法の確認、書き込み

![img](/assets/photos/drevo_calibur_v2_firm_writer.png)  

さて書き込みです。先達が解析ログとともに書き込みツールを記録していました。windowsのGUIで使えそうな [https://github.com/IslamAlam/blitzwolf-bw-kb-1](https://github.com/IslamAlam/blitzwolf-bw-kb-1)で見つけたSONiX_USB_MCU_ISP_Tool_V2.3.1.7-.zipを使いました。  
MCUのモデルを選択（SN32F24xB）して、作成したファーム（binでもhexでもどちらでも）を選択。  
MCUのBOOTピンをGNDに落としながらUSBを接続するとブートローダーモードになるので、ブートローダーモード用のVID/PIDを設定して書き込みします。果たしてうまく書き込んでくれるか？失敗すると面倒くさいことになるor文鎮になるのでここが一番怖いんですが…💦  

…無事QMKが動作するキーボードになりました🎉  

キーマップはTS60をベースにしてTAPPING_TERMの速度調整をしたら使い心地的には十分に納得行くものになったので1週間ほど使って、問題無かったので次はバックライトなどのRGBLEDの対応をやってみることに。  

### RGBLEDのピンアサインとマトリクス解析、ファーム修正

上でRGBバックライト周りのコードをコメントアウトするときに、ファームのdrivers/sn32/rgb_matrix_sn32f248b.cにトランジスタとの接続について書かれてあるのを見つけてこれが役に立ちました。バックライトに使われているRGBLEDはR,G,B,+（アノードコモン）の4ピンで、+ピンはCOL、RGB各ピンはROWとするマトリクス接続になっています。RGBLEDのピンからトランジスタを介してMCUに繋がっているのでちょっとややこしいですが、トランジスタとMCUとLEDのピンのつながりを一個一個チェックしてつながりを把握します。把握したらソースコードに設定を書いて終わり。やったね！無事光りました。  

### 感想

![img](/assets/photos/20210901-IMGP7318.jpg)  

ぶっちゃけてしまうと最初からQMK対応のキーボードキットを組んだ方がスマート（元も子もない💦）  
それかせきごんさん作のKeyboard QuantizerみたいなQMKコンバーターを間にかましたほうが遥かにお手軽です。  

4000円台（購入当時）の市販キーボードが無料でQMKに対応出来る！レイヤー設定自由自在！お得！みたいな捉え方も出来るんだけど、手間やリスクを考えるとお得ではないかなぁ…と。  
それに去年から65～75%の手頃なお値段のキーボードキットが続々とGroupBuyされてたので、そっちを買った方が満足感は断然高いかなぁ…と。  
私はダメ元でやり始めて結果オーライだったのでネタとしてこうして書いてますが、ダメになってたらスイッチ外してポイしてましたね…😅  

加えて現時点でSONiX QMKのEEPROMエミュレータが動作していないっぽいので、VIA/Remapに対応してもあまり意味をなさないことと（電源OFFで設定忘れる）、RGBLEDの点灯状態を記憶出来ないことが残念ポイント。  

![img](/assets/photos/20210919-IMGP7360.jpg)  

までも、ぱっと見は何も手を加えられてないように見える普通の市販キーボード、だけど触るとわかるノイズの少ない打鍵感と打鍵音、自分の手に馴染むバネレートとキーマップ。という外見と中身のギャップ萌えが際立つキーボードが完成してかなり満足しています😊

### まとめ

- 本体の調査資料 [https://github.com/marksard/re-drevo-calibur-v2-te](https://github.com/marksard/re-drevo-calibur-v2-te)

- 改造ファームウェア [https://github.com/marksard/qmk_firmware/tree/sn32_add_keeb_drevo_caliburv2te](https://github.com/marksard/qmk_firmware/tree/sn32_add_keeb_drevo_caliburv2te)

### ほか資料など

- キーボードの製品ページ [https://www.drevo.net/product/keyboard/calibur-v2-te](https://www.drevo.net/product/keyboard/calibur-v2-te)

この記事は  
Drevo Calibur V2 TE MOD
（Durock L2(stock)、Novelkeys PBT Doubleshot WoB + Zen Pond III、ボトムケースのウェイトの敷き詰め、フォームの入れ換え、SONiX QMK化）を使って書きました。
