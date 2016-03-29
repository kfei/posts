+++
title = "Convert text to upper/lower case using sed"
date = "2013-10-01T14:51:00+08:00"
+++
最近需要寫一段程式去解析並編輯 /etc/hosts 這個檔案, 由於 hostname 是不區分大小寫的, 所以在操作前先統一轉換成大寫會較方便. 試了幾種不同的寫法, 發現還是 sed 幹起來最漂亮, 可以一句話搞定.
```sed
sed 's/\(.*\)/\U\1/'
```
*Hint:* 如果要轉小寫則改用 `\L`

簡單說明一下:
1. `(.*)` 可以 match 所有字符, 因為 `(` 和 `)` 都不是 meta charactor, 故再加上 escape sequence 就變成 `\(.*\)`.
2. `\U` 把接在後面的字符都轉大寫.
3. `\1` 參考第一項 match 到的字符.