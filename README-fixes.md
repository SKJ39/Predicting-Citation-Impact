# Fixes to the project notebook (EDA and hypothesis testing)

Author: Avi Singh Oberoi
Date: 2026-11-18
File: `cis5450project-avi-fixes.ipynb` (based on Lexie's `cis5450project` notebook)

13 cells altered, 3 cells added. The notebook was re-executed end to end with no errors.
Cell numbers count from 1 at "# Introduction" in the original notebook.

## Summary

1. **Date-gap bug (2.2.3).** The arXiv timestamp kept its time of day while the OpenAlex
   publication date was midnight, so same-day papers got a gap of -1 day. The share of
   papers "published before arXiv" was reported as 62.5%. The correct figure is 15.1%,
   and 57.4% of those carry a January 1 placeholder date.
2. **January citation claim (3.7).** January's high mean comes from papers dated exactly
   January 1 (mean 2.12) and not from the rest of January (mean 1.67).
3. **New robustness check (4.6).** The three permutation tests are rerun with publication
   month added to the strata. All three nulls are still rejected.
4. **Wording and consistency fixes** in the EDA text (3.2 to 3.5).

## Cell-by-cell changes

| Part | Subsection | Cell | Type | Change |
|---|---|---|---|---|
| 2 | 2.2.3 Time Gap | 35 | code | Bug fix: `first_submission_date.dt.normalize()` before subtracting |
| 2 | 2.2.3 Time Gap | 37 | code | Prints negative, same-day and positive shares, and negative gaps dated January 1 |
| 2 | 2.2.3 Time Gap | 38 | text | 62.5% corrected to 15.1%; January 1 placeholder caveat added |
| 3 | 3.2 Feature Outliers | 87 | text | Outlier counts no longer used as a reason to drop features; zero-inflated features explained |
| 3 | 3.3 Correlation Matrix | 88 | text | Spearman described as rank-based and monotonic, not "non-linear" |
| 3 | 3.3 Correlation Matrix | 90 | text | rho of -0.88 corrected to -0.31; `pub_after_arxiv_days` no longer listed as dropped (matches 4.4) |
| 3 | 3.4 Subfield Impact | 93 | text | AI share 47% corrected to 46% |
| 3 | 3.4 Subfield Impact | 95 | text | Computer vision explanation corrected (CNNs and shared benchmarks, not Transformers); medians and low-citation shares added |
| 3 | 3.5 | 96 | text | Heading and intro: reference count is no longer called "collaboration" |
| 3 | 3.7 Industry, Venue, Month | 105 | code | Month plot gains a line excluding January 1 dates; prints the January split |
| 3 | 3.7 Industry, Venue, Month | 106 | text | Venue point reworded as an association; month point rewritten around the January 1 split |
| 4 | 4.3 Date Sanity Check | 113 | text | Time-zone explanation removed (wrong cause) |
| 4 | 4.6 (new) | 121-123 | text, code, text | Robustness check with `subfield x year x month` strata (396 strata) |
| 6 | Conclusion | 148 (was 145) | text | See below |

### Conclusion edits (cell 148)

- **Hypothesis tests:** 4.6 result added; ratio column relabelled as a ratio of geometric means.
- **E4:** January value attributed to placeholder dates (test set: 2.18 for January 1 dates, 1.61 for other January dates).
- **E5:** M5 MAE 0.770 to 0.769; LightGBM interval upper bound 0.088 to 0.089; feature-importance sentence corrected (`referenced_works_count` ranks second, `author_count` fourth).
- **Data caveats:** updated to the corrected 15% figure.

## Effect on results

| Result | Before | After |
|---|---|---|
| Papers with OpenAlex date before first arXiv version | 62.5% | 15.1% |
| Spearman rho, `pub_after_arxiv_days` vs `arxiv_after_pub_days` | -0.88 | -0.31 |
| Baselines, M1, M2, M3, ablation (E4) | unchanged | unchanged |
| M4 Ridge (extended) RMSE | 1.0231 | 1.0232 |
| M5 LightGBM (extended) RMSE | 0.9722 | 0.9719 |
| Top-10% classifier (extended) AUROC | 0.8634 | 0.8635 |
| Hypothesis tests in 4.5 | unchanged | unchanged |

### 4.6 robustness check

| Test | Adjusted effect (4.5) | Adjusted effect (+ month) |
|---|---|---|
| has_code | 0.88 | 0.82 |
| authors >= 5 vs <= 2 | 0.81 | 0.79 |
| venue_mention | 0.62 | 0.54 |

p = 1/10,001 for all three; all rejected at the Bonferroni level of 0.0167.

## Notes for teammates

- Cells 35, 37 and 38 are in the cleaning section; cells 113 and 148 are in the modeling and conclusion sections.
- Outputs in the file were generated outside Colab. Run all cells in Colab before submitting.
