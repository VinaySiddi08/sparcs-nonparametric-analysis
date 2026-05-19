# Data Source

The dataset used in this analysis is the **SPARCS Inpatient Discharges (De-identified) 2022** from the New York State Department of Health.

## Where to get it

- **Public download:** https://health.data.ny.gov/
- **Dataset name:** Hospital Inpatient Discharges (SPARCS De-identified): 2022
- **License:** Public domain (de-identified administrative data)

## How to prepare the file used in this analysis

The analysis uses a **subset** of the full SPARCS 2022 file, restricted to Kings County (Brooklyn) and Queens County. To recreate the input file:

1. Download the full SPARCS 2022 CSV from the NY State Open Data Portal (~1 GB)
2. Filter rows where `Hospital County` is either `Kings` or `Queens`
3. Save the filtered file as `SPARCS_2022_Kings_Queens.csv` in the project root (~160 MB)

The R Markdown file expects this CSV to be present.

## Why the data is not committed

The filtered file is ~160 MB, which exceeds GitHub's 100 MB per-file limit. It is also publicly available from the original source, so re-distributing it here would be redundant.

## Privacy

All SPARCS data is **de-identified** before public release. No record contains personally identifiable information about patients.
