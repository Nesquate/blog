---
title: iPad 鎖定螢幕後 Wi-Fi 會自動斷線的鬼故事
tags:
  - iPad
  - Wi-Fi
categories:
  - - 系統維運
date: 2023-04-19 19:15:55
---

自從 3 月買了 iPad 之後，就一直發生很奇怪的事情－Wi-Fi 在鎖定螢幕之後自動斷線。 

起初以為是 iPadOS 本身的機制，我也以為這是 iPad 省電神話的其中一部份。但後來想想不太對，畢竟隔壁棚的 iPhone 沒有聽過有人有類似的問題。 

而且更奇怪的事情是，iPad 在學校連上學校的 Wi-Fi 竟然沒有發生鎖定螢幕後斷線的問題，於是決定去找看看是不是 RouterOS 的問題。 

於是找到了 [MikroTik 官方論壇的討論串](https://forum.mikrotik.com/viewtopic.php?t=111359)，有人提出了很有意思的回答：

> What's your DHCP lease time? Maybe it's too low? If I recall correctly iOS doesn't like very low DHCP lease times. You could raise the "Group Key Update" of your Security Profile to something like 1h. This should only be relevant to WPA2-EAP (as far as I know), but might be worth a try.

跟我發生類似事情的事主於是回答：

> I haven't changed anything in lease time for ages. It is set up to 00:10:00. As it comes to "Group Key Update" I can't see anything like that at CAPsMAN... When APs were working separately it was set up to 00:05:00.

先不管 CAPsMAN，我沒有搞這個，且我也沒有搞 WPA2-EAP，那問題就只剩下一個－DHCP lease time。 

於是我去確定了一下 DHCP Server lease time，哇怎麼只有 10 分鐘......。 

後來改成 23 小時，讓 iPad 放一個晚上，隔天醒來看，沒有自動斷線，看來問題就出在這裡。 

我開始好奇 Apple 到底有沒有對 iPad 的 DHCP lease time 設定下限或上限，於是找到了 [Recommended settings for Wi-Fi routers and access points](https://support.apple.com/en-md/HT202068) 一文，並指出：

> DHCP lease time Set to 8 hours for home or office networks; 1 hour for hotspots or guest networks DHCP lease time is the length of time that an IP address assigned to a device is reserved for that device. Wi-Fi routers usually have a limited number of IP addresses that they can assign to devices on the network. If that number is depleted, the router can't assign IP addresses to new devices, preventing those devices from communicating with other devices on the network and internet. Reducing DHCP lease time allows the router to more quickly reclaim and reassign old IP addresses that are no longer being used.

看來 Lease time 只要設定 8 小時就好了，於是又改成 8 小時，目前沒有發生斷線狀況，不過還要再觀察看看......。