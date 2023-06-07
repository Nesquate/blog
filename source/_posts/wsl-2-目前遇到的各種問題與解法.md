---
title: WSL 2 目前遇到的各種問題與解法
tags:
  - Windows
categories:
  - - 系統維運
date: 2020-07-26 20:40:00
---

> 玩具，終究只是玩具 — Nesquate 2020/07/26

最後更新：2020/07/26

## 記憶體吃太兇

![](/post_image/2020/wsl-2-problems/image-01.png)

當時我在跑以下指令：

```bash
repo sync -f -c --no-tags -j16 -q
```

然後 WSL 2 直接把我的記憶體徹底吃光，毫不留情面。

解決方法是在使用者目錄底下新增 `.wslconfig` 的檔案，填入以下內容，下 `wsl --shutdown` 後重開 Linux 發行版就好：

```
[wsl2]
memory=6GB
swap=0
localhostForwarding=true
```

解法來源：[這裡](https://github.com/microsoft/WSL/issues/4166)

## 刪除檔案之後 WSL 2 仍然佔用硬碟空間

![](/post_image/2020/wsl-2-problems/image-02.png)

我已經把檔案清光光，但是看來 VHDX 檔案沒有跟著縮小， 解決方法就是手動把 VHDX 給縮小。

開啟 Powershell ，到 WSL 發行版目錄底下的 `LocalState` 資料夾，輸入以下指令：

```
wsl --shutdown # 先徹底關閉 WSL Lxss 的服務
optimize-vhd -Path .\ext4.vhdx -Mode full
```

這樣就可以減少好幾百 GB 的空間佔用了（但還是有 10 幾 G 那麼大）

解法來源：[這裡](https://github.com/microsoft/WSL/issues/4699)