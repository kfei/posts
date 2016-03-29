+++
title = "OpenSSL 正夯"
date = "2014-04-09T13:53:00+08:00"
+++
花了半天的時間跟著 [這篇標題超嗆的文章](https://www.peereboom.us/assl/assl/html/openssl.html) 一起 trace OpenSSL 的 code, 真沒想到天天在用的東西, 而且還是被整個世界大幅採用的庫, 居然藏了這麼多垃圾 code... 先不說邏輯錯誤, 一眼就能看到的莫名其妙又亂七八糟的縮排, 真的讓人捏一大把冷汗, 原來我們天天賴以為生的 "secure" internet 居然是這樣搭建出來的...

總之, 還沒 patch 的快點吧! 這次的影響非常大, 重點是攻擊幾乎沒有難度可言, 網路上一堆 POC script, 隨便就能 dump 出 server memory (反過來要 dump client memory 也是一樣輕鬆寫意)!

更多細節參考 http://heartbleed.com/ (連 domain name 都申請了 XD)