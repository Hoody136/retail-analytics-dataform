# ADR-001: Dataform over dbt Cloud

## Status
Accepted

## Context
Voyeur Voyeur needed a modern data transformation layer on BigQuery. Two main options:
- **dbt Cloud:** The industry standard, excellent docs, large community
- **Dataform:** Google's native BigQuery tool, same modular SQL paradigm

## Decision
Use Dataform.

## Consequences

### Positive
- **Lower cost:** No dbt Cloud subscription (~€100/month saved)
- **Native BigQuery integration:** No separate warehouse connection needed
- **Same core capabilities:** Modular SQL, dependency management, assertions, documentation
- **Google Cloud ecosystem:** Single billing, single IAM, single support channel

### Negative
- **Vendor lock-in:** Tied to Google Cloud (acceptable for BigQuery-native stack)
- **Smaller community:** Fewer Stack Overflow answers, fewer third-party packages
- **Less mature docs:** dbt's documentation is more comprehensive

## Notes
This decision was revisited after 3 months of use. Dataform performed reliably. No regrets.
