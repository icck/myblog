---
author: "Issei Komori"
title: "fishでaws-cliの補完機能を使う"
date: 2019-09-02T02:17:32+09:00
draft: true
tags: ["AWS","fish","aws-cli"]
weight: 10
---

# bash -> fishに変更した場合、aws-cliの補完機能を有効にする方法

{{< highlight bash >}}
# 位置を特定します
$ which aws_completer

# 存在しない場合は作成します
$ vim ~/.config/fish/config.fish

# aws_completerのパスはwhichで調べたパスに適時変更しconfig.fishに追記して保存します
$ complete -c aws -f -a '(begin; set -lx COMP_SHELL fish; set -lx COMP_LINE (commandline); /usr/local/bin/aws_completer; end)'

#確認
$ aws s + tab
{{< /highlight >}}

# さいごに
これで、良いfish Lifeを過ごせます。
