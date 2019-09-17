---
author: "Issei Komori"
title: "Qiita API②いいねとVIEW数をLambdaで取得してS3へ保存"
date: 2019-09-17T23:00:33+09:00
draft: false
tags: ["Qiita","AWS","Lambda","Python"]
weight: 10
---

前回投稿した[「Qiita API でいいねとVIEW数をCurlで取得する方法」](https://icck.github.io/posts/0009-qiita-curl/)を利用し、Lambdaで実行してs3にjsonファイルとして格納してみようと思います。

![1](/posts/0010/1.png)

# Code

以下のコードを実行しs3に格納していきます。<br>
※事前にバケットは作成しておきます。

{{< highlight python >}}

import csv
import json
import logging
import os
import sys
import boto3

import requests

formatter = '%(asctime)s %(name)-12s %(levelname)-8s %(message)s'
logging.basicConfig(level=logging.WARNING, format=formatter)
logger = logging.getLogger(__name__)

def get_next_url(response):

    link = response.headers['link']
    if link is None:
        return None

    links = link.split(',')

    for link in links:

        if 'rel="next"' in link:
            return link[link.find('<') + 1:link.find('>')]
    return None

def get_items(token):

    url = 'https://qiita.com/api/v2/authenticated_user/items'
    headers = {'Authorization': 'Bearer {}'.format(token)}

    items = []
    while True:
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        items.extend(json.loads(response.text))
        logger.info('GET {}'.format(url))

        url = get_next_url(response)
        if url is None:
            break

    for item in items:

        # ビュー数
        url = 'https://qiita.com/api/v2/items/{}'.format(item['id'])
        logger.info('GET {}'.format(url))
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        item['page_views_count'] = json.loads(response.text)['page_views_count']

        # ストック数
        url = 'https://qiita.com/api/v2/items/{}/stockers'.format(item['id'])
        logger.info('GET {}'.format(url))
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        users = json.loads(response.text)
        for user in users:
            logger.info({
                'id': user['id'],
                'name': user['name']
                })
        item['stocks_count'] = len(users)

    return items

def upload_s3(items):
    s3 = boto3.client('s3')
    bucket_name = s3.Bucket(os.environ['BUCKET_NAME'])
    json_key = 'output/sample.json'

    s3.put_object(Bucket=bucket_name, Key=json_key, Body=json.dumps(items,ensure_ascii=False))

def lambda_handler(event, context):
    TOKEN = os.environ['TOKEN']
    items = get_items(TOKEN)
    upload_s3(items)
    return items

{{< /highlight >}}

# 実行後、バケット確認

JSONファイルが保存されていることを確認しました。

![2](/posts/0010/2.png)

# さいごに
- pythonの`requests`を使ってAPIからデータを取得できました。
- 今後は、以下に発展させていこうと思います。
  - ファイル名にタイムスタンプの追加
  - CloudWatchから定期的に実行
  - 収集したログの可視化