---
title: WinSerEssentials體驗 之 如何讓 Remote Web Access 與 WordPress 共存
tags:
  - Remote Web Access
  - Windows Server Essentials
  - 隨處存取
categories:
  - - 系統維運
date: 2017-11-25 09:09:47
---

最近在Windows Server Essentials體驗上搞了很久，起初是看到[可以用Windows Server架設自己的多媒體網站](https://dotblogs.com.tw/swater111/2013/05/09/103308)，就想說來用用看，結果沒想到**Windows Server 2016的Essentials體驗竟然不支援媒體擴充套件**，只好降級回Server 2012 R2，但2012 R2一玩下去的問題又很多，後來是把他們都解決了，然而有些是莫名其妙就好了，猜不透，之後有空再寫一篇心得文。 把Remote Web Access搞定之後，接下來就是想把WordPress裝在自己的電腦上，但這一塊讓我的腦袋徹底打結，原因是要設定的東西還真不少......。
<!-- more -->
### 搞定實體路徑

Remote Web Access在設定的過程中，會建立一些IIS的應用程式與虛擬目錄，並且會把Default Web Site的實體路徑改變。 當然這是Server 2012 R2以前的狀況，**在Server 2016的實體路徑完全沒有變化**，結果這樣反倒是造成一票人的困擾，因為不會自動導向（他們希望一連網址就是開啟Remote Web Access）。 那為什麼要改變預設路徑，當然是因為在C:\\Program Files\\Windows \\Server\\Bin\\WebApps\\Site路徑下管理網站頗不方便。 所以就，改吧。 ![01](/post_image/2018/06/01.png)

### [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 懶人搞定 WordPress

在IIS上安裝WordPress是非常非常非常麻煩的事情，你要設定PHP、MySQL還有URL Rewrite，一個一個設定會累死你自己。還好有Web Platform Installer可以搞定。 以下Web Platform Installer = WebPI。 沒看過WebPI的其實不要害怕，因為真的超簡單，點個新增安裝就好了XD。 ![02](/post_image/2018/06/02.png) 安裝會要求輸入MySQL的帳號密碼，完事之後還要設定一下WordPress的「應用程式名稱」（應為網址上的子目錄），如果你想要打上網址就直接連到網站，那就空白吧。 ![03](/post_image/2018/06/03-1.png) 這樣設定一輪之後，WordPress就都好了。

### 解決安裝後 Remote Web Access 的403錯誤

![04](/post_image/2018/06/04.png) 安裝完WordPress之後，跑回去Remote Web Access一看，怎麼會403錯誤？ 後來查看Error訊息才發現URL rewrite把後面的字串傳給index.php了 :<。 要解決這個問題，只能把URL rewrite好好設定一遍。 ![05](/post_image/2018/06/05.png) ![06](/post_image/2018/06/06.png) 找到在IIS管理員中的URL rewrite（請注意是在Default Web Site站台下點選），之後新增一個空白的輸入規則。 名稱可以隨便，比對URL中的樣式打「remote」且記得忽略大小寫，條件兩個－remote既不是檔案也不是目錄（他是IIS的應用程式），動作選擇「無」，最後記得勾選「停止處理後續規則」以避免此規則處理完後又處理後面的規則。 完整設定起來大概是這樣： ![07](/post_image/2018/06/07.png) 之後請不要忘記把新設定好的規則上移到最上面，他必須要優先處理。 ![08](/post_image/2018/06/08.png) _這樣Remote Web Access + WordPress的工作就大功告成，enjoy!_