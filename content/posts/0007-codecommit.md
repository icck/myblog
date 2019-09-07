---
author: "Issei Komori"
title: "AWS CodeCommitの作成、プルリクエストまでの方法"
date: 2019-09-06T21:38:30+09:00
draft: false
tags: ["AWS","CodeCommit"]
weight: 10
---

![main](/posts/code3/c0.png)


# Codeシリーズはまずは使ってみる！
<br>
AWSにはCodeXXXと名前がついているDeveloperツールがいくつかあります。<br>
それらは単体でも使えますが、組み合わせて利用することが多いです。<br>
↓<br>
難しく考えず手を動かすことで、手っ取り早く感覚を掴んでいくのが学習しやすいです<br>
本記事では、リポジトリの作成、コミット&プッシュ、プルリクエストの3つを動かして行きます

## 概要（最低限）
- ソースコードを管理するGitリポジトリサービス
- サードパーティ(SourceTreeなど)利用可能
- プルリクエスト機能が利用可能

## リポジトリの作成

- リポジトリ名を入れて、作成ボタン ＞ 完成！！
- はじめにマネジメントコンソールでCodeCommitを選択しリポジトリを作成します。
- 名前を選択するだけ、簡単ですね。

![c1](/posts/code3/c1.png)

![c2](/posts/code3/c2.png)

- 次にgitを使う端末で設定をしていきます。
- 今回はAWS CLIを設定していることが前提になります。

{{< highlight bash >}}
git config --global credential.helper '!aws --region ap-northeast-1 codecommit credential-helper $@'
git config --global credential.UseHttpPath true

git clone [URLのクローン]


{{< /highlight >}}

## コミット&プッシュしてみる

- 通常のgitと同様の利用方法が使えることを確認します。

{{< highlight bash >}}
$ echo "hello codecommit!" > README.md

$ git add -A

$ git commit -m "add readme"

$ git push origin master
{{< /highlight >}}


- コミットされたことを確認しました。

![c3](/posts/code3/c3.png)


## プルリクエストしてみる

- ブランチを作成しファイルを修正、コミット%プッシュします。

{{< highlight bash >}}
(master) $ git checkout -b f1

(f1) $ echo "f1 add" >> README.md

(f1) $ cat README.md
hello codecommit!
f1 add

(f1) $ git add -A
(f1) $ git commit -m "f1 add"
(f1) $ git push origin f1

{{< /highlight >}}

- ブランチが作成されました。

![c4](/posts/code3/c4.png)

- さっそくプルリクエストしていきましょう。
- 作成ボタン＞プルリクエストを作成します。

![c5](/posts/code3/c5.png)

![c6](/posts/code3/c6.png)

![c7](/posts/code3/c7.png)

- プルリクエストは左のプルリクエストから確認できます。
- 未解決のプルリクエストを選択してマージしていきます。

![c8](/posts/code3/c8.png)

![c9](/posts/code3/c9.png)

- マージ後にブランチは削除を選択し、マージします。

![c10](/posts/code3/c10.png)

![c11](/posts/code3/c11.png)

- マージされたことを確認しました。

![c12](/posts/code3/c12.png)

# さいごに
- CodeCommitのUIはシンプルでGitHubの利用経験があれば学習コストはほぼゼロでした。
- さくっと共有できるプライベート環境を作りたいときの選択肢としてはありかも知れません。

