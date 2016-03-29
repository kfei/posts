+++
title = "Persistent cache in QEMU or librbd?"
date = "2014-06-13T09:05:00+08:00"
tags = ["qemu","vm"]
+++

我們知道無論是 QEMU 或是 librbd 都有提供 writeback cache. 同時也有兩個明顯的效能議題:

  * cache 的生命週期跟隨 QEMU process, 也就是說一旦 VM shutdown, cache 也就跟著消失.
  * 多個 QEMU process 間無法共享 cache, 而這在某些情境 (e.g. VDI) 下卻非常有用.

解決這兩個問題的方式無非是將 cache 持久化在一些高速裝置上. 事實上大約一年前在 QEMU 的 mailing list 上就[討論](https://lists.gnu.org/archive/html/qemu-devel/2013-06/msg03649.html)過這個議題, Ceph 的作者也發表了看法, 只可惜後續好像沒什麼進展?
