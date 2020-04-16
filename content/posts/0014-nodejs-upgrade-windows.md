---
author: "Issei Komori"
title: "Node.jsをUpgradeする手順（Windows）"
date: 2020-04-17T00:28:07+09:00
draft: false
tags: ["Node.js"]
weight: 10
---
## 事前作業：version確認

{{< highlight js >}}

$ node -v
v10.15.0
$ npm -v
6.4.1

{{< /highlight >}}

## 最新版をインストール
1. サイトにからLTSをダウンロードしインストール
https://nodejs.org/ja/

2. すべて次へ。

![1](/posts/0014/1.png)

## インストール後：version確認
{{< highlight js >}}

$ node -v
v12.16.2

$ npm -v
6.14.4

{{< /highlight >}}