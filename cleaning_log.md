# Cleaning Log

## Applied Rules

| Rule Area | Student-Facing Rule |
| --- | --- |
| Raw file | Do not overwrite raw_orders.xlsx. Create cleaned_orders.xlsx separately. |
| Missing region | Fill as Unknown and flag in quality report. |
| Missing ship_mode | Fill as Unknown and flag in quality report. |
| Missing discount | Treat as 0 only if all other sales fields are valid; otherwise flag. |
| Discount | Flag negative discounts and unusually high discounts as invalid. |
| Cancelled/Failed/Refunded | Do not include non-completed/failed/refunded records in completed-sales summaries. |
| Date sequence | Flag ship_date earlier than order_date. |
| Duplicates | Remove exact duplicate copies; flag conflicting duplicate order IDs. |
| Calculated fields | Create calculated_sales, calculated_profit, profit_margin, shipping_delay_days, order_month, order_year, and data_quality_flag. |
| Documentation | Document cleaning decisions and assumptions in cleaning_log.md. |

## Cleaning Decisions

- Exact duplicate rows were removed; conflicting duplicate `order_id` values were retained and flagged.
- Missing `region` and `ship_mode` values were filled with `Unknown` and flagged.
- Missing `discount` values were set to 0 only where quantity, price, sales, cost, and profit were present.
- Negative discounts and discounts greater than 50% were flagged as invalid or unusually high. The 50% threshold is an explicit assumption because the source rule did not define one.
- Completed-sales summaries include only rows with `order_status = Completed` and `payment_status = Paid`.
- Slash dates were parsed as MM/DD/YYYY because the raw data contains values such as `08/31/2024`; hyphen dates were parsed as DD-MM-YYYY.

## Quality Summary

| metric | value |
| --- | --- |
| Raw rows | 932 |
| Exact duplicate copies removed | 20 |
| Rows after exact duplicate removal | 912 |
| Unique order IDs after exact duplicate removal | 900 |
| Conflicting duplicate order IDs | 12 |
| Rows with any quality flag | 132 |
| Rows marked OK | 780 |
| Missing region filled as Unknown | 25 |
| Missing ship_mode filled as Unknown | 21 |
| Missing discount set to 0 | 26 |
| Negative discount rows | 15 |
| High discount rows > 50% | 7 |
| Ship date earlier than order date | 21 |

## Flag Counts

| flag | rows |
| --- | --- |
| OK | 780 |
| conflicting_duplicate_order_id | 24 |
| high_discount_over_50pct | 7 |
| missing_discount_set_to_zero | 26 |
| missing_region_filled_unknown | 25 |
| missing_ship_mode_filled_unknown | 21 |
| negative_discount | 15 |
| ship_before_order_date | 21 |

## Completed-Sales Summary

| metric | value |
| --- | --- |
| Completed paid orders | 602.0000 |
| Completed paid sales | 5975425.0100 |
| Completed paid profit | 1737674.9000 |
| Completed paid profit margin | 0.2908 |
| Average shipping delay days | 3.8289 |
| Rows with quality flags in completed paid sales | 81.0000 |

## Status Summary

| order_status | payment_status | orders | sales | profit |
| --- | --- | --- | --- | --- |
| Cancelled | Failed | 32 | 277189.8900 | 82783.1400 |
| Cancelled | Paid | 39 | 402416.7700 | 97075.0400 |
| Cancelled | Pending | 37 | 423394.4000 | 129641.4300 |
| Cancelled | Refunded | 37 | 355065.4600 | 102902.3500 |
| Completed | Failed | 2 | 28033.0000 | 9250.4300 |
| Completed | Paid | 602 | 5975425.0100 | 1737674.9000 |
| Returned | Failed | 35 | 337371.1500 | 115041.8000 |
| Returned | Paid | 45 | 411500.3800 | 126277.7400 |
| Returned | Pending | 49 | 547186.1200 | 173615.1100 |
| Returned | Refunded | 34 | 322008.2900 | 92957.5300 |

## Limitation

The raw file does not define a high-discount threshold. Rows over 50% are flagged for review, not overwritten or removed.
