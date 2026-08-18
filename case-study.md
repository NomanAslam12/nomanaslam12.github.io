# RFM Customer Segmentation & Churn Risk

**In one line:** Found 6,878 customers — $56.9M in historical revenue — who looked "high value" by spend history alone but had already gone quiet, a group the prior LTV-only segmentation couldn't see.

**Problem:** LTV-only segmentation grouped customers by lifetime spend, but couldn't tell an actively engaged high spender from one who'd already gone quiet.

**What I built:** RFM (Recency, Frequency, Monetary) scoring in PostgreSQL — quintile-scored each dimension with `NTILE(5)`, combined into five behavioral segments via stacked CTEs.

**Verified before trusting the output:**
- No NULLs or divide-by-zero risk in the revenue calculation
- 91 of 49,487 customers (0.18%) sit on a scoring boundary where tie-breaking is arbitrary — small enough not to move the totals below

| Segment | Customers | Revenue | % of Revenue |
|---|---|---|---|
| Champions | 7,493 | $68.2M | 33.1% |
| Mid-Tier | 20,461 | $64.5M | 31.3% |
| **At Risk (High Value)** | **6,878** | **$56.9M** | **27.6%** |
| New/Low Frequency | 5,453 | $10.8M | 5.2% |
| Lost | 9,202 | $5.9M | 2.9% |

![The At Risk (High Value) segment was invisible to LTV-only segmentation](images/4_rfm_segmentation.png)

**Result:** gives the earlier segmentation project a concrete, dollar-sized win-back target instead of a vague "protect high-value accounts" instruction.

**Limitations:** point-in-time snapshot — segment stability across multiple scoring periods hasn't been checked. No win-back campaign has been run yet to validate the recommendation in practice.

*Tools: PostgreSQL | [View query on GitHub →](https://github.com/NomanAslam12/Int_SQL_Project)*
