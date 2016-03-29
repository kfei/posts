+++
title = "gc after git filter-branch"
date = "2014-06-01T07:41:00+08:00"
tags = ["git"]
+++

```bash
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now
```
<!--more-->
