[English](README.md) | 繁體中文

# 📊 Group6 專題：什麼因素造就一部熱門電視劇？

本專案分析 IMDb、Netflix、TMDb 三個資料來源，探討究竟是哪些特徵讓一部影集成為「熱門作品」，並最終建立一個可為影集打分排名的 **Hit Score** 模型。

Notebook：[netflix-writer_group6.ipynb](netflix-writer_group6.ipynb)

## 組員

- SUN
- Janice
- Elvis
- Jerry

## 使用的資料

- `netflix/data/imdb.titles.composite.csv`
- `netflix/data/netflix.titles.composite.csv`
- `netflix/data/tmdb.titles.v3.csv`

## 分析流程

1. **讀取並檢查資料**：讀入三份 CSV 後，先印出各自的 `shape` 與 `head()`，確認欄位內容與筆數是否符合預期，再進行後續處理。
2. **資料合併**：以片名為鍵，將 IMDb（類型、片長、卡司、編劇）與 TMDb（季數、集數、語言、電視網）的欄位合併進 Netflix 資料集，並檢查各來源的匹配覆蓋率。
3. **定義「熱門」標籤（`is_hit`）**：先計算三個候選訊號（`netflix_viewing_hours`、`imdb_numVotes`、`tmdb_popularity`）之間的 Spearman 相關係數，確認彼此只是弱相關——值得合併使用而非互相重複。接著對三個指標取 75 分位數，若至少 2 項達標即視為熱門，並用不同分位數 / 門檻組合驗證此設計的合理性。
4. **特徵工程**：
   - `binge_velocity`：上榜期間平均每週觀看時數
   - `imdb_rating_shrunk`：以貝氏方法將評分依票數向平均值收縮，避免小樣本評分失真
   - `audience_alignment_gap`：TMDb 與 IMDb 評分的分歧程度
   - `imdb_buzz_log`：對投票數做 log1p 轉換，壓縮極端值
5. **關聯性檢查**：計算各特徵與 `is_hit` 的相關係數，確認哪些特徵最具預測力。
6. **Hit Score 模型**：將每個特徵正規化到 0–1 後，依相關性強弱加權加總，得出綜合排名分數，找出最符合「完美影集」條件的作品。
7. **高分作品的類型、語言與片長分析**：取出 `hit_score` 前 25% 的作品，分析其 `imdb_genres`（拆成單一類型標籤並統計次數）、`tmdb_original_language`（語言分佈）與 `imdb_runtimeMinutes`（片長的統計摘要）。

## 執行方式

```console
make python-init
make run
jupyter notebook netflix-writer_group6.ipynb
```

`make run` 會先產生上述三個 CSV 檔案，之後即可依序執行 notebook 中的儲存格。
