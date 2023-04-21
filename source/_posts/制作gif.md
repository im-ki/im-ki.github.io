---
title: 制作gif
date: 2021-02-22 09:56:51
tags: [gif]
---

```bash
convert -delay 30 {0..17}.png -loop 0 test.gif
```
delay越大，播放速度越慢．loop为0表示无限循环播放．
