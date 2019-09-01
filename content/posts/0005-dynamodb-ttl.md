---
author: "Issei Komori"
title: "Dynamodb TTL(Time to Live、データを自動削除する機能)を使ってみた"
date: 2019-09-01T09:17:57+09:00
draft: true
tags: ["AWS","DynamoDB"]
weight: 10
---

# TTLとは
- レコード毎にデータの有効期限を指定でき、その有効期限を過ぎるとデータが自動削除される機能です
## 利用例
- セッションデータを格納し、自動で削除
  - Itemが存在する or しない のみでセッションの有効/無効 制御を行う


# まとめ

# 実際にやってみた
## テーブル作成

{{< highlight bash >}}

aws dynamodb create-table --table-name ttl \
--attribute-definitions AttributeName=id,AttributeType=S \
--key-schema AttributeName=id,KeyType=HASH \
--provisioned-throughput \
ReadCapacityUnits=5,WriteCapacityUnits=5

{{< /highlight >}}




## お片付け

{{< highlight bash >}}
aws dynamodb delete-table --table-name ttl
{{< /highlight >}}

# さいごに


# Table of Contents
  * [Chapter 1](#chapter-1)
  * [Chapter 2](#chapter-2)
  * [Chapter 3](#chapter-3)

## Chapter 1 <a id="chapter-1"></a>

## Chapter 2 <a id="chapter-2"></a>

## Chapter 3 <a id="chapter-3"></a>

![Minion](https://octodex.github.com/images/minion.png)

https://learn.netlify.com/en/cont/markdown/
