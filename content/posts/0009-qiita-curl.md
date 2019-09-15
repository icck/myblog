---
author: "Issei Komori"
title: "Qiita API でいいねとVIEW数をCurlで取得する方法"
date: 2019-09-15T13:39:55+09:00
draft: false
tags: ["Qiita"]
weight: 10
---

# はじめに
QiitaにはAPIが用意されています。
私もQiitaに投稿しているのですが、Google Analyticsでは取得しきれない「いいね」の情報が欲しくてCurlで取得する方法を調べてみました。

APIのドキュメントは以下にあります。

https://qiita.com/api/v2/docs

# APIを使うための準備
個人の情報を取得するためにはアクセストークンを発行する必要があります。

https://qiita.com/settings/tokens/new

にアクセスし、アクセストークンを発行しましょう。

![t1](/posts/0009/t1.png)


発行できたら、あとはドキュメントに従ってCurlでデータを取得していきます。

{{< highlight bash >}}
QIITA_TOKEN=XXXXXXXXX
# いいね、記事タイトル→CSV形式
curl -sH "Authorization: Bearer ${QIITA_TOKEN}" \
  "https://qiita.com/api/v2/authenticated_user/items?page=1&per_page=100" \
  | jq -r '.[] | [.likes_count, .title, .id] | @csv'

0,"Session Managerでプライベートサブネット上のEC2にSSHする方法","e8dc6b039ef95d7b3d72"
0,"5分でAWS Elastic Beanstalk(作って、修正して、お片付け)","e30d62e878b1b2c6f3d7"
2,"CodeBuildでECRビルドエラーから得た4つの知見","bcf118a38c2a691a837d"
0,"AWS Cloud9でAWS SAM CLI環境作成～Hello World(Python)まで","873721df727943beb52d"
3,"[Docker for Windows]Kubernetesを動かしてみる","91eac9da094666e47c62"
0,"Docker for WindowsでEnabled Kubernetesが出来なくてハマったときの解決方法","c40eede0bb120d643f2c"
1,"[小ネタ]fishでaws-cliの補完機能を使うための手順","e57a86ba65c14a7fbb19"
9,"Dockerでaws-cli環境を作ればアカウント切替が簡単に","40545486a342179a5bd3"
7,"Pythonista3でstashを実行しNo module named 'urlparse'が出た場合の解決方法","a33a3b113dea0b2fa850"
5,"QiitaにGoogleAnalyticsの設定する。数字を眺めて楽しい","56060f8a72863ee05924"
3,"バッチファイルを経由すればPowerShellファイルの実行権限を変えずに指定権限で実行できた","60bc30a793a8f4ecdc2b"
27,"Docker for Windowsをインストール後 could not read CA certificateエラーが出た時の解決方法","60e1c1f8b6a05c8fdf72"
10,"AWS認定ソリューションアーキテクトアソシエイト合格[2018.04]","a1416e818135a424ad89"
2,"[AWS Cloud9]EC2自動停止を検知して別の処理を始める","c841fa99af2c88a6d956"
5,"AWS Cloud9でpythonライブラリを含めたLambdaのデプロイ","6cc042d2693b84c96905"
16,"AWS Cloud9でLambdaの作成、テスト、デプロイまでの手順まとめ","4c2fa4e0bd87ce214c92"
50,"AWS Cloud9を動かしてみた","7705ef6871e261d6535e"
1,"Win10 Creators Update後、vagrant upがエラーになったときの解決方法","37e3b88c657b4e4cdb51"
11,"SNSでSlack投稿","75f484cd3ccd944cd4c2"


{{< /highlight >}}

VIEW数は記事単位でしか取得できないようです。（nullが取得されてしまう）
プログラムで記事をループしてVIEW数を取得する必要がありそうですね。

{{< highlight bash >}}
ITEM_ID=7705ef6871e261d6535e
curl -sH "Authorization: Bearer ${QIITA_TOKEN}" \
  "https://qiita.com/api/v2/items/${ITEM_ID}" \
  | jq -r '.page_views_count'

21895

{{< /highlight >}}

# さいごに
投稿した後にVIEW数などは日々気になるものです。<br>
私も自動で取得する仕組みを作るなど応用してみたいと思います。
