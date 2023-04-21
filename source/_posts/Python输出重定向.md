---
title: Python输出重定向
date: 2019-01-10 23:04:49
tags: [Python]
---

Python 输出重定向的例子
<!-- more -->

```bash
import sys
temp = sys.stdout

file = open('f.txt','w')
sys.stdout = file

print (1,2,3)
sys.stdout.flush()
file.write("aaa")
file.flush()

sys.stdout.close()
sys.stdout = temp
```