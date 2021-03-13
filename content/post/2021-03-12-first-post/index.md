---
author: Yuuna Chang
title: Hugo 紀錄
date: 2021-03-12
description: 值得紀念的第一篇文章，所以紀錄一下 Hugo 建置過程
categories:
    - 紀錄
    - Hugo
tags:
    - Hugo
---

值得紀念的第一篇文章，所以紀錄一下方法

### 本機安裝 Hugo

```
$ brew install hugo
```

### 選擇主題

於 [Theme](https://themes.gohugo.io/) 挑選一個自己喜歡的主題，記得注意 License 。

### 建置專案

```
$ hugo new site <site-name>
```

#### 主題有提供教學

然後可以依照選擇主題裡的教學方式繼續完成建置(如果他有提供教學的話)。

#### 主題未提供

至主題的 Git 將 Theme 下載下來，放進專案的 Theme 資料夾裡，以及確認 `config.toml` 或 `config.yaml` 的設定(當然主題有提供最好...)。

### 啟動專案

```
$ hugo serve
```

### 新增文章

我的專案是以資料夾在使用 index.md，也有其他主題可能不需要墊一層資料夾，直接以 .md 檔案，ex. how-to-use-css.md。

```
$ hugo new post/<folder-name>/index.md
```
