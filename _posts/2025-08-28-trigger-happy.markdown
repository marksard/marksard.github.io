---
layout: post
title: Trigger Happyモジュールを作った
date: 2025-08-28 17:00:00 +0900
tag: [hardware, eurorack, synth]
image: 20250412_20250412-IMGP9018.jpg
---

![img](/assets/photos/20250624-IMGP9085.jpg)  

ゲート・トリガーとピッチCVをバシバシ出すモジュールが欲しいなと思って作りました。

これまで作っていた

- パターンベースランダム16ステップシーケンサー
- 16ステップユークリッドトリガー
- クロックディバイダー
- 8bitシフトレジスター

の各アプリをまとめてコンパクトサイズに押し込めてあげればケースの空きスペースが増えるのでは？みたいな魂胆もありまして。

---

### ハードウェア

- 仕様
  - 6HP
  - ±12V
  - Waveshare RP2040 zero または互換ボード
- 入出力
  - GATE出力：6
  - CV出力：1（0～5V V/OCT対応）
  - GATE入力：1
  - RESET入力：1
  - DATA入力：1
- 操作系
  - ロータリーエンコーダー：1
  - ボタン：4（うち1つはロータリーエンコーダー内臓）
- 表示系
  - フルカラーLED（ロータリーエンコーダー内臓）
  - GATE出力確認LED：6
  - CV出力確認LED：1

詳しくは → [マニュアル](https://github.com/marksard/TriggerHappy/blob/main/app/TriggerHappy/manual/TriggerHappy%20Operation%20Manual.md)

![img](/assets/photos/20250726-IMGP9102.jpg)  
![img](/assets/photos/20250726-IMGP9107.jpg)  

---

### 回路の概要

![img](https://private-user-images.githubusercontent.com/38324387/471035382-f9e13bb8-b088-4d35-bd2d-046cb3acb846.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTYzNjU1MjYsIm5iZiI6MTc1NjM2NTIyNiwicGF0aCI6Ii8zODMyNDM4Ny80NzEwMzUzODItZjllMTNiYjgtYjA4OC00ZDM1LWJkMmQtMDQ2Y2IzYWNiODQ2LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA4MjglMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwODI4VDA3MTM0NlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWRmZjA4ZjJhYTJjZTIxNjA1ZDY2ZmZhZjllMWY4ZjE4M2U0ZWI5MWQ5YjcyZTU4N2Q4YjljMGMzMmU5MzcyYjkmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.ha2UHoQlKLSjJLJfbzA-8PMd3dwk8-UUR7KRk-c5wAE)  

トリガー出力回路はレベルシフトとバッファアンプを兼ねるロジックICで、5V電源をモジュール内で生成して出力電圧としています。  
初回試作は12VもイケるMC14504を使ってたんですが、電圧そんな要らんかと考え直して電流を十分出せるSN74AHCT244にしました。  
5V生成は初回試作ではリニアレギュレータを使ってみたんですがほんのり温かく、12V→5Vの熱損失が勿体ないなとなってDCDCのTPS62163DSGRにしました。  
TiだけでもDCDCのICが無数にありすぎてヨクワカランのでAIにチョイスしてもらいました。  

エンコーダー＋スイッチ＋3色フルカラーLEDは秋月電子で販売されてるものです。マイコン非内臓（アノードコモン）なので、点灯・消灯・点滅、色変化はオシレーターのソースコードを流用して三角波と矩形波でのPWM制御で行っています。  

CLOCK/RESET/DATAの入力は全部同じ回路にしていて、RP2040 zeroの3V3リファレンス電圧の1%誤差範囲を考えて5Vを3.27V程度まで分圧で落としてADCピンへ入力しています。高精度抵抗で分圧することで入力電圧上限の誤差を抑えて、RP2040のエラッタ補正とリファレンス電圧からのスケール補正でV/OCTを0-5V範囲をカバーします。  
（※上記のアプリにV/OCT入力がないので使ってはないんですが機能としてはそんな感じで用意しています）  

本来ならRP2040から直に実装してリファレンス電圧を正確に調整出来るようにするか、リファレンス電圧入力ピンのあるPiPicoなんかを使うのが正しいんでしょうけど…

---

### ソフトウェア

冒頭の通りこれまで作ってたアプリをギュッとまとめたものにしました。  
操作体系を統一しつつ少ない操作子で設定項目を多数盛り込んだせいでリアルタイムな操作性は正直良くはありません💦  

自分でイチから作らずともケーブル繋げばそれっぽいリズムやシーケンスを何かしら刻むので、ジャック差し替えたりORやANDしてみたりCLOCK入力をランダムにしたりして探り探りで音の変化を楽しむ的な、そんな感じです。

- 3つのアルゴリズム（クロックディバイダー、シフトレジスター、ステップシーケンサー＆ユークリッドトリガー）から選択
- 各アルゴリズムのゲート出力には複数のパターンがある
  - クロックディバイダー：各出力と分割数の割り振り  
    - /2,/3,/4,/5,/6,/7  
    - /2,/4,/8,/16,/32,/64  
    - /3,/4,/5,/6,/7,/8  
    - …etc
  - シフトレジスター：各出力とビット位置の割り振り  
    - 1,2,3,4,5,6  
    - 3,4,5,6,7,8  
    - …etc
  - ユークリッドトリガー：
    - 独立した3基のユークリッドトリガー：（オンセット、ステップサイズそれぞれ1-16まで独立して設定可）
    - ランダムステップシーケンサーのゲート出力（ゲート長さ設定可）
    - ランダムステップシーケンサーのACC出力（ゲート長さ200ms固定）
- 各アルゴリズムのゲート出力はデュレーション調整可能（2, 8, 16, 32, 64, 80, 100%）
- 内臓クロック付き。使用時すべてのOUT6はクロック出力に変わる
- 操作子からのフィードバックはフルカラーLED、各出力のLEDで確認
- ピッチCV出力は以下（キーはナチュラルマイナー・メジャーに対応）
  - クロックディバイダー：ステップシーケンサーのピッチCV
  - シフトレジスター：8bit値合計をクオンタイズしたピッチCV or ランダムトリガーモード時はランダムピッチCV
  - ユークリッドトリガー：ステップシーケンサーのピッチCV
- DATA入力
  - クロックディバイダー：ゲート出力パターンテーブル切り替え（0-5V）
  - シフトレジスター：データ登録
  - ユークリッドトリガー：ステップサイズとオンセットのランダム生成
- RESET入力
  - クロックディバイダー：カウンターリセット
  - シフトレジスター：レジスターリセット
  - ユークリッドトリガー：ステップシーケンサーのランダム生成

詳しくは → [マニュアル](https://github.com/marksard/TriggerHappy/blob/main/app/TriggerHappy/manual/TriggerHappy%20Operation%20Manual.md)

---

### 感想

元々適当に感覚で扱ってみたいという思いがあったので想定内ではあるんですけど、個人的にパラメータの視認性もちょっと悪いかなという気もしますが探り探りでも良いか的な気もする微妙な所…  

基本的な操作方法を

- （エンコーダーのみ操作）  
- （A or B or MODEボタン押しながらエンコーダー操作）

前者は無操作時のLEDの変化で後者はLEDの色変化で設定値を視認。  
どの階層にいるかが3色LEDじゃ分かりにくいから階層化するのを出来るだけさけたんだけど、んー、なしよりのありよりくらい。

ただこのサイズでトリガーをバシバシ出せるようになったし、だらだら機能追加してたアプリに一区切り付けられたのは良かったかな。  

現在手入力で16stepトリガーx6chとピッチCVも手入力するアプリを作って遊んでるし、入力をOR/AND/XOR/NOT/NOR/NANDするみたいなアプリなんかも作ることが出来そう。

現在はこの設計をベースにしてレベルシフトの代わりにDACを載せたモジュールを開発中しているので、こちらの話はまた近々アップ出来れば良いかなと…

![img](/assets/photos/20250726-IMGP9090.jpg)  

（6月ごろの状態…）
![img](/assets/photos/20250617_IMG_9232.jpg)  

---

### デモ

一通りサラッと動かしているデモです。  
左のオシレータをクロックソースにデモ対象のTriggerHappyを動かし、試作中の4HP 2chDAC出力モジュールに入れた2chVCOアプリを鳴らしています。  
2chの片方は矩形波でメロディを、もう片方は正弦波の周波数をエンベロープで動かしてバスドラに見立てて鳴らしています。  

<iframe width="560" height="315" src="https://www.youtube.com/embed/n2r3Bfe7ZCw?si=A5hqF6yJkyvGtSu-" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


こちらは試作品のものですけど、シフトレジスタに組み込んだ8stepのランダムトリガー＆CVのデモな感じです。  

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">ランダムトリガー&amp;CV機能をシフトレジスター機能のなかに入れてみた<br>フレットレスぽいフレーズ延々やってくれる<br><br>8ステップぶんのメモリーループ機能をつけたので色々応用は効くと思う<a href="https://twitter.com/hashtag/%E8%87%AA%E4%BD%9C%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%A9%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%BB?src=hash&amp;ref_src=twsrc%5Etfw">#自作モジュラーシンセ</a> <a href="https://t.co/82St7Rzf3B">pic.twitter.com/82St7Rzf3B</a></p>&mdash; 𓊬 ᙢᗩᖇḰ 𓊬 (@marksard) <a href="https://twitter.com/marksard/status/1928075053557551238?ref_src=twsrc%5Etfw">May 29, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
