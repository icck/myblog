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
- 低アクセスデータをアーカイブTBLへ移動
  - メインTBLと同定義、キャパシティーだけ下げてOLDデータを保存
- 低アクセスデータを削除
  - 削除時にトリガーして、LOGを残す
- トリガーさせるにはStreamの有効化が必要

# TTL詳細
- TTL の有効化
  - カラムで指定した有効期限を過ぎると、自動でデータが消える
  - 変更が完全に処理されるまでに最大で 1 時間かかる
- TTL 属性
  - 項目の TTL タイムスタンプを保存する DynamoDB 属性の名前
- 有効期限はNumber型のエポック時間(Unixtime（ミリ秒無し）)を指定
  - 現在の時間を、項目の Time To Live 属性に保存されている時間と比較
  - 属性に保存されているエポック時間の値が現在の時間よりも少ない場合、項目は期限切れとマークされ、その後に削除される
- データは48時間以内に削除される
  - リアルタイム性が重要でない場合（48時間後に消えてもOKな場合）は、とても有効な機能
- スループットは消費せず、データ容量が減らせる

## エポック時間形式(Unixtime)
- コンピューターシステム上での時刻表現の一種
- UNIXエポック、すなわち協定世界時 (UTC) での1970年1月1日午前0時0分0秒から形式的な経過秒数（すなわち、実質的な経過秒数から、その間に挿入された閏秒を引き、削除された閏秒を加えたもの）
- 現在時刻からエポック時間を取得する方法
  - Linux ターミナル: date +%s
  - Python: import time; long(time.time())
  - Java: System.currentTimeMillis() / 1000L
  - JavaScript: Math.floor(Date.now() / 1000)


# 実際にやってみた
## テーブル作成

{{< highlight bash >}}

aws dynamodb create-table --table-name ttl-sample \
--attribute-definitions AttributeName=id,AttributeType=S \
--key-schema AttributeName=id,KeyType=HASH \
--provisioned-throughput \
ReadCapacityUnits=5,WriteCapacityUnits=5

{{< /highlight >}}

## TTLの有効化

{{< highlight bash >}}

aws dynamodb update-time-to-live --table-name ttl-sample --time-to-live-specification "Enabled=true, AttributeName=ttl"

{
    "TimeToLiveSpecification": {
        "AttributeName": "ttl",
        "Enabled": true
    }
}

aws dynamodb describe-time-to-live --table-name ttl-sample
{
    "TimeToLiveDescription": {
        "AttributeName": "ttl", 
        "TimeToLiveStatus": "ENABLED"
    }
}

{{< /highlight >}}

## データ追加(TTL:５日)

{{< highlight bash >}}

EXP=`date -d '+5 days' +%s`
aws dynamodb put-item --table-name "ttl-sample" --item '{"id": {"S": "1"}, "ttl": {"N": "'$EXP'"}}'

aws dynamodb scan --table-name ttl-sample
{
    "Count": 1,
    "Items": [
        {
            "id": {
                "S": "1"
            },
            "ttl": {
                "N": "1567788232"
            }
        }
    ],
    "ScannedCount": 1,
    "ConsumedCapacity": null
}

{{< /highlight >}}

## お片付け

{{< highlight bash >}}
aws dynamodb delete-table --table-name ttl-sample
{{< /highlight >}}

# さいごに
- 特定のしきい値があって、消したいけどリアルタイム性は求めないという場合は使える機能
- s3のライフサイクル機能のDynamo版と認識してOK

