# ADR-002: 4-4-5 ISO Retail Calendar

## Status
Accepted

## Context
Standard calendar months have uneven weeks (4, 4, 5, 4, 4, 5...). This makes week-on-week and year-on-year comparisons misleading in retail. For example, January might have 4 weeks one year and 5 weeks the next.

Retail industry standard is the **4-4-5 calendar:**
- Quarter 1: 4 weeks, 4 weeks, 5 weeks
- Quarter 2: 4 weeks, 4 weeks, 5 weeks
- And so on...

This gives 52 weeks per year (or 53 in leap years), with consistent period lengths for comparison.

## Decision
Build a custom 4-4-5 ISO calendar in the data warehouse.

## Consequences

### Positive
- **True like-for-like comparisons:** PW, PWLY, P-4W, YTD all compare equal-length periods
- **Retail season alignment:** Summer/Winter seasons map cleanly to calendar periods
- **Industry standard:** Buyers and merchandisers already think in these terms

### Negative
- **Custom build required:** No off-the-shelf 4-4-5 calendar in BigQuery
- **53-week handling:** Leap years need special logic (implemented via CASE statement)
- **Non-standard:** Finance teams may still want standard months for accounting

## Notes
The calendar is built from 2024–2034. A 53-week safeguard is included in the SQL.
