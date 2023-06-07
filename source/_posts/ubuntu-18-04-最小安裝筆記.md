---
title: Ubuntu 18.04 最小安裝筆記
tags:
  - Linux
  - Ubuntu
categories:
  - - 系統維運
date: 2020-04-19 14:52:00
---

## 注意事項

1.  最小安裝**需要使用到網路**，你有有線網路用有線，無線網路用無線，沒有你就掰掰。  
    因為要連網，所以先確定好網卡驅動是否能夠在 mini 安裝環境下使用。
    
2.  最小安裝難免在後面安裝其他程式的時候會有套件缺失的問題（最小安裝的套件會少很多），  
    不能接受就迴避ㄅ。
    
3.  如果 Ubuntu 的最小安裝你都無法接受，**那請你千萬別碰Arch Linux**，因為它超麻煩
    

## 為什麼要做 Ubuntu 最小安裝？

自己的理由 :

*   反正我很閒 / 卑鄙源之助
*   練功
*   Ubuntu 沒精簡真的太肥，選了最小安裝也是肥，雖然我自己最小安裝裝起來好像也很肥就是了
*   爽感（無誤）
*   我不想用 tasksel ...

## 開始

首先去下載[映像檔](https://help.ubuntu.com/community/Installation/MinimalCD)，然後想辦法用到 USB 裡面去

*   Windows 環境下推薦使用[Rufus](https://rufus.ie/)
*   就算你是 UEFI 電腦也別肖想全部 copy 過去就沒事了，一樣不能開機...  
    （因為 bootx64.efi 還要解開 img 檔案才有）

然後就是開機安裝了，這部份應該不用教。(幹勒自己寫的筆記教屁喔)

但是因為 18.04 之後的 mini 安裝環境的中文環境有 bug ，所以**安裝要選擇英文安裝**

*   這部份的 bug 你也可以在 Ubuntu Server 安裝的時候體驗到，畢竟安裝環境差不多

對了，畢竟是最小安裝，所以出現軟體選擇的時候**請跳過**

反正裝好了看到文字界面你就成功了

## 安裝 Gnome

會選擇 Gnome 當作桌面只是因為它在我這台電腦（Acer Aspire 3750G）最安定最順，而且我也不排斥。

不過說起來 Gnome 其實很肥...。不管了裝就對了，穩比較重要（？

下以下指令就可以裝上去了

```bash
sudo apt install gnome-session gdm3
```

*   給只是想要 Gnome Vanilla 環境而查到這篇文章的人 :
    *   裝 `gnome-session` 就好，然後登出後去切換
*   裝好之後別忘了安裝 `gnome-terminal` ，不然進入桌面環境之後沒終端機可以用
*   如果你打算在圖形界面下管理檔案...
    *   安裝檔案管理軟體
        
        *   個人是用用跟 Gnome 搭配的 Nautilus
        *   安裝：
        
        ```bash
        sudo apt install nautilus
        ```
        
    *   選擇別裝圖形軟體，用 ls、cp、mv、rm 也是個好選擇啊
*   如果你有打算開機的時候不要自動進入桌面的話...
    *   安裝 `xinit` 後你就可以用 `startx` 手動進入桌面
*   如果你不喜歡 gdm 預設的紫色背景...
    *   下 `sudo update-alternatives --config gdm3.css` 然後選擇  
        `/usr/share/gnome-shell/theme/gnome-shell.css` 那一項  
        這樣登出之後應該就是 Gnome 預設的灰色了

## 搞定中文字型支援 & 安裝 fcitx

### 中文字型支援

打開 Language Support ，點「Install / Remove Languages...」  
Chinese (tradition) 勾起來~

![](/post_image/2020/ubuntu-mini/gnome-chinese-tradition.png)

Apply 之後就完成了，如果有缺少檔案就麻煩他順便裝一裝。

### 安裝 fcitx

下指令安裝

```bash
sudo apt install fcitx fcitx-chewing
```

裝好之後去 Fcitx Configuration 新增輸入法

![](/post_image/2020/ubuntu-mini/fcitx-chewing-setting.png)

記得把「Only Show Current Language」取消勾選，  
如果你的 Ubuntu 語系不是中文的話，不然會看不到。

最後記得去 Language Support 把 「Keyboard input method system:」改成 fcitx  
然後**登出再登入**

![](/post_image/2020/ubuntu-mini/languagesupport-fcitx-selected.png)

## 使用 Ubuntu 的懶人方法安裝 Nvidia driver

這就是我為什麼會選用 Ubuntu 當作我學習 Linux 的主力系統原因，  
就是 Nvidia driver 太好裝了。只要點個幾個 GUI 界面就裝好，  
還不需要去做額外設定就可以應付最基本的日常使用。

當然如果你有要用到 CUDA 等 Nvidia 附帶的運算功能，  
那這個段落就毫無參考價值了。

### 安裝 ubuntu-drivers

其實精簡安裝沒有附帶這好用的工具可以用，那就自己裝！

```bash
suod apt install ubuntu-drivers-common
```

*   然後這個好像不是官方附帶的那個...？
    *   沒事,照用（欸

![](/post_image/2020/ubuntu-mini/ubuntu-drivers.png)

先用 `ubuntu-drivers list` 確認有沒有找到你的 Nvidia 裝置，沒有就代表這方法行不通  
如果有找到的話， `sudo ubuntu-drivers install` 輸入下去讓他幫你跑完。  
跑完之後再重開機，你的 Nvidia 顯示卡就可以使用了。

## zsh 安裝與 tty 辛酸史

這我打算開一篇新文章專門講這些事，敬請期待～  
（因為受傷害太深所以先不要馬上寫）

## 總結

老實說 Ubuntu 本身就不怎麼精簡了，所以這樣的裝法可能無法精簡太多。  
但至少你有比較大的彈性安裝你自己想要的套件，  
而不是被 `ubuntu-desktop` 強塞一堆垃圾。

## 參考資料

[Getting Real GNOME Back in Ubuntu 18.04 \[Quick Tip\]](https://itsfoss.com/vanilla-gnome-ubuntu/) by It's FOSS  
[如何使用「ubuntu-drivers」這個指令，來輔助安裝驅動程式套件](http://samwhelp.github.io/book-ubuntu-qna/read/case/driver/install-driver-package/ubuntu-drivers) by Ubuntu 問答集