---
title: "2026-8-24"
date: 2026-08-27
description: "看代码过程中踩过的坑"
tags: ["code review", "c++"]
showTableOfContents: true
---

# code review注意点
当执行`code review`并设计代码时应当先查看所有这个标签类似的标签所在的所有位置，查看是否有除了提取逻辑之外的地方有用到这个标签，如果用到了也要加类似的代码结构。不可以有漏网之鱼，就算代码执行了，没有报错，也要进行仔细排查
