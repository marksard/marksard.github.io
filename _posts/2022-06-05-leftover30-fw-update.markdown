---
layout: post
title: LEFTOVER30のファームウェアアップデート
date: 2022-06-05 13:00:00 +0900
tag: [hardware, keyboard]
image: 2022-06-05_12-56-52.jpg
---


## アップデート内容＆手順

ロータリーエンコーダーの回転時のキー割り当てをremapから変更出来るようにしました。ファームウェアをアップデートし、remapでレイアウトを表示すると使えるようになります。  

図でいうと右下のAudio Vol-側が左回転時、Audio Vol+が右回転時に動作するキーです（割り当ては自由）  

![img](/assets/photos/leftover30_fw_up_01.jpg)  

このボタンはレイアウトオプションで「RotatyEncoder Rotating」をONにすると出てきます。  
![img](/assets/photos/leftover30_fw_up_02.jpg)  

ファームウェアをremapの以下よりDLしてflashするか、remapから直接flashすることで使えるようになります。  
https://remap-keys.app/catalog/Ouljvg5MI79CCVJt0Pqg/firmware
![img](/assets/photos/leftover30_fw_up_03.jpg)  

## 以下なりゆき

最近LEFOVER30使ってないなーと思ってキーキャップ変えたりして遊んでたんですが、私がロータリーエンコーダーあんまり使ってないのって、もしやキー割り当てするのにコード書いて焼くの面倒だからでは？と思いいたって対応してみることにしたんです（いや今まで対応せず申し訳ない）  

モンクさんのattack25のソースを参考にしました（まったくもってモンクさんに感謝）  
[https://github.com/monksoffunk/qmk_firmware/tree/feature/attack25_rev1rev3_numa16/keyboards/25keys/attack25/rev32](https://github.com/monksoffunk/qmk_firmware/tree/feature/attack25_rev1rev3_numa16/keyboards/25keys/attack25/rev32)  

概略だけ書くと、via/remapでキーを変更可能にするためにkeyboard.hに左右回転時に押したことにするキーの割り当て追加して、（r01,r02がそれ）  
![img](/assets/photos/leftover30_fw_up_05.jpg)  

keymap.cにはロータリーエンコーダーの回転動作時に押したことにするコードを書いて、（row7,col0 or 1を押した体にする）  
![img](/assets/photos/leftover30_fw_up_04.jpg)  

キーマップの定義部分には割り当て分のキーを追加設定する。（KC_WH_U, KC_WH_Dがそれ）
![img](/assets/photos/leftover30_fw_up_06.jpg)  
こうすることで、  

ロータリーエンコーダー回転→割り当てられたrow,columnを押したことにする→キーマップ定義の位置が押されたのでPCへ出力  

というフローになります。なるほどね。  

私のソースはこちら  
[https://github.com/marksard/qmk_firmware/tree/own_build/keyboards/marksard/leftover30](https://github.com/marksard/qmk_firmware/tree/own_build/keyboards/marksard/leftover30)  


使い心地はこれからです😃

たねやつさんのアルチザンレジンノブもつけました😍
![img](/assets/photos/2022-06-05_12-56-52.jpg)  
