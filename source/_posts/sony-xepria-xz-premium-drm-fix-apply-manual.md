---
title: Sony Xperia XZ Premium DRM FIX手動套用方法
tags:
  - DRM FIX
  - XperiFIX
  - XZP
categories:
  - - 系統維運
date: 2018-04-15 18:44:01
---

XZP的DRM FIX在[XDA](https://forum.xda-developers.com/xz-premium/development/hack-mod-sony-xperia-xz-premium-twrp-t3695171)已經推出許久，最近的DRM FIX v2.x版本甚至把Kernel部分獨立成一個刷機包來安裝。 獨立刷機包的好處在於往後有新官方Kernel釋出時，只要放置適當的檔案和刷入刷機包即可套用DRM FIX。以前還要等sToRm//打包Kernel，一來相當費時，其次是等待期間的新系統舊Kernel之組合會使得安全性更新的修補不完全。 目前的v2.2的一鍵DRM FIX只支援到47.1.A.12.75，但原廠系統已經來到47.1.A.12.119(TW、HK等)/47.1.A.12.145(大部分)，程式在手機fastboot偵測下去果然是無法支援的，不過我們可以利用獨立刷機包的特性來完成手動DRM FIX。
<!-- more -->
步驟：

1.  至[XperiFIX](https://www.xperifix.com/download/xperifix-latest/)下載v1.4和v2.2，**然後先安裝v1.4再安裝v2.2**（反過來裝好像也可以？沒試過）
2.  複製檔案到手機（TWRP下的MTP有點問題，不要在TWRP下複製，這部分是Myself5的鍋）
    1.  XperiFIXDATAXZPG814247.1.A.8.49底下的drmpatch.zip （非台版就在XperiFIXDATAXZPG814147.1.A.8.49底下）
    2.  XperiFIXDATADRM底下的xperifix.zip
3.  用fastboot刷TWRP（指令：fastboot flash recovery xxx.img） 在XperiFIXDATATWRP底下有，**請注意是刷xzp-twrp.img而非 xzp-twrptemp.img** **請先裝好fastboot驅動和fastboot再來**
4.  在TWRP下刷入drmpatch.zip和xperifix.zip（無先後順序問題）
5.  重開機，enjoy!

但是既然都做成刷機包了，難道不能把drmfix.so和drmfuck.so一起包進去嘛？非得要去載舊版才行？