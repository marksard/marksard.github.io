---
layout: post
title: Treadstone60Δ
date: 2021-04-26 23:00:00 +0900
tag: [hardware, keyboard]
image: 20210423-IMG_2085.jpg
---

　自分のためのコンセプトモデルとしてちびちび作って遊んでいるTreadstone60ですが、ついに4作目。  

![img](/assets/photos/20210304-IMG_1916.jpg)  

## かえたとこ

### RGBLEDバックライト対応

　YS-SK6812MINI-Eを使って脚付きのフルカラーLEDめっちゃつけやすいって感じたのと、Drevo Caribur V2を改造したりcrkbdの36キー化したときにバックライトLEDがキラキラ光るのもやっぱ良いいなと思ってつけてみることに。  
　配線が増えるので表面実装系のパーツの配置も見直して、配線はイチから全部引き直し。  

### レイアウトオプション変更

　キーごとにYS-SK6812MINI-Eをつけるのでスプリットスペースバー以外のレイアウトのオプションを削除。  

- SteppedなCapslockキー対応 → 削除
- 2.75U Shift(ソケット) ⇔ 1.75U+1U(直付け) 両対応 → 1.75U+1U(ソケット)のみ対応
- 1.25U Control(直付け) → 削除
- アンダーグロー用のLED出力 → 削除

### スリットのあけ方を変更

　これまでのスリット更新履歴(写真は左からα、β、γ、Δ)

![img](/assets/photos/20210423-IMG_2084.jpg)  

- α版：1.6mmPCB、A行Z行間のスリット（ai03氏のVoyager60系）
- β版：1.2mmPCB、α版に加えて、GHキー間のネジ留めホール部周辺にスリットを用意（効果ゼロ）
- γ版：1.6mmPCB
  - A行Z行間のスリット（ai03氏のVoyager60系）→ Z行スペーズバー行間のスリットに変更（wilba.techのWT60-D WEIRDFLEX系）
  - ネジ留めホール部に気持ちスリットを用意（効果ゼロ）
- Δ版：1.6/1.2mmPCB
  - γ版のネジ留めホール部のスリットを延長
  - 横長のスリットは削除

　Δ版はネジ留めホール部のスリットを大きめにとってやわらかくして、打鍵時の指にかかる衝撃の緩和とケースへの衝撃の伝わり方を変異させるのがねらい。  
　VoyagerタイプとかWEIRDFLEX系の横長のスリットはハーフプレートなどで使うほうが効果が高そうなので、ソケットのためプレート必須な私の基板では不要だと思ったので削除しました。  

![img](/assets/photos/20210423-IMG_2085.jpg)  

　Δ版に以下のパーツを付けた感想です。ちなみに私はステムのセンターポールが長いHolyPanda系スイッチ大好きで底打ちして打鍵しています。  

- TOFUアルミケースを使用。ケースのくぼんだ底面に防振用の鉛シートを貼り付けてケースの共振を抑える改造済み
- プレートは1.2mm FR4を使用。PCB～プレート間はフォームで埋めていない
- G,H間だけネジ留めしない。他は締めすぎない程度にネジを締めている
- DiamondStarstoneを使用（詳細は下記動画の概要にも記載）

　PCBの厚み1.2mmは結構柔かめな印象。打鍵時に少しボンボン弾むような感触を感じるものの、底打ちしても衝撃が柔らかい。  
　PCBの厚み1.6mmは丁度いいかもだけど、TABやEnter上のホール、スペースバー横のホールのスリットはもう少し長くしてもいいかなという印象もあったりするけど調整が難しいところ（これ以上長さが取れない）  

　プレートにKBDFansのプレートフォームを貼り付けてΔ版で試打するとまた印象が変わりました。

- 1.2mmPCB：少しかたくなり丁度いいくらいに。打鍵音は全体的によりタイトに。
- 1.6mmPCB：硬さはさほど変わらず…打鍵音は全体的によりタイトに。

　上記のプレート＋β版だと、左右端モデファイアの打鍵するとケースが鳴くので、打鍵時の衝撃の伝わり方がスリットの有無によって変化していそうなのは確認出来ました。  

## タイピング動画

タイピング動画を録りました  

### DiamondStarstone+ブラスプレート版

<iframe src="https://www.youtube.com/embed/KztRPZfWnUs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- ケース改造
  - TOFUアルミケースのくぼんだ底面に防振用の鉛シートを貼り付けてケースの共振を抑える
- プレート
  - ブラス。PCB～プレート間をKBDFansモジュールフォームを貼り付けて埋める
- PCB：Treadstone60Δ 1.6mm
- G,H間のネジ留めはしない。他は締めすぎない程度に締める
- DiamondStarstone（改）
  - Ink Blueハウジング
  - Halo Clearステム
  - ルブ
    - スプリング：Krytox105
    - リーフ：Tribosis3204
    - その他：Krytox205
  - TX Keyboards SwitchFilm
  - スプリング：モデファイア：68S Slow
  - スプリング：アルファ：63.5S Slow

ブラスプレートの影響で全体的に硬質な打鍵感と打鍵音に。  
このあと、1.2mmのPCBに変えるたのですがちょっとやわらかくなって快適に。  

### Everglide Dark Jade+FR4プレート版

<iframe src="https://www.youtube.com/embed/rvvx5L5WS9c" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- ケース改造
  - TOFUアルミケースのくぼんだ底面に防振用の鉛シートを貼り付けてケースの共振を抑える
- プレート
  - 1.2mm FR4。PCB～プレート間をKBDFansモジュールフォームを貼り付けて埋める
- PCB：Treadstone60Δ 1.6mm
- G,H間のネジ留めはしない。他は締めすぎない程度に締める
- Everglide Dark Jade（ストック）

こちらはFR4プレートと無改造DarkJadeの組み合わせ。  
DarkJadeはHolyPanda系で底付きの音が大きいのですがタイトな音になりました。  

### これまでのTreadstone60シリーズをお使いの方へ

PID/UIDを変更しました。

ソースファイルは[QMKリポジトリのown_buildブランチ](https://github.com/marksard/qmk_firmware/tree/own_build/keyboards/marksard/treadstone60)にあります  
VIA用hexファイルは[弊キーボードのVIA hex置き場](https://github.com/marksard/qmk_firmware_hex/releases/tag/release-20210426)にあります  

[Remap](https://remap-keys.app/)ですぐに使いことが出来るようになっていますので是非！  

Alpha版からピン配置は変えていない（マトリクスは同一）ですので、表示されるレイアウトの細かい座標やキーのサイズを気にしなければRemapをそのまま使えると思います。
