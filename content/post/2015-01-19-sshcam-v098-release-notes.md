+++
title = "sshcam v0.9.8 release notes"
date = "2015-01-19T06:11:00+08:00"
tags = ["golang", "sshcam"]
+++

總算利用空閒的假日時間把 [sshcam](https://github.com/kfei/sshcam) 這兩個重要的功能補完了!

[![1.png](/images/assets/2015-01-19-sshcam-v098-release-notes/1.png)](https://www.youtube.com/watch?v=pAa-pGda9kY)

## 2 倍垂直解析度

利用 Unicode HALF BLOCK 字元 ([U+2580/U+2584](http://www.alanwood.net/unicode/block_elements.html)) 在一個 row 裡面插入兩條水平掃描線. 解析度提高一倍之外, 每個 "pixel" 看起來也更趨近正方形:

![7.png](/images/assets/2015-01-19-sshcam-v098-release-notes/7.png)

**ASCII Only:**
![2.png](/images/assets/2015-01-19-sshcam-v098-release-notes/2.png)

**Unicode HALF BLOCK:**
![3.png](/images/assets/2015-01-19-sshcam-v098-release-notes/3.png)

再更進一步, 事實上 Unicode 字元集裡還有 QUADRANT BLOCK (U+2596 to U+259F) 可以同理將水平方向的 pixel 增加一倍, 不過依然還是只有兩個顏色可以用, 效果很有限就是了. 且考慮到目前畫面 refresh 已經有一些遲頓, 因此暫時不實作, 等 render 的效能問題解決之後再來玩.

## 彩色畫面

原來只支援灰階顯示的原因在於 RGB 到 xterm 256 色盤的映射, 用簡單幾何距離計算色距的效果太差, 畫面的辨識度比灰階模式低許多. 但後來試了 [CIE94](http://www.chromapure.com/colorscience-CIE94.asp) 演算法之後發現效果有明顯改善, 因此也就加上 `--color` 模式.

**Grayscale:**
![4.png](/images/assets/2015-01-19-sshcam-v098-release-notes/4.png)

**Colorized:**
![5.png](/images/assets/2015-01-19-sshcam-v098-release-notes/5.png)

不過, 彩色模式下的畫面 refresh 遲頓感似乎又升高了一級, 猜測可能跟大量的 ANSI 控制碼有關, 這個問題留待有時間再來研究研究. (*Update 20150120:* 遲鈍感已透過引入 frame caching 及大量減少 Printf 獲得改善) 再來如果還想更暴力一點, 其實已經有不少 terminal emulator [支援 True Color 模式](https://gist.github.com/XVilka/8346728)了...

最後, 這一連串的過程讓我恍然大悟覺得原來彩色電視機才是人類科技真正偉大的發明啊!
