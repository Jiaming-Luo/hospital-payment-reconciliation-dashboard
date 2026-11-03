# DAX Measures

This file documents the core DAX measures used in the Hospital Payment Reconciliation Dashboard.

The dashboard calculations intentionally filter to records where `Data Quality Flag = "VALID"` for core transaction metrics, so conflicting records do not distort reported results.

## Transaction Count

```DAX
Transaction Count = 
CALCULATE(
    DISTINCTCOUNT(Transactions_Raw[Transaction ID]),
    Transactions_Raw[Data Quality Flag] = "VALID"
)
```

## Total Transaction Amount

```DAX
Total Transaction Amount = 
CALCULATE(
    SUM(Transactions_Raw[Amount]),
    Transactions_Raw[Data Quality Flag] = "VALID"
)
```

## Missing Payment Count

```DAX
Missing Payment Count = 
CALCULATE(
    [Transaction Count],
    Transactions_Raw[Status] = "MISSING PAYMENT"
)
```

## Missing Payment Rate

```DAX
Missing Payment Rate = 
DIVIDE(
    [Missing Payment Count],
    [Transaction Count]
)
```

## Mismatch Count

```DAX
Mismatch Count = 
CALCULATE(
    [Transaction Count],
    Transactions_Raw[Status] = "MISMATCH"
)
```

## Mismatch Amount

```DAX
Mismatch Amount = 
CALCULATE(
    [Total Transaction Amount],
    Transactions_Raw[Status] = "MISMATCH"
)
```

## Mismatch Amount Rate

```DAX
Mismatch Amount Rate = 
DIVIDE(
    [Mismatch Amount],
    [Total Transaction Amount]
)
```

## Calculation Logic

- `Transaction Count` uses `DISTINCTCOUNT` on Transaction ID rather than row count.
- Core transaction metrics exclude records flagged for conflict review.
- Missing-payment and mismatch measures reuse validated base measures, so the same data-quality rule remains consistent across the dashboard.
- Rate measures use `DIVIDE` instead of the `/` operator for safer denominator handling.
