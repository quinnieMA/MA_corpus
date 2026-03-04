Zephyr Deal Database Construction Documentation
Database Overview
This document describes the construction of a merger and acquisition (M&A) deal database extracted from Zephyr (Bureau van Dijk), focusing on deals with target overview text for NLP analysis.

Source Data Extraction
Zephyr Search Strategy
The database was constructed using the following search criteria in Zephyr:

Search Step	Criteria	Result Count
1. Deal type	Acquisition	999,339
2. Deal value (USD)	all deals with known value	1,226,677
3. Pre deal multiples	Deals match at least one criteria: Pre-deal value multiple on operating revenue/turnover: min=0 ; Pre-deal equity value multiple on operating revenue/turnover: min=0 ; Pre-deal enterprise value multiple on operating revenue/turnover: min=0 ; Pre-deal modelled enterprise value multiple on operating revenue/turnover: min=0	899,178
Final Search Result	Intersection of above criteria	0
Data Export Information
Export Date: 28/10/2025

Software Version: 30

Update Number: 302/1212

Export Format: CSV with target overview text

Data Processing Pipeline
1. Initial Export Challenge
Due to Zephyr's export limitations (maximum rows per export), the data was downloaded in 5 batches:

Batch	Deals Count
Batch 1	11,235
Batch 2	11,235
Batch 3	11,235
Batch 4	11,235
Batch 5	10,358
Total Unique Deals	55,298
2. Stata Processing Overview
Why Observation Counts Differ
The raw CSV files contain more observations than unique deals because:

One deal may have multiple acquirers → appears as multiple rows

One deal may have multiple targets → appears as multiple rows

Each unique combination generates a separate row in the export

Batch Processing Statistics
Batch	CSV Observations	After Deal_num Dedup	After Merge with Dates	Final (Year ≤ 2024)
Batch 1	25,526	~842	9,158	~8,953
Batch 2	20,465	~680	8,823	~8,640
Batch 3	17,803	~580	7,806	~7,611
Batch 4	16,201	~415	5,906	~5,766
Batch 5	14,327	~237	3,846	~3,782
Total	94,322	~2,754	35,539	~34,752
3. Data Cleaning Steps (Performed for Each Batch)
stata
// Step 1: Import CSV
import delimited "acquisition_industry_[batch]_cleaned.csv"

// Step 2: Keep relevant variables
keep deal_num tar_overview

// Step 3: Clean deal numbers
tostring deal_num, replace
replace deal_num = trim(itrim(deal_num))  // Remove extra spaces
replace deal_num = subinstr(deal_num, `"""', "", .) // Remove quotes

// Step 4: Remove missing values
drop if deal_num=="."                    // Missing deal numbers
drop if tar_overview==""|tar_overview=="""" // Missing target overview

// Step 5: Deduplicate by deal number
bysort deal_num: keep if _n==1

// Step 6: Merge with announcement dates
merge 1:1 deal_num using "acq_d.dta"     // Keep only matched records

// Step 7: Filter by year
keep if year <= 2024
4. Key Definitions
Ultimate Owner Definition
Minimum control percentage: 50.01% in the path from subject company to Ultimate Owner

A company is considered an Ultimate Owner (UO) if:

It has no identified shareholders, OR

Its shareholder's percentages are not known

Deal Value
All deals with known value (USD) are included

Pre-deal multiples are calculated on operating revenue/turnover

Final Dataset Structure
The processed dataset contains:

deal_num: Unique deal identifier (string, cleaned)

tar_overview: Target company overview text (for NLP analysis)

year: Announcement year (numeric, ≤ 2024)

Notes for Users
Row count discrepancy: The raw CSV (94,322 rows) > unique deals (55,298) due to multiple acquirers/targets per deal

Data loss: Substantial filtering occurs when merging with announcement dates and requiring non-missing target overviews

Batch processing: Files are processed independently and need to be appended for full analysis

Time coverage: Only deals announced through 2024 are retained

Repository Structure
text
├── 01-deals/
│   ├── industry/
│   │   └── cleaned_output1/
│   │       ├── acquisition_industry_1_cleaned.csv
│   │       ├── acquisition_industry_2_cleaned.csv
│   │       ├── acquisition_industry_3_cleaned.csv
│   │       ├── acquisition_industry_4_cleaned.csv
│   │       └── acquisition_industry_5_cleaned.csv
│   └── structure_date/
│       └── acq_d.dta
└── 01-corpus/
    └── NLP_tar_overview/
        └── taroverview/           (output directory)
