---
title: torch.load读入torch 1.6的模型报错
date: 2020-10-27 17:38:09
tags: [pytorch]
---

torch1.6版本中，对torch.save进行了更改.The 1.6 release of PyTorch switched torch.save to use a new zipfile-based file format. torch.load still retains the ability to load files in the old format. If for any reason you want torch.save to use the old format, pass the kwarg _use_new_zipfile_serialization=False.

## 方法1
升级pytorch到1.6版

## 方法2
使用pytorch读入,再保存
```bash
device = 'cuda'
net = model()
saved_net = torch.load("nozip.pth", map_location=device)
net.load_state_dict(saved_net)
net.to(device=device)
torch.save(net.state_dict(), 'nozip.pth', _use_new_zipfile_serialization=False)
```
