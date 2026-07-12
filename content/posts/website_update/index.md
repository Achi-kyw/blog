---
title: "網頁更新筆記"
date: 2026-07-12T19:55:00+08:00
slug: ""
draft: false
tags: [網站]
categories: [筆記]
math: true
toc: false
katex: true
keywords:
  - Astro
  - TypeScript
  - Material 3
  - Waline
description: 個人網頁大風吹
---

能點到這裡代表你應該有看到我的個人網頁的變化。總之現在這個 `/blog` 下面才是我的網誌所在地。原本的 `achi-kyw.github.io` 已經是我的個人頁面了。動機純粹只是我想要建一個「介紹自己」的網頁，加上我最近有 Claude Code 可以用。

# 原部落格

對於這個舊網站我做了一些變化：

- 圖片改為 webp 要看大圖再點進去看，可以加快載入速度
- Tags 和 Categories 分類明確化
- Deploy 步驟簡化，詳細來說是取消 `/public` 下的 repo 追蹤，完全本地建置。
- 留言板從 Disqus 改為 Waline，為此有另外開了一個 MangoDB Cluster 拿來存留言。（具體換的原因只是我的 Disqus 管理畫面混亂我想放棄掉）
- About 現在會直接連回我的個人頁
- 一些舊網頁的連結（沒有加 `/blog` 的那些會被自動轉址）

# 個人網頁

新網頁的建置是採用 Astro + TypeScript。因為我沒有什麼美學概念，決定參考 [Material 3](https://m3.material.io/) 來設計。以下是 Claude Code 協助整理的設計元件：

## 色彩系統（Color System）

配色採用 [M3 baseline scheme](https://m3.material.io/styles/color/system/overview) 的完整色彩 token，支援亮 / 暗雙主題：

| Token | 本站用途 |
| --- | --- |
| `primary` 系列（`on-primary`、`primary-container` 等） | Filled 按鈕、連結、時間軸節點、hover 凸顯 |
| `secondary-container` / `on-secondary-container` | Tonal 按鈕、導覽列 active 指示膠囊、圖示底色 |
| `surface` 與 `surface-container` 四階（low → highest） | M3 的色調高度 （tonal elevation） 系統：頁面底色、導覽列、抽屜、卡片，層級越高顏色越「浮」 |
| `on-surface` / `on-surface-variant` | 主要 / 次要文字 |
| `outline` / `outline-variant` | Outlined 按鈕邊框、chip 邊框、時間軸的線 |

互動狀態依循 [interaction states](https://m3.material.io/foundations/interaction/states/overview) 規範，以 **state layer** 疊色呈現：hover 疊 8%、按壓疊 12% 的 on-color。

主題偵測順序：使用者手動選擇（存於 `localStorage`）→ 系統 `prefers-color-scheme`。切換時 `<meta name="theme-color">` 與原生控件（`color-scheme`）一併跟隨。

## 字體排印（Typography）

對應 [M3 type scale](https://m3.material.io/styles/typography/type-scale-tokens)：

| M3 層級 | 本站對應 |
| --- | --- |
| Display(Expressive 加粗風格) | 各頁主標題：隨視窗縮放(最大 6rem)、semi-bold、緊縮字距 |
| Headline | 區塊標題(h2) |
| Title | 卡片與時間軸條目標題 |
| Body large / medium | 內文與卡片說明 |
| Label large / medium | 按鈕文字、導覽標籤、日期 |

字族為 `Roboto` + `Noto Sans TC`（M3 標準字體與繁中對應）。

## 元件（Components）

- **[Navigation rail](https://m3.material.io/components/navigation-rail)**（桌面）：左側直排導覽，active 項目有膠囊形指示器
- **[Top app bar](https://m3.material.io/components/top-app-bar)** + **[Navigation drawer](https://m3.material.io/components/navigation-drawer)**（行動版）：左上漢堡按鈕展開 modal drawer，含 scrim 遮罩與圓角面板
- **[Buttons](https://m3.material.io/components/buttons)**:filled / tonal / outlined 三個層級；標準 40px 與 Expressive medium（56px）兩種尺寸；支援前置圖示
- **Connected button group**（M3 Expressive，首頁）：按鈕以 2px 間距相連，外側角全圓、內側角 8px；行動版轉為 2×2 相連網格
- **Icon buttons**：主題切換（outlined icon button）、漢堡選單、Home
- **[Cards](https://m3.material.io/components/cards)**：作品與聯絡資訊採 filled card（12px 圓角）
- **[Chips](https://m3.material.io/components/chips)**：技術標籤與技能清單採 assist chip 樣式
- **圖示**：[Material Symbols](https://fonts.google.com/icons)（Apache 2.0），全站單一來源管理

## 動態與互動（Motion）

- **Ripple**：按壓時從實際點擊座標擴散的 pressed state layer，套用於全站可點擊元件
- **Shape morph**（M3 Expressive）：膠囊按鈕按住時圓角收緊、connected group 按住的段落變為完整膠囊，放開以彈簧曲線回彈
- **Spring motion**：形狀與位移動畫使用近似 M3 Expressive spatial spring 的帶回彈曲線
- **Elevation**：卡片 hover 浮起（elevation 陰影）、按壓時壓平並微縮
- **雙向連動 hover**：時間軸條目上，游標在圖示或內容任一側，另一側同步反應
- 主題切換按鈕為日出日落式的圖示位移動畫
- 完整支援 **`prefers-reduced-motion`**:偏好減少動態的使用者會得到無動畫版本（M3 無障礙規範）

## 版面 （Layout）

- 響應式導覽依 M3 [window size classes](https://m3.material.io/foundations/layout/applying-layout/window-size-classes)：桌面用 navigation rail、緊湊寬度（≤700px）改用 top app bar + modal drawer
- 內容欄最大 1120px 置中，24px gutter

# 結語

總之舊網頁會留著掛在 `/blog` 下面，遠期可能有一起搬進去使用新設計語言的可能性，但暫時不打算。未來這裡還是會不定期更新我的網誌。

