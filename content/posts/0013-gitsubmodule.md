---
author: "Issei Komori"
title: "Git Submodule化できないときの対処"
date: 2020-04-12T01:15:02+09:00
draft: false
tags: ["Git"]
weight: 10
---

# Git Submodule化できないときの対処

## キャッシュが残っている可能性
- まずは、キャッシュのクリアを行います。

{{< highlight bash >}}

git rm –cached サブモジュールディレクトリ
git submodule update --init --recursive

{{< /highlight >}}

## サブモジュールの削除
- 既に存在しているサブモジュールがあったら削除してしまいます。
- ディレクトリごと消すことがポイント

{{< highlight bash >}}

git submodule deinit -f サブモジュールディレクトリ
git rm -f サブモジュールディレクトリ

{{< /highlight >}}

## サブモジュールの追加
- 正しく追加できていれば2つのgitがソース管理プロバイダーに見えているはずです。

{{< highlight bash >}}

git submodule add git@github.com:<user>/<path>.git 

{{< /highlight >}}
