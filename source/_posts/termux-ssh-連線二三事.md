---
title: Termux SSH 連線二三事
tags:
  - Android
  - SSH
  - Termux
categories:
  - - 架站相關
date: 2020-02-24 19:14:00
---

## 安裝

安裝其實不難， `pkg install openssh` 或 `apt install openssh` 就好了。 然而其實也不用再做而外的設定，預設就是 **密碼** 和 **私鑰** 兩種都有開。

如果只是要用密碼登入，再輸入 `passwd` 指定密碼就可以了； 如果是要用私鑰登入，請繼續往下看。

(登入的部分也往下看)

## 私鑰登入

稍微整理了一下，流程大概是：

### 產生私鑰

這部分只要打 `ssh-keygen` 就好了

### 信任私鑰

這邊打以下指令就好

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

這樣私鑰的部分就解決了。你可以把 `id_rsa.pub` 和 `id_rsa` 複製到電腦上。 如果有 root 的話這邊應該是沒什麼問題，如果沒有的話...自己想辦法複製到 `/sdcard` 上 (用指令)。

## 登入

登入有幾個要注意的事情：

1.  Termux 的 SSH Port 為 **8022** ，沒錯不是 22 ，這讓我一度以為自己的設定哪裡有問題。
2.  注意網路環境，有些網路環境會把所有的 Port 鎖光光。

登入之前只要先在 Termux 輸入 `sshd` 最後再用相關軟體登入就好了 (這裡使用 MobaXterm)

![](/post_image/2020/termux-ssh/termux-ssh-mobaxterm.png)

如果要停止 SSH 服務，只要輸入 `pkill sshd` 就行； 如果要在啟動 Termux 時自動啟動 SSH 服務，請善用 Shell 的設定檔，如 bash 的 `.bashrc` 或 zsh 的 `.zshrc` 。

## 參考來源

*   [使用Termux把Android手机变成SSH服务器 - 暗无天日](http://blog.lujun9972.win/blog/2018/01/24/%E4%BD%BF%E7%94%A8termux%E6%8A%8Aandroid%E6%89%8B%E6%9C%BA%E5%8F%98%E6%88%90ssh%E6%9C%8D%E5%8A%A1%E5%99%A8/)
*   [Termux 設定SSH Server - 阿舍的隨手記記、隨手寫寫...](https://www.arthurtoday.com/2017/04/termux-install-and-config-ssh-server.html)