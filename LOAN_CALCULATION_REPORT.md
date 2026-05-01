# Pet Shop Loan 2023 - Remaining Balance Calculation

## Input
- **Loan principal**: $45,000
- **Loan disbursed**: 3rd month of 2023 (March 2023)
- **Monthly interest**: 5.12% compounded monthly => factor 1.0512
- **Weekly payment**: 12% of weekly profit
- **Data source**: `/data/Pet Care 2023 Weekly Financials.csv` (52 weeks, Jan 2 2023 - Dec 31 2023)

## Weekly Profit Summary (from CSV)
- Total annual profit 2023: **$342,870**
- Weekly profit columns parsed from Weekly Profits field (e.g., $ 6,080)

### Two plausible start scenarios
**Scenario A - payments start Week 9** (27/2/2023-5/3/2023, first week touching March)
- Profit Mar-Dec (weeks 9-52): $291,455
- Total weekly payments 12%: **$34,974.60**
- Monthly profit breakdown (by End Date month):
  - 2023-03: $26,391 => payment $3,166.92
  - 2023-04: $32,681 => $3,921.72
  - 2023-05: $25,004 => $3,000.48
  - 2023-06: $26,817 => $3,218.04
  - 2023-07: $33,672 => $4,040.64
  - 2023-08: $25,869 => $3,104.28
  - 2023-09: $26,658 => $3,198.96
  - 2023-10: $33,503 => $4,020.36
  - 2023-11: $26,927 => $3,231.24
  - 2023-12: $33,933 => $4,071.96
- Months counted: 10 (Mar→Dec)

**Scenario B - payments start Week 10** (6/3/2023-12/3/2023, first *full* week in March)
- Profit Mar-Dec (weeks 10-52): $285,115
- Total weekly payments 12%: **$34,213.80**
- Monthly profit breakdown:
  - 2023-03: $20,051 => $2,406.12
  - (Apr-Dec same as above)

## Compound growth without payments
- Formula: 45,000 × (1.0512)^10 = 45,000 × 1.647607
- **Future value = $74,142.30**

## Remaining balance end of 2023

### Naive subtraction (no amortization of interest)
- Scenario A: $74,142.30 - $34,974.60 = **$39,167.70**
- Scenario B: $74,142.30 - $34,213.80 = **$39,928.50**

### Method 1: Interest applied **before** weekly payment each month (balance * 1.0512 - payment)
- Scenario A: **$30,129.03**
- Scenario B: **$31,321.47**

### Method 2: Payment **before** interest each month ((balance - payment) * 1.0512) – most realistic for “pay throughout month, interest accrues at month-end”
Month-by-month for Scenario A:
| Month | Profit | Payment 12% | Balance start | After pay | After 5.12% interest |
|-------|--------|-------------|---------------|-----------|----------------------|
| 2023-03 | 26,391 | 3,166.92 | 45,000.00 | 41,833.08 | 43,974.93 |
| 2023-04 | 32,681 | 3,921.72 | 43,974.93 | 40,053.21 | 42,103.94 |
| 2023-05 | 25,004 | 3,000.48 | 42,103.94 | 39,103.46 | 41,105.56 |
| 2023-06 | 26,817 | 3,218.04 | 41,105.56 | 37,887.52 | 39,827.36 |
| 2023-07 | 33,672 | 4,040.64 | 39,827.36 | 35,786.72 | 37,619.00 |
| 2023-08 | 25,869 | 3,104.28 | 37,619.00 | 34,514.72 | 36,281.87 |
| 2023-09 | 26,658 | 3,198.96 | 36,281.87 | 33,082.91 | 34,776.75 |
| 2023-10 | 33,503 | 4,020.36 | 34,776.75 | 30,756.39 | 32,331.12 |
| 2023-11 | 26,927 | 3,231.24 | 32,331.12 | 29,099.88 | 30,589.80 |
| 2023-12 | 33,933 | 4,071.96 | 30,589.80 | 26,517.84 | **27,875.55** |

- **Final Scenario A: $27,875.55**
- **Final Scenario B (same method, Week 10 start): $29,129.05**

### Method 3: True weekly simulation (weekly payment each week, interest once per month on month change)
- Scenario A ≈ **$26,517.84**
- Scenario B ≈ **$27,710.28**

## Recommendation / Answer
If you started paying from the first week that touches March (Week 9, 27 Feb–5 Mar) and assume interest compounds monthly *after* weekly payments are deducted, you would owe **≈ $27,876 by 31 Dec 2023**.

If you use a stricter “first full March week” start (Week 10) the figure rises to **≈ $29,129**.

Using a conservative “interest before payment” model:
- Week 9 start: **$30,129**
- Week 10 start: **$31,321**

At absolute minimum (ignoring amortization benefit) you’d still owe **≈ $39,168**.

## Data provenance
File read via `/data/Pet Care 2023 Weekly Financials.csv`, 52 rows, columns:
- `Date range, Start Date, End Date ,Daily Care Weekly Revenue,Grooming Services Weekly Revenue,Training Services Weekly Revenue,Total Weekly Revenue  Week ,Total Weekly Expenses ,Weekly Profits`
Sample row 1: `Week 1,2/1/2023,8/1/2023," $ 5,824.00 "," $ 3,353.00 "," $ 4,432.00 "," $ 13,609.00 "," $ 7,529 "," $ 6,080 "`
