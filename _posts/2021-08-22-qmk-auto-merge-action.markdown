---
layout: post
title: qmk_firmwareのfetch&mergeを定期的に行うgithub actions
date: 2021-08-22 14:35:00 +0900
tag: [qmk, keyboard, github]
image: 
---

たまにはQMK最新取ってマージしとくかーと（いらんことを）やったらQMK公式にPRしていない自分のコードがフォーク元のbreaking changeの影響でmakeできなくなっちゃった、みたいなことが時々おこります。  

QMK公式にPRを出してしまえばそのへんのメンテナンスをある程度はしてくれるけど（登録されたキーボードのデフォルトキーマップはファームのバイナリを公開するためmake可能かチェックされ、breaking changeの場合はまとめて更新してくれている場合がある）試作基板向けのコードや、自分用のキーマップなど公式にPRを出していない物は個別に対処する必要がある。  

たまにやるからいっぱいエラーが出る  
↓  
本来したいことを阻害される  
↓  
あーメンドクサ、やめやめ、やめー(´・ω・｀)  
ってなるので、  

- 週イチとかで定期的に
- fetch&mergeして
- make出来るかチェックして
- make成功ならそのままpushしてマージ完了
- 失敗ならpushせずなにかしら通知する

というのを定期的かつ勝手にやらせて、失敗した時のbreaking changeを察知出来るように、成功してれば自分のファームのあるブランチを最新に保ち続けてくれるのが嬉しいなと。  
誰がやってくれるのか。Github Actionsにやらせましょということで調べつつやってみました。  
このテの自動化スクリプトって色んな技術を横断して理解して繋いでく必要があって🔰はなかなかキビシイ世界ですね…。linux(shell, shellスクリプト, コマンド), docker, git, github, slack app, セキュリティ周りと各技術用語につぐ用語…理解しきらずテキトーに書くと動かないし思いもよらない結果になることもしばしば。  

まずこのgithub actionを動作させるだけのリポジトリを用意。フォーク元にaction用ファイルを入れずに済むのでマージやPRへの影響を極力避けられるように。そのぶんSecret系の作成が面倒くさくなりますが。  

アクションではまずQMKが動作するDockerイメージを起動して、その中に対象となるフォークのブランチをクローン。クローン後にfetch&merge。  

```yaml
# github actionsに登録するyamlファイル

name: Build Check after merge with own_build keyboard
on:
  # 手動実行
  workflow_dispatch:
  # 周期実行(UTC。日曜の15時に走らせる＝＝日本時間で月曜0時)
  schedule:
    # Cron format:
    #         ┌───────────── minute (0 - 59)
    #         │ ┌───────────── hour (0 - 23)
    #         │ │ ┌───────────── day of the month (1 - 31)
    #         │ │ │ ┌───────────── month (1 - 12 or JAN-DEC)
    #         │ │ │ │ ┌───────────── day of the week (0 - 6 or SUN-SAT)
    #         │ │ │ │ │
    #         * * * * *
    - cron:  '0 15 * * 0'

jobs:
  # 最新マージ→makeチェック→push処理
  merge_check_push:
    runs-on: ubuntu-latest
    container:
      # QMKのコンテナでビルド
      image: docker://qmkfm/base_container:latest
    env:
      NAME: [!your name]
      EMAIL: [!your email]
      REPO_FORK: https://github.com/qmk/qmk_firmware.git
      # SlackのフックURLをsecretに登録が必要
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK_URL }}

    steps:
    # QMKリポジトリと使用するブランチを選択
    - name: Checkout
      uses: actions/checkout@v2
      with:
        # リポジトリ名。github.com/からうしろのパスだったかな
        repository: [!your github abount]/qmk_firmware
        # マージ対象にするブランチ名
        ref: [!your branch]
        submodules: true
        # Personal Access Token設定とsecret登録が必要
        token: ${{ secrets.ACCESS_TOKEN }}

    # マージ作業
    - name: Merge
      run: |
        git config --global user.name ${NAME}
        git config --global user.email ${EMAIL}
        git config --global pull.rebase merges
        git pull --unshallow  # this option is very important, you would get
                              # complains about unrelated histories without it.
                              # (but actions/checkout@v2 can also be instructed
                              # to fetch all git depth right from the start)
        git remote add upstream ${REPO_FORK}        
        git fetch upstream
        git merge --no-edit upstream/master

    # 指定したブランチに入れてあるmakeスクリプトを起動
    - name: Make completion test
      run: |
        chmod +x ./_release_own_build.sh
        ./_release_own_build.sh -release

    # 指定したブランチに入れてあるファイルサイズチェック用スクリプトを起動
    - name: Hex file size check
      shell: bash
      run: |
        check_result=$(./_release_own_build.sh -hexcheck)
        # envに結果文字列を登録して通知時に使用する（複数行の文字列を格納するハック）
        echo "check_result<<EOF" >> $GITHUB_ENV
        echo "$check_result" >> $GITHUB_ENV
        echo "EOF" >> $GITHUB_ENV

    # makeスクリプトでエラーがでなければpush
    - name: Push
      run: |
        git push origin own_build

    # Slackへの通知
    - name: Slack Notification on Success
      if: success()
      uses: rtCamp/action-slack-notify@v2.0.2
      env:
        SLACK_TITLE: Merge Success
        SLACK_COLOR: good
        SLACK_MESSAGE: "Merge check has been succeeded.\n${{ env.check_result }}"
    - name: Slack Notification on Failure
      uses: rtCamp/action-slack-notify@v2.0.2
      if: failure()
      env:
        SLACK_TITLE: Test Failure
        SLACK_COLOR: danger
        SLACK_MESSAGE: ":fire: Merge check has been failed. :fire:.\n${{ env.check_result }}"
```

マージが終わったらmakeのチェック。ここにmakeを一行一行書いてももちろんいいのですが、私は対象となるフォークのブランチに置いている、自分が使うファームウェアだけダラダラとmakeするスクリプトを起動しています。  
（qmkスクリプトでは未検証。アレ逆に書き方面倒くさいのよね）

```bash
#!/bin/bash -eu
# _release_own_build.sh

# VIA
if [ "$1" = "-all" ] || [ "$1" = "-via" ] || [ "$1" = "-release" ]; then
    make treadstone32:via
    make treadstone32/lite:via
    make treadstone48/rev1:via
    make treadstone48/rev2:via
    make marksard/leftover30:via
    make marksard/rhymestone:via
    make marksard/treadstone60:via
fi
# デフォルト
if [ "$1" = "-all" ] || [ "$1" = "-default" ] || [ "$1" = "-release" ]; then
    #make marksard/gogofunk:default
    make marksard/leftover30:default
    make marksard/rhymestone:default
    #make marksard/stoneaxe:default
    #make marksard/stonehenge30:like_jis
    make marksard/treadstone38:default
    make marksard/treadstone60:default
    make treadstone32:default
    make treadstone32/lite:default
    make treadstone48/rev1:default
    make treadstone48/rev2:default
fi
```

終わったらmakeしたファイルのサイズチェックをしています。これも対象となるフォークのブランチにあるスクリプトに入れています。ファイルサイズの計算はmakeしたときに出てくるファームサイズと同じ計算方法で出してます(自分設計のKeebのMCUは全部AVR)。ファームのサイズを超えちゃったままMCUに書き込むと文鎮化しちゃて最悪なのでエラーを返してヤバいことがわかるように。  

```bash
#!/bin/bash -eu
# _release_own_build.sh

# ファイルサイズチェック（makeで生成時エラーが出てたような気もするけどciのログ出し用途も兼ねて）
if [ "$1" = "-hexcheck" ] || [ "$1" = "-via" ] || [ "$1" = "-release" ]; then
    for file in `ls *.hex`; do
        filesize=`avr-size --target=ihex $file | awk 'NR==2 {print $4}'`
        oversize=$(($filesize-28672))
        remining=`printf '%5d' $(($oversize*-1))`
        filesizepf=`printf '%5d' $filesize`
        echo -e 'filesize:[' "$filesizepf" ']\tremaining:[' "$remining" ']\tname:[' $file ']'
        if [ $oversize -gt 0 ]; then
            ESC=$(printf '\033')
            echo "${ESC}[31mERROR: $file file was oversize!!!!!!!!!!${ESC}[m"
            exit 1
        fi
    done
fi
```

で、終了したらslackに成功か失敗の判定とmakeしたファイルサイズを通知しています。  
(なんでか通知が0:21なんだけどヨシ！)  

![img](/assets/photos/qmk-auto-merge-action.jpg)

毎週月曜の夜中に一度走らせてて２ヶ月ほど経ちますが今のところ問題なくマージされてってます。  
[このへんのログ](https://github.com/marksard/qmk_firmware_hex/actions/runs/1132928279)みると3分くらいなので、無料枠でも全然余裕です。ただ、このactionsを登録しているリポジトリの更新がないと60日で自動停止するのでそこんところは注意する必要がありますね…。  


重い腰上げて慣れない事したあとに限って、新しくキーボード設計するでもなく新機能に手を出すでもなく仕事に追わてしまっている日々を過ごしています。いつか「こんなこともあろうかと」という一言をつぶやければいいな…  
