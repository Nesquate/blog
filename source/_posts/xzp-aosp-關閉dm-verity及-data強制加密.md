---
title: XZP (AOSP) 關閉dm-verity及/data強制加密
tags:
  - /data
  - disable
  - dm-verity
  - forceencryption
  - sony
  - XZP
categories:
  - - 系統維運
date: 2018-06-16 13:46:24
---

從2018/4的AOSP編譯開始，Sony官方開始強制使用dm-verity和/data加密。 而這樣會發生什麼問題呢？使用dm-verity會增加安裝Magisk的難度，畢竟這機制是一開機就會驗證是否有修改系統。不裝Magisk就無法root、或是安裝如Xposed等systemless模組。 /data強制加密將會導致TWRP無法使用（閃退），經過XDA大神及本人的實測，關閉/data強制加密後TWRP就可以使用了。這應該是TWRP的一個bug，只能等待修正。
<!-- more -->
所以這篇筆記文就誕生了。

## 關閉dm-verity

關閉dm-verity有兩個部份要動：Kernel和Platform 同步完原始碼之後，切換到目錄至kernel/sony/msm/arch/arm64/configs下，找到「aosp\_yoshino\_maple\_defconfig」並且打開。 以dm-verity搜尋應該會找到這幾個： \[code\] CONFIG\_DM\_VERITY=y CONFIG\_DM\_VERITY\_FEC=y CONFIG\_DM\_VERITY\_AVB=y CONFIG\_DM\_ANDROID\_VERITY=y \[/code\] **請把他們全部調成「n」並且存檔**，Kernel的部份就完成了。 接著是Platform的部份，將目錄切換到device/sony/yoshino底下，開啟platform.mk 移動到文件的最後一行，應該會看到： \[code\]

# setup dm-verity configs.

PRODUCT\_SYSTEM\_VERITY\_PARTITION := /dev/block/platform/soc/1da4000.ufshc/by-name/system $(call inherit-product, build/target/product/verity.mk) \[/code\] **把後兩行以「#」為開頭註解掉並且存檔**，之後重新編譯就完成關閉dm-verity了。

## 關閉/data強制加密

需要修改Partition map。 首先目錄切換到device/sony/yoshino/rootdir/vendor/etc下，找到fstab.yoshino並打開。 找到/dev/block/bootdevice/by-name/userdata這行，後面有一串東西： \[code\] noatime,nosuid,nodev,barrier=1,data=ordered,noauto\_da\_alloc,errors=panic wait,check,formattable,fileencryption=ice,quota \[/code\] 重點在fileencryption=ice，有了此行將會導致首次開機強制加密。 **要關閉的話，把它換成encryptable=footer並且存檔**，然後重新編譯。

## repo sync衝突

遇到代碼衝突而無法同步的話，因為小的還不太懂git指令，我的作法是把修改的檔案砍掉再執行repo sync，然後再重新修改一次。