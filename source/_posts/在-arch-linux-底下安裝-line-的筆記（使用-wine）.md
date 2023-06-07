---
title: 在 Arch Linux 底下安裝 LINE 的筆記（使用 Wine）
tags:
  - Arch Linux
  - LINE
  - Linux
categories:
  - - 系統維運
date: 2020-05-23 16:50:00
---

> (2022/03/30整理更) 目前已經有新版的教學可以看，請點以下連結
> 
> [在 Arch Linux 安裝最新版的 LINE 總整理](https://blog.nesquate.tw/2022/07/13/%e5%9c%a8-arch-linux-%e5%ae%89%e8%a3%9d%e6%9c%80%e6%96%b0%e7%89%88%e7%9a%84-line-%e7%b8%bd%e6%95%b4%e7%90%86/)

[HackMD 重點整理版](https://hackmd.io/@nesquate/note-for-archlinux-wine-line)  
_（其實內容差不多）_

會寫這篇文主要是紀錄一下我在 Arch Linux 底下安裝 Wine 與 LINE 的過程，以作為之後的參考。  
因為在 Ubuntu 底下安裝之後遇到不少問題，像是字體問題就是令我非常頭大的事情。

我原本是先寫好 HackMD 筆記，想說公開丟著不管它好了，但是我想到我的部落格文章非常少，只好......。

## 開啟 multilib 軟體庫

因為 LINE 需要 Wine 32 Bit 才能使用，所以要把 32 位元的軟體庫（`multilib`）給開起來

打開 `/etc/pacman.conf` 並且取消註解這兩行：

```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

如果 LINE 可以在純 64 位元底下執行就好了，  
不過看起來 LINE 目前還是用 32 位元編譯（為了相容性）

最後輸入

```bash
sudo pacman -Sy
```

更新本地軟體庫

## 安裝 Wine 與 Winetricks

順邊裝個 Winetricks 比較好管理

```bash
sudo pacman -S wine
sudo pacman -S winetricks
```

## 設定

### 建立 Wine 32 環境

```bash
WINEARCH=win32 wincfg
# 如果你要自訂其他路徑的話
WINEARCH=win32 WINEPREFIX=~/win32 winecfg 
```

### 安裝 Visual C++ Runtime 2008

```bash
winetricks vcrun2008
```

### 安裝 LINE

*   Gnome的作法
    *   以其他應用程式開啟 -> Wine Program Loader
*   終端機的作法： `wine LineInst.exe`

## 然後就沒有了？！

沒想到在 Arch Linux 搭配最新版的 LINE 與 Wine 居然沒什麼問題？！  
Ubuntu 還有字體問題，果然是包袱越重問題越多嗎

## 後記：Linux 悲歌

首先，不得不說我非常不喜歡用 LINE，  
要不是跟現實妥協我還真的是不會用它，連碰都不想碰。

再來，消費者面向的軟體在 Linux 非常非常缺乏，  
要不然就是，雖然有但是程式可用的功能跟 Windows/macOS 差異頗大。

舉例來說，  
Microsoft Office？沒上（只有上 Teams 而且還是 Beta？！）  
Edge Chromium？還沒上（到底要什麼時候才上啊？）  
還有本篇的 LINE。

只能說這真的是 Linux 悲歌了吧...。

## 參考文件

*   [Wine from Arch Linux Wiki](https://wiki.archlinux.org/index.php/Wine_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
*   [在 WINE 上使用 LINE](https://blog.xuite.net/d93921012/twblog/252066321-%E5%9C%A8+WINE+%E4%B8%8A%E4%BD%BF%E7%94%A8+LINE)