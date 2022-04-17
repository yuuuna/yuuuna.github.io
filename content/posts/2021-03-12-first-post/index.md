---
title: "Hugo 紀錄"
date: 2021-03-12
draft: false
categories:
    - Hugo
tags:
    - Hugo
    - 紀錄
---

首發的第一篇文章，來記錄一下使用 Hugo 來建立個人的 Blog。

簡單描述一下需要安裝、主題選擇、以及啟動方式。

<!--more-->

# 初次使用

## 本機安裝 Hugo

```sh
$ brew install hugo
```

## 選擇主題

於 [Hugo Theme](https://themes.gohugo.io/) 挑選一個自己喜歡的主題，記得注意 License 。

## 建置專案

```sh
$ hugo new site <site-name>
```

### 主題有提供教學

然後可以依照選擇主題裡的教學方式繼續完成建置(如果他有提供教學的話)。

### 主題未提供

使用 Git Submodule 至主題的 Git 將 Theme 下載下來，放進專案的 Theme 資料夾裡，以及確認 `config.toml` 或 `config.yaml` 的設定(當然主題有提供最好...)。

```sh
## 將 Theme 連結資料夾，可以使用 Submodule (以我當前的主題為例)
git submodule add https://github.com/kakawait/hugo-tranquilpeak-theme.git themes/hugo-tranquilpeak-theme

## 複製 config.toml 的設定
cp themes/hugo-tranquilpeak-theme/exampleSite/config.toml config.toml
```

## 啟動專案

```sh
$ hugo serve
```

起動成功直接打開 `http://localhost:1313/` 即可看到頁面囉

## 新增文章

預設新增就會新增在 `content` 資料夾下，在指定後面的資料夾名稱以及檔案名稱即可。

```sh
$ hugo new posts/<folder-name>/index.md

## 希望新增為 content/posts/how-to-use-css.md
$ hugo new posts/how-to-use-css.md

## 希望新增為 content/posts/how-to-use-css/index.md
$ hugo new posts/how-to-use-css/index.md
```

## 部署設定

若有要部署至 Github，須將 `config.toml` 或 `config.yaml` 的 `baseurl` 調整為自己的網址

```sh
baseurl: https://blog.yuuna.dev
```

# 重新 Clone 專案

重新 Clone 專案時，需要重新下載 theme 內的 git 檔案，  
需要使用 git submodule 初始化 & 更新，即可完成 theme 的下載

<!-- ### 重新 Clone Blog -->
```sh
git submodule init
git submodule update
```
