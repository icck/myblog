---
author: "Issei Komori"
title: "JavaでLambdaを利用してFizzBuzzしてみた"
date: 2019-09-06T23:44:41+09:00
draft: false
tags: ["Java"]
weight: 10
---

# FizzBuzzとは
- エンジニアがテストコードでよく書くネタ。Wiki参照<br>
https://ja.wikipedia.org/wiki/Fizz_Buzz

## Javaなら1行でシンプルに書ける。そうLambdaならね
{{< highlight bash >}}
import java.util.stream.IntStream;

public class FizzBuzz {

    public static void main(String[] args) {
        IntStream.rangeClosed(1, 100).forEach(
                i -> System.out.format("%s%s%n",
                            (i%3 == 0 ? "Fizz" : ""),
                            (i%5 == 0 ? "Buzz" : (i%3 == 0 ? "" : i))
                        )
                );

    }

}
{{< /highlight >}}

