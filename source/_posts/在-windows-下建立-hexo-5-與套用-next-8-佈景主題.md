---
title: 在 Windows 下建立 Hexo 5 與套用 Next 8 佈景主題
tags:
  - Hexo
  - Windows
categories:
  - - 架站相關
date: 2021-03-10 22:12:00
---

_這裡只會說如何把基礎環境設定好，其他的就自己看[Hexo 官網](https://hexo.io/zh-tw/docs/)_

## 搞定 Node.js 部分

### NVS (Node Version Switcher)

至[Github](https://github.com/jasongin/nvs/releases/latest)下載最新版的 NVS 並且把它安裝起來

安裝起來之後，輸入以下：

```shell
nvs add lts
nvs link lts
```

打完之後，重開終端機或是跟 CLI 有關的東西，輸入 `node --version` 應該就會跑出版本號了。

### 安裝 Hexo

```shell
npm install -g hexo-cli
```

## 初始化 Hexo

```shell
hexo init <folder>
# 或是先切到資料夾底下再 hexo init 也可以
```

設定的部分請參考官方說明文件

## 安裝 Next 佈景主題

我們借助 npm 來幫忙

進入 Hexo 所在的目錄，並輸入：

```shell
npm install hexo-theme-next
```

然後設定 `_config.yml` ，修改 `theme:`:

```shell
theme: next
```

並把 Next 的設定檔複製過來 :

```shell
cp node_modules/hexo-theme-next/_config.yml _config.next.yml
```

這樣就可以開始修改 `_config.next.yml` 來自訂佈景主題了。

## 其他部分

我這邊就只先做 Hexo 和 Next 的套用，更多資訊參考一下官網或是搜尋都會得到解答。