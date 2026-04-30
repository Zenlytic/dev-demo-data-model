# Pure Organics — 4-5-4 Retail Fiscal Calendar

Pure Organics uses a **4-5-4 retail fiscal calendar**. Apply this calendar whenever the user asks about fiscal periods, fiscal weeks, fiscal months, fiscal quarters, or fiscal years.

## Overview

The 4-5-4 calendar is a standard retail fiscal calendar used to align comparable periods across years:

- The fiscal year is divided into **13 weeks**, grouped into **3 periods per quarter** following a 4-week / 5-week / 4-week pattern.
- Each fiscal quarter contains exactly **13 weeks** (4 + 5 + 4).
- The fiscal year contains exactly **52 weeks** (364 days). A **53rd week** ("leap week") is added approximately every 5–6 years to realign with the Gregorian calendar.
- Fiscal year-end falls on the **Saturday nearest to January 31**.

## Fiscal Year Boundaries

| Fiscal Year | Start Date  | End Date    | Weeks |
|-------------|-------------|-------------|-------|
| FY2023      | 2023-01-29  | 2024-02-03  | 53    |
| FY2024      | 2024-02-04  | 2025-02-01  | 52    |
| FY2025      | 2025-02-02  | 2026-01-31  | 52    |
| FY2026      | 2026-02-01  | 2027-01-30  | 52    |

> Note: FY2023 is a 53-week year. The extra week falls at the end of Q4 (Period 12 becomes 5 weeks instead of 4).

## Quarter and Period Structure

Each fiscal year has **4 quarters**, each with **3 periods** following the 4-5-4 week pattern:

| Quarter | Period | Weeks | Pattern |
|---------|--------|-------|---------|
| Q1      | P1     | 4     | 4       |
| Q1      | P2     | 5     | 5       |
| Q1      | P3     | 4     | 4       |
| Q2      | P4     | 4     | 4       |
| Q2      | P5     | 5     | 5       |
| Q2      | P6     | 4     | 4       |
| Q3      | P7     | 4     | 4       |
| Q3      | P8     | 5     | 5       |
| Q3      | P9     | 4     | 4       |
| Q4      | P10    | 4     | 4       |
| Q4      | P11    | 5     | 5       |
| Q4      | P12    | 4     | 4 (5 in 53-week year) |

## FY2025 Period Calendar (2025-02-02 to 2026-01-31)

| Period | Quarter | Start Date  | End Date    | Weeks |
|--------|---------|-------------|-------------|-------|
| P1     | Q1      | 2025-02-02  | 2025-03-01  | 4     |
| P2     | Q1      | 2025-03-02  | 2025-04-05  | 5     |
| P3     | Q1      | 2025-04-06  | 2025-05-03  | 4     |
| P4     | Q2      | 2025-05-04  | 2025-05-31  | 4     |
| P5     | Q2      | 2025-06-01  | 2025-07-05  | 5     |
| P6     | Q2      | 2025-07-06  | 2025-08-02  | 4     |
| P7     | Q3      | 2025-08-03  | 2025-08-30  | 4     |
| P8     | Q3      | 2025-08-31  | 2025-10-04  | 5     |
| P9     | Q3      | 2025-10-05  | 2025-11-01  | 4     |
| P10    | Q4      | 2025-11-02  | 2025-11-29  | 4     |
| P11    | Q4      | 2025-11-30  | 2026-01-03  | 5     |
| P12    | Q4      | 2026-01-04  | 2026-01-31  | 4     |

## FY2026 Period Calendar (2026-02-01 to 2027-01-30)

| Period | Quarter | Start Date  | End Date    | Weeks |
|--------|---------|-------------|-------------|-------|
| P1     | Q1      | 2026-02-01  | 2026-02-28  | 4     |
| P2     | Q1      | 2026-03-01  | 2026-04-04  | 5     |
| P3     | Q1      | 2026-04-05  | 2026-05-02  | 4     |
| P4     | Q2      | 2026-05-03  | 2026-05-30  | 4     |
| P5     | Q2      | 2026-05-31  | 2026-07-04  | 5     |
| P6     | Q2      | 2026-07-05  | 2026-08-01  | 4     |
| P7     | Q3      | 2026-08-02  | 2026-08-29  | 4     |
| P8     | Q3      | 2026-08-30  | 2026-10-03  | 5     |
| P9     | Q3      | 2026-10-04  | 2026-10-31  | 4     |
| P10    | Q4      | 2026-11-01  | 2026-11-28  | 4     |
| P11    | Q4      | 2026-11-29  | 2027-01-02  | 5     |
| P12    | Q4      | 2027-01-03  | 2027-01-30  | 4     |

## SQL Patterns

When writing SQL for fiscal period filtering, use explicit date range conditions based on the tables above. Do **not** rely on calendar month boundaries.

### Example: Filter to FY2026 Q1

```sql
WHERE order_date >= '2026-02-01' AND order_date < '2026-05-03'
```

### Example: Filter to FY2026 P2

```sql
WHERE order_date >= '2026-03-01' AND order_date < '2026-04-05'
```

### Example: Fiscal Year-to-Date (FY2026, as of 2026-04-29)

```sql
WHERE order_date >= '2026-02-01' AND order_date <= '2026-04-29'
```

### Example: Prior Fiscal Year-to-Date (FY2025, same elapsed days)

FY2025 started 2025-02-02. Elapsed days in FY2026 as of 2026-04-29 = 87 days.
Prior year-to-date end = 2025-02-02 + 87 days = 2025-05-01 (exclusive upper bound: 2025-05-02).

```sql
WHERE order_date >= '2025-02-02' AND order_date <= '2025-05-01'
```

## Key Rules

1. **Never use calendar months as fiscal month proxies.** Fiscal periods do not align with calendar months.
2. **Always use the fiscal year start date, not January 1**, when computing year-to-date ranges.
3. **For YoY comparisons**, align elapsed days from each fiscal year's start date, not calendar dates.
4. **The current fiscal year is FY2026** (started 2026-02-01). The current date is provided in the system prompt.
5. **Fiscal week starts on Sunday** (standard NRF 4-5-4 convention).
