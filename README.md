English | [繁體中文](README.zh-TW.md)

# 📊 Group6 Project: What Makes a TV Series Popular?

This project analyzes three data sources — IMDb, Netflix, and TMDb — to explore which features make a TV series a "hit," and ultimately builds a **Hit Score** model that ranks series based on those features.

Notebook: [netflix-writer_group6.ipynb](netflix-writer_group6.ipynb)

## Team Members

- SUN
- Janice
- Elvis
- Jerry

## Data Used

- `netflix/data/imdb.titles.composite.csv`
- `netflix/data/netflix.titles.composite.csv`
- `netflix/data/tmdb.titles.v3.csv`

## Analysis Pipeline

1. **Load and inspect the data**: After loading the three CSV files, print each one's `shape` and `head()` to confirm the columns and row counts look right before further processing.
2. **Merge the data**: Using the title as the join key, merge IMDb columns (genres, runtime, cast, directors, writers) and TMDb columns (number of seasons, number of episodes, language, networks) into the Netflix dataset, then check the match coverage from each source.
3. **Define the "hit" label (`is_hit`)**: First check the Spearman correlation between the three candidate signals (`netflix_viewing_hours`, `imdb_numVotes`, `tmdb_popularity`) to confirm they're only loosely correlated — i.e. worth combining rather than redundant. Then label a title a hit if it clears the 75th-percentile threshold on at least 2 of the 3 metrics. Different quantile/threshold combinations are tested to validate this design.
4. **Feature engineering**:
   - `binge_velocity`: average viewing hours per week while on the list
   - `imdb_rating_shrunk`: Bayesian-shrunk rating that pulls low-vote-count ratings toward the dataset mean, avoiding distortion from small samples
   - `audience_alignment_gap`: disagreement between TMDb and IMDb ratings
   - `imdb_buzz_log`: log1p transform of vote count to compress extreme values
5. **Correlation check**: compute each feature's correlation with `is_hit` to see which ones are most predictive.
6. **Hit Score model**: normalize each feature to 0–1, weight them by correlation strength, and sum them into a single ranking score to find the titles closest to a "perfect show" profile.
7. **Genre, language, and runtime of top performers**: take the titles in the top 25% by `hit_score` and break down their `imdb_genres` (split into individual tags and counted), `tmdb_original_language`, and `imdb_runtimeMinutes` (summary statistics).

## How to Run

```console
make python-init
make run
jupyter notebook netflix-writer_group6.ipynb
```

`make run` generates the three CSV files above; after that you can run the notebook's cells in order.

## AI Usage Disclosure

This project was developed with assistance from Claude Code (Anthropic). AI assistance was used for drafting/refactoring notebook code, translating the notebook and README into Traditional Chinese, and writing this documentation. The analysis design and decisions (e.g. the hit-label thresholds, feature choices, and hit_score weights) were reviewed and confirmed by the team.
