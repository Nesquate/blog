---
title: Arch Linux GunPG 近期驗證遇到的問題
tags:
  - Arch Linux
  - GPG
  - Linux
categories:
  - - 系統維運
date: 2020-08-11 22:24:00
---

![](/post_image/2020/arch_linux-gpg-key-import-problem/image-01.png)

```bash
gpg: keyserver receive failed: No name
```

其實在這個訊息跳出來之前，還有噴過另外一種訊息（但是很遺憾的是我沒有把它記錄下來，因為只是要在 Pacman-key 上指定 Keyserver 就解決了），原本想說忽略就好了，但是很不巧的連 `pacman-key --refresh-keys` 這個指令都會噴出上面這種訊息，讓我不得不重視這個問題。

後來經過測試的時候發現其實這是 Keyserver 的問題，之前噴其他奇怪的訊息的時候原本想說是 Key 的問題，不過現在的狀況完全推翻了以前的假設，看來就是 Keyserver 的問題了。

我後來換去 ubuntu 官方的 keyserver ( [keyserver.ubuntu.com](https://keyserver.ubuntu.com/) )，如果 Arch Linux 預設的 Keyserver 沒壞掉，我現在也不用這麼辛苦...。

## 更換方法

總共要修改兩個檔案

1.  `/etc/pacman.d/gnupg/gpg.conf`
    *   這檔案 Pacman 驗證 Key 時會參考這個設定檔來處理
2.  `~/.gnupg/gpg.conf`
    *   這檔案會是其他非 root 程式（如 yay）在驗證時會參考這個檔案來處理

沒有 `~/.gnupg/gpg.conf` 就自己建立一個，不用怕，不會有事。 然後在兩個檔案都加入下面內容：

```xml
keyserver keyserver.ubuntu.com
```

存檔，下 `sudo pacman-key --refresh-keys` 跑跑看，沒啥問題就收工。

## 786C63F330D7CB92 無法驗證的問題

這個我 [查了一下](https://forum.endeavouros.com/t/are-the-key-servers-down-for-anyone-else/6271/22) 看起來只能自簽來解決。

```bash
sudo pacman-key 786C63F330D7CB92 --lsign
```