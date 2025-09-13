---
title: "在 Pixel 手機安裝 SUICA 個人經驗紀錄"
date: 2025-09-13T15:20:00+08:00
slug: ""
description: ""
keywords: []
draft: false
tags: [手機]
categories: [札記]
math: true
toc: false
katex: true
keywords:
  - Pixel
  - SUICA
  - Felica
description: 在 Pixel 手機安裝 SUICA 個人經驗紀錄
---

先說明一下，我在這裡使用的手機分別是 Pixel 6 以及 Pixel 10，另外安裝的步驟均參考[這篇文章](https://www.mobile01.com/topicdetail.php?f=565&t=7164410)

Android 使用者可能會很羨慕 iPhone 使用者到日本可以直接刷交通卡，而我們還是需要掏出實體卡，在旅行中拖著大包小包還要從包包挖出卡片稍嫌麻煩。這是因為日本的交通卡採用的是 felica 技術，通常只有日本版的手機才有辦法啟用。而 iPhone 跟 Pixel 是目前唯二在全球發行的手機都有相關硬體設備，但是 iPhone 並沒有對此設下太多限制，Pixel 則是以韌體版本判斷機種是否為日本版進行啟用。總之如果想要在台灣能夠用悠遊付嗶乘車，在日本同時能刷 suica 的話就是只有 Pixel 做得到。

能夠讓非日版 Pixel 啟用 felica 的程序有兩種，這兩種方法都無可避免需要重置手機，因此都需要預先備份資料。

1. 修改韌體版本為日版，程序較為麻煩，且拍照時快門聲會關不掉（經個人實測，用台版機使用日本 sim 卡聲音也會關不掉），不過也可以再刷回解決。
2. root 後使用模組騙 app 是日版進行設定再刷回。

本文使用第二個方法。

安裝前的狀況是未申請過任何 SUICA 等的帳戶，另外也是剛將手機資料從 Pixel 6 換到 Pixel 10，（當初應該要先處理完再做資料移轉，**強烈建議未來拿到新機都先做這些程序再做資料移轉**），所以會有一些處理上比較麻煩的部分（特別是銀行、信用卡之類的）。

很抱歉我在嘗試的過程中並沒有進行截圖，所以不少地方都沒有圖片請見諒。

## 先置步驟

因為我們會需要能夠載幾個只能夠使用日本 Google 帳戶安裝的 App，所以需要開一個日本 Google 帳戶（或是拿一個沒有綁過信用卡消費的帳戶）。依照一般程序辦完帳戶（不要填電話號碼或住址，不確定填了之後會發生什麼事，從手機辦帳戶可以跳過這幾步）

找個日本 VPN 連(Play 商店有一些免費的)，請注意不要連到 IP 219 開頭的，地區會被判定成美國。並下載 Google 意見回饋獎勵，使用剛剛的

## Pixel 6

### 手機上

我是先在 Pixel 6 進行測試，首先先打開開發人員選項（關於手機>版本號碼按六下），之後開啟 OEM 解鎖以及 USB 偵錯。之後關機。

關機完同時按電源鍵和下音量鍵進入 bootloader，這時候有 lock(unlockable) 就可以繼續了。

### 電腦上

接著是電腦這裡，請[安裝 SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools?hl=zh-tw)，解壓縮，並把 fastboot 所在的路徑加入環境變數（Windows: 去找編輯環境變數，macOS/Linux: Terminal 輸入`export PATH==$PATH:<路徑>`並重啟 Terminal）。

接著用線連上手機，執行 `fastboot flashing unlock` 解鎖，此時手機如果再開 bootloader 就會寫 unlock 了。之後任何 flash 指令都是在開啟 bootloader 的狀況下執行。

**不管怎樣 unlock 一定會清資料**，請務必備份好需要的資料。特別是各大網銀（尤其是公股銀行通常需要裝置綁定碼，沒有的話都要跑臨櫃）、Line 還有我們公司在用的 Duo 務必先在另一台手機設定好。

這個時候到[這裡](https://developers.google.com/android/images?hl=zh-tw)找到對應版本的原廠韌體。下載在電腦解壓縮之後裡面會還有另外一層 zip。裡面有 `init_boot.img` 或 `boot.img`。（註：建議使用前者，然而 Pixel 6 並沒有提供，Pixel 10 有提供，其他版本並沒有另外測試）請想辦法丟到手機上（Quick Share 好用）

### 手機上

回到手機，請安裝 [Magisk Manager](https://magiskmanager.com/)，然後進去後點 Magisk 旁邊的安裝，選擇剛剛傳過來的 img 進行修復。完成後他應會顯示一行

``` Output file is written to /storage/emulated/0/...../magiskxxxxxxxxxxx.img```

請找到這個檔案，再把它傳回電腦。

### 電腦上

回到電腦，在剛剛的那個 img 所在的目錄執行 `fastboot flash init_boot magiskxxxxxxxxxxx.img` 就會成功 root 了

### 手機上

之後下載 [unlock-felica-pixel](https://github.com/jjyao88/unlock-felica-pixel) 這個模組，直接載那個 zip 檔在手機，到 magisk 按右下角的 module 並直接把 zip 丟進去。

然後請到 Play 商店用日版 Google 帳號下載這些軟體（四個都要有才能使用）：
- おサイフケータイ アプリ 
- おサイフケータイ 設定アプリ
- モバイルFeliCaクライアント
- Google Play services for payments

接著打開 Google 錢包就會看到以下畫面

![](wallet1.png)

這裡共有五種貨幣可供選擇，分別是 Suica、nanaco、PASMO、Edy、WAON。其中通路最廣能在最多地方乘車的是 Suica 跟 PASMO（因為這兩間都在交通系ICカード全国相互利用サービス，彼此間通路是互通的），然而 PASMO 基本上鎖國外信用卡，這裡基本上只能用 SUICA。不過 SUICA 有不少信用卡儲值被鎖卡的先例，要特別注意。ICOCA 雖然較無這種情況，但是並沒有被整合進 Google 錢包，需要另外去おサイフケータイ設定，所以這邊還是建議使用 SUICA。

接著進入按新增，會要求你填一些個人資料，與上面參考文章不同的是我這邊是直接在 Google 錢包裡面填資料。這邊注意郵遞區號地址跟電話需要是日本的，但他不會另外驗證。然後請記得你填的資料，到時候被鎖卡要拿這些資料去申請解卡。

然後就順利將這張卡加入了，之後可以不解鎖刷閘門。這邊也可以直接在 Google 錢包裡面加值（海外信用卡似乎只能使用 JCB，我這邊使用的是富邦J卡，特別注意台灣的 JCB 卡都不能設定感應支付，但是仍可以用來支付加值款項），實測成功。

之後就是把手機 unroot 並 lock 回去。有人會在這裡維持 root，但是我這邊的建議（或著說我個人的需求）是不要。首先是資料會不安全，另外想把資料從另一台手機備份回來很多應用程式資料會不給複製。還有在 root 下是沒辦法感應支付信用卡的，各大網路銀行 app 也會擋 root。雖然市面上有套件是可以偽裝不 root 再進入這些 app，但是與其在那邊玩 root 躲貓貓不如直接鎖回去。不過鎖回去會有的缺點就是おサイフケータイ無法直接打開（如果只是偶爾去日本玩的話這部分倒還好，只要有一張能刷的 Suica 就很夠了）。

### 電腦上

這邊請回去使用刷機，也就是上面載下來的韌體zip檔案會有個 flash-all.sh(for macOS or Linux) 或 flash-all.bat(for Windows)。執行它，需要花一點時間，這裡手機會再重置一次。之後再 `fastboot flashing lock` 鎖回去（兩個動作皆要在手機是 bootloader 的時候做）。

### 手機上

這邊可以開始轉移資料。記得一樣把剛剛那四個 app 用日版帳號載下來，但不需要打開。直接進 Google 錢包一樣會有電子貨幣的選項，之後再點 Suica，這時應該會有「發現既有的 Suica卡 卡」，點一下就能拿回這張卡並且正常使用手機了。

不過這邊特別注意，開啟おサイフケータイ之後是無法使用墨爾本 myki 以及大多倫多 PRESTO 這兩張交通卡（目前沒有測試把おサイフケータイ刪掉會發生什麼事）。而對八達通的影響仍不確定（都是 Felica 的話可能影響不大），另外也不確定未來如果一卡通推廣到 Pixel 上面會不會與 Suica 衝突。目前悠遊付嗶乘車仍能正常使用

## Pixel 10

當你興高采烈地在舊手機成功做完，然後在新手機上面裝了那四個 App 並點開 Google 錢包發現有 Suica 可以按欸，結果

![](wallet2.png)

![](wallet3.png)

因為這支手機是沒有啟用過 Felica 的，任何非日本的 Pixel 手機都還是需要啟用晶片，也就是上面的流程再跑一遍。這邊建議在轉移手機資料前先啟用完再進行資料轉移，不然就像我一樣需要從舊手機轉到新手機又轉回舊手機又轉到新手機，非常愚蠢。

再跑一遍的時候點選新增 Suica 卡時，會有從其他裝置轉移 SUICA 卡的選項，反正順著操作就對了（下圖是舊裝置的畫面）

![](wallet4.png)

重新刷回鎖上，依循一樣的方法設定就可以了。

![](wallet5.png)