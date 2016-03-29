+++
title = "Reordering windows in GNU Screen"
date = "2013-10-12T03:31:00+08:00"
tags = ["geek"]
+++

對於一個重度 Screen 使用者來說, workspace window 一開十幾個是很常見的事,
然而視窗一多, 愛整理工作環境的毛病就會犯, 這時候就需要把 Screen
裡的視窗重新排序分類一下了. 在 Screen 裡可以透過 `number [n]`
指令來更改當前視窗的編號:

```
C-a :number NUM
```

*Hint:* 如果想把一個視窗"釘死"在最右邊, 可以用 `C-a :number 999` 這樣的小技巧!
<!--more-->
