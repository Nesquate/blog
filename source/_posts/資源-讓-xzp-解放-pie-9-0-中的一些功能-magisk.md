---
title: 讓 XZP 解放(?) Pie 9.0 中的一些功能 (Magisk)
tags: []
categories:
  - - 系統維運
date: 2018-11-19 19:34:00
---

### 嗯？

大概2018年11月初吧，XZP正式收到Pie 9.0的更新。除了Android Pie的所有功能之外，Sony似乎也放棄了相機需要DRM的情況，於是已解鎖的XZP也就不需要DRM FIX Patcher了，可喜可賀，可喜可賀。

  

不過老實說，個人認為XZP的Pie 9.0好像還少了什麼東西......。

### 少掉的東西？

目前看來在XZP的Pie中似乎還是沒有：

1.  Night Light（夜間模式）
2.  如同Pixel 3的導覽列手勢
3.  VR相關選項
4.  Camera2 API（我想這大概是沒救了吧......）
5.  Digital Wellbeing（數位健康）

前三點如果要使用的話可以安裝Magisk模組解決（Camera2 API在Sony手機真的沒救了）。

  

至於Digital Wellbeing，這塊是Google的問題。Google目前僅限在Pixel/Android One機種中開放Digital Wellbeing的功能，雖然說可以自己下載Apk再透過Pixel Shortcuts建立捷徑來使用，不過計時限制、灰階畫面及排程功能無法使用（會閃退）。若要克服這個問題，就需要使用Magisk模組。

  

  

### Pix3lify

原本叫做「Pixel Experience」，到後面就改名了，害我差點以為這玩意消失了......。

  

這個Magisk模組裝下去之後，夜間模式、VR相關選項都會出現，位置在「設定→顯示」當中。夜間模式可以正常使用。

  

![](/post_image/2018/11/Screenshot_20181119-191522-169x300.png)

在「顯示」設定中的兩個選項

### Lawnstep

這模組可以在XZP實現Pixel 3的導覽列手勢，不過安裝此模組之前要先安裝[Lawnchair v2](https://www.apkmirror.com/apk/deletescape/lawnchair/)，注意是v2不是v1。Play商店上的是v1不是v2，**v2沒有在Play商店中上架。**

  

安裝好Lawnchair v2之後記得**先將它設為預設主畫面**後再安裝Lawnstep，這樣重新開機之後應該就會看到導覽列手勢了。

  

沒有設為預設或是不是用v2會發生什麼事？就跑不出來而已......。

  

Lawnstep的相關設定可以在Lawnchair v2的設定當中找到。

### Digital Wellbeing Enabler

這個模組沒有在Magisk中上架，[需要自己下載](https://androidfilehost.com/?a=show&w=files&flid=280229)。

  

進入上面的連結，找到Wellbeing開頭的檔案下載下來就對了（我是下載Wellbeing@PH1\_1.1.1.zip）

  

這模組原本是設計給Essential Phone使用，後來經過測試之後發現OnePlus也行。個人是秉持著不試白不試的心態測試，結果發現XZP一樣也可以使用。

  

雖然模組內已經有Digital Wellbeing的Apk，不過建議在安裝之前/之後[下載最新版](https://www.apkmirror.com/apk/google-inc/digital-wellbeing/)。

  

要進入數位健康的話，點開設定App就會看到「數位健康」的選項了。

![](/post_image/2018/11/Screenshot_20181119-184115-169x300.png)

計時功能正常使用

_如果要非Root的方法，可以參考[Dcard上的文章](https://www.dcard.tw/f/3c/p/230067811)。_

### 結論

透過Magisk的模組，算是補齊了一些原本在9.0應該要有的元素，而且不用手動修改系統文件，模組裝下去就可以直接使用，算是省下了不少時間。