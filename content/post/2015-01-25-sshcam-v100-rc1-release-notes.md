+++
title = "sshcam v1.0.0-rc1 release notes"
date = "2015-01-25T06:10:00+08:00"
tags = ["golang", "sshcam"]
+++

這個[版本](https://github.com/kfei/sshcam/releases/tag/1.0.0-rc1)主要做的工作都是性能方面的，對於先前版本存在的畫面遲鈍感和高 CPU 佔用問題有明顯的改善。

## 大幅減少 I/O 次數

I/O 本來就是昂貴的操作，而 terminal 又是 I/O device 裡特別慢的一種。在這種慢速裝置上進行大量 I/O，還包含了複雜的 ANSI color seqeunce，這是之前畫面更新遲鈍感的主因。因此：

  - 引入 frame caching 機制縮短 30% ~ 50% 的 ANSI 控制碼長度
  - ANSI 控制碼的化簡合併，從原來每個 pixel 一次 Print 合併為每個 row 一次 Print
  
當然，還可以再優化，例如 Print buffer 的長度應該按照實際因素來決定等。

## 大幅降低 CPU 使用率

透過 Go 的 [pprof][] 工具，發現在彩色模式下，CPU 有幾乎 50% 的時間花在 [CIE94][] 公式裡的 [math.Sqrt][] 運算，這在 pixel 數較多或 fps 較高的情況下會給 CPU 帶來不小的壓力。因此：

  - 將彩色模式下預設的色距算法改為 YIQ 空間裡的線性距離
  - 加上 `--max-fps` 參數控制每秒的張數上限

[pprof]: http://golang.org/pkg/net/http/pprof/
[CIE94]: https://gist.github.com/kfei/c563d577c995f943260b#file-using-cie94-L6
[math.Sqrt]: https://gist.github.com/kfei/c563d577c995f943260b#file-using-cie94-L5
