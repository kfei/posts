+++
title = "TERM environment variable not set"
date = "2013-10-19T14:30:00+08:00"
+++
腳本部署出來的機器通通遇到這個怪問題, `rsync -e ssh` 或 `scp` 往裡面丟東西時會出現:
```
TERM environment variable not set.
```
這個錯誤訊息, 檢查一下 server/client 都有 `TERM=linux` 這個環境變數了, 那這個錯誤原因為何?

原來, 是我寫在 `.bashrc` 裡的 auto-run script 產生 stdout 干擾了 ssh protocol 的資料流...
簡單來說, sshd 會幫 rsync/scp 進來的 user fork 一個 bash,
然後執行 /bin/true 把資料流重定向, 也就是說
`rsh <remotehost> /bin/true > out.dat`
應該要得到是一個空的輸出. 如果非空, 那就剉屎了.

最近遇到的一個小地雷, 記錄一下.