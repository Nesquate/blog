---
title: 利用 userChrome.css 隱藏最上面的 Tab Bar (Firefox 81)
tags:
  - Firefox
  - userChrome.css
categories:
  - - 系統維運
date: 2021-03-17 16:21:00
---

## 隱藏的理由

自從安裝了 [Tree Style Tab](https://addons.mozilla.org/zh-TW/firefox/addon/tree-style-tab/) 之後，Firefox 最上面的 Tab bar 其實就用不太到了。  
出於過往的習慣，自己還會一直想要在上面點 Tab ，但目前我未關閉的 Tab 有八十幾個，而且已經用 Tree Style Tab 管理上下關係了，因此想要把上面的 Tab 列表給隱藏。

本文從啟用 userChrome.css 功能開始，與大家說明如何透過 userChrome.css 隱藏最上面的 Tab Bar。
<!-- more -->
## 啟用自動載入的 Flag

這裡會進入 Firefox 的 `about:config` 頁面。

在瀏覽器上輸入 `about:config` 之後，  
在裡面的搜尋框搜尋 **toolkit.legacyUserProfileCustomizations.stylesheets** ，並把他的布林值改為 **true**：

![](/post_image/2021/firefox-hide-tabbar-using-userChrome/1-flag_enable-01.png)

改完之後記得重啟 Firefox 。

## 進入設定檔目錄，放入 userChrome.css

點選 Firefox 右上角的**選單 > 說明 > 疑難排解資訊**，在畫面中有目前 Firefox 設定檔目錄的路徑，左邊有「開啟資料夾」的按鈕，請點它：

![](/post_image/2021/firefox-hide-tabbar-using-userChrome/2-putUserChrome-1.png)

開啟資料夾之後，請在裡面新增一個叫做 **chrome** 的空資料夾，並在該資料夾新增 **userChrome.css** 的文字文件 ( 須注意副檔名 )。

然後在 `userChrome.css` 當中放入以下內容，然後存檔**並重啟 Firefox**：

```css
@-moz-document url(chrome://browser/content/browser.xul),
               url(chrome://browser/content/browser.xhtml) {
    /* Your style rules for the user interface go here */
    .titlebar-spacer {
        display: none;
    }

    #TabsToolbar-customization-target {
        display: none;
    }
}
```

這樣你的 Tab Bar 就被隱藏起來了：

![](/post_image/2021/firefox-hide-tabbar-using-userChrome/2-putUserChrome-2.png)

## 補充：如果我想要自己改其他樣式...

如果你想要修改其他樣式的話，除了要做上面啟用 Flag 的動作之外，還要再多開一些東西才會有效果。  
然而修改其他樣式需要有一些 CSS 、 HTML 的基礎，這部分可能就要麻煩自我學習了。

按下鍵盤的 **F12/Ctrl+Shift+I** 或是 **選單 > 網頁開發者 > 網頁工具箱**，開啟「網頁工具箱」 ( 圖片名詞有誤，請忽略 )，之後進入設定：

![](/post_image/2021/firefox-hide-tabbar-using-userChrome/3-customStyle-1.png)

關閉網頁主控台，按下鍵盤的 **Ctrl+Shift+Alt+I** 或是 **選單 > 網頁開發者 > 瀏覽器工具箱**，開啟「瀏覽器工具箱」。  
請注意開啟時可能會出現這個視窗，請允許它：

![](/post_image/2021/firefox-hide-tabbar-using-userChrome/3-customStyle-2.png)

等待出現這視窗並且完全載入之後，你就可以如同對網頁除錯般地調整 Firefox 的介面：

![](/post_image/2021/firefox-hide-tabbar-using-userChrome/3-customStyle-3.png)

切記，修改完之後記得把結果儲存在 `userChrome.css` 當中唷！