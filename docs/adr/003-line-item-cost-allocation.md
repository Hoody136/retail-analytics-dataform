# ADR-003: Line-Item Cost Allocation

## Status
Accepted

## Context
Shopify exports order data at two levels:
- **Order header:** Shipping, tax, discounts, refunds (order-level)
- **Order lines:** SKU, quantity, price (line-level)

To calculate true profitability **by SKU**, order-level costs must be apportioned down to each line item. Without this, a £10 shipping cost on a 2-SKU order would be invisible to both products' margin calculations.

## Decision
Apportion order-level costs to line items proportionally by line-item net sales value.

## Method

| Cost Type | Allocation Basis | Rationale |
|---|---|---|
| Discounts | Proportional by line-item gross sales | Higher-value items absorb more discount |
| Shipping | Proportional by line-item net sales | Fairer than equal split |
| Tax | Proportional by line-item net sales | Matches tax jurisdiction rules |
| Refunds | Proportional by line-item net sales | Refunds usually match original purchase mix |

## Consequences

### Positive
- **True SKU-level margin:** Every product carries its fair share of costs
- **Better buying decisions:** Buyers can see which SKUs are actually profitable
- **Consistent with accounting:** Method is defensible and auditable

### Negative
- **Complexity:** More SQL, more testing, more documentation needed
- **Assumption risk:** Proportional by value assumes all SKUs have similar cost profiles (acceptable for this business)
- **Performance:** Extra joins and calculations add compute cost (minimal at this scale)

## Notes
This was the most technically complex model in the project. It required 5 CTEs:
1. Order headers (allocation ratios)
2. Base lines (glue headers to lines)
3. Sales calculations (gross/net/selling price)
4. Cost allocations (shipping + tax)
5. Final allocations (refunds)
