# Zephyr Deal Database Construction - Target Trade Description

## Database Overview
This document describes the construction of a merger and acquisition (M&A) deal database extracted from Zephyr (Bureau van Dijk), focusing on deals with target trade descriptions for Natural Language Processing (NLP) analysis.

## Source Data Extraction

### Zephyr Search Strategy
![image](https://user-images.githubusercontent.com/xxx/xxx.png)
## Data Processing Pipeline

### 1. Initial Export Challenge

Due to Zephyr's export limitations (maximum rows per export), the data was downloaded in 5 batches:

| Batch | Deals Count |
|-------|-------------|
| Batch 1 | 11,235 |
| Batch 2 | 11,235 |
| Batch 3 | 11,235 |
| Batch 4 | 11,235 |
| Batch 5 | 10,358 |
| Total Unique Deals | 55,298 |

### 2. Why Observation Counts Differ

The raw CSV files contain more observations than unique deals because:

* One deal may have multiple acquirers → appears as multiple rows
* One deal may have multiple targets → appears as multiple rows
* Each unique combination (acquirer-target pair) generates a separate row in the export

* ### 3. Batch Processing Statistics
#### Target Overview Corpus

| Batch | CSV Observations | After Cleaning & Dedup | After Merge with Dates | Final (Year ≤ 2024) |
|-------|-----------------|------------------------|------------------------|---------------------|
| Batch 1 | 25,526 | ~842 | 9,158 | ~8,953 |
| Batch 2 | 20,465 | ~680 | 8,823 | ~8,640 |
| Batch 3 | 17,803 | ~580 | 7,806 | ~7,611 |
| Batch 4 | 16,201 | ~415 | 5,906 | ~5,766 |
| Batch 5 | 14,327 | ~237 | 3,846 | ~3,782 |
| **Total** | **94,322** | **~2,754** | **35,539** | **~34,752** |
#### Target Trade Description Corpus

| Batch | CSV Observations | After Cleaning Missing Data | After Deduplication | After Merge with Dates | Final (Year ≤ 2024) |
|-------|-----------------|-----------------------------|---------------------|------------------------|---------------------|
| Batch 1 | 25,526 | 11,235 | 898 | 9,371 | 9,158 |
| Batch 2 | 20,465 | 11,235 | 797 | 9,581 | 9,381 |
| Batch 3 | 17,803 | 11,235 | 771 | 9,603 | 9,360 |
| Batch 4 | 16,201 | 11,235 | 716 | 9,489 | 9,280 |
| Batch 5 | 14,327 | 10,358 | 684 | 8,871 | 8,733 |
| Total | 94,322 | 55,298 | 3,866 | 46,915 | 45,912 |
Note: The trade description corpus retains more deals because this field has fewer missing values compared to target overview text.

## Summary Statistics

| Metric | Target Overview | Target Trade Description |
|--------|-----------------|--------------------------|
| Total unique deals exported | 55,298 | 55,298 |
| Total CSV rows (raw) | 94,322 | 94,322 |
| After cleaning missing data | 55,298 | 55,298 |
| After deduplication | ~2,754 | ~3,866 |
| After merge with dates | 35,539 | 46,915 |
| Final deals with year ≤ 2024 | ~34,752 | ~45,912 |
| Data retention rate (from unique deals) | ~62.8% | ~83.0% |
| Average rows per deal (raw) | ~1.7 | ~1.7 |

## Comparison with Target Overview Corpus

| Aspect | Target Overview | Target Trade Description |
|--------|-----------------|--------------------------|
| Field name | `tar_overview` | `tar_trade_descr_en` |
| Content type | Company overview/description | Trade/business activity description |
| Language | Mixed (mostly English) | English only |
| Final sample size | ~34,752 | ~45,912 |
| Missing data rate | ~37.2% | ~17.0% |
| Use case | General company NLP | Trade-focused NLP |
