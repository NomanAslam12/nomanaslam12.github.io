# Customer Segmentation, Cohort & Retention

**In one line:** 25% of customers drive 66% of revenue, retention stabilizes at a leaky ~90% churn within 2-3 years for every cohort, and 2024 customers spend 27% less than 2016-2018 ones — three findings, one root problem: the business is refilling a bucket that leaks at a fixed rate.

**Problem:** an e-commerce company needed to know who its valuable customers were, how revenue trends moved across acquisition years, and where purchase drop-off was happening.

**What I built:** three linked SQL analyses in PostgreSQL — LTV-based segmentation, cohort revenue tracking by first-purchase year, and retention/churn analysis by cohort.

![Top-value customers, 25% of the base, generate two-thirds of all revenue](images/1_customer_segmentation.png)

**Cohort revenue is declining, not flat:**
2016-2018 cohorts spent $2,800+ on average; by 2024, that dropped to ~$2,040 — a ~27% fall, not explained by any single bad year.

![Newer cohorts spend meaningfully less than 2016-2018 cohorts](images/2_cohort_analysis.png)

**Retention is a systemic problem, not a bad-cohort problem:**
Every cohort — old or new — settles into roughly the same 89-92% churn rate within 2-3 years. That consistency is the finding: this isn't fixable by targeting one weak cohort.

![Every cohort converges to roughly the same churn rate](images/3_customer_retention.png)

**Result:** reframed the business question from "how do we win back a few unhappy customers" to "why does the retention curve leak at the same rate for every cohort" — a structural question with a structural fix, not a one-off campaign.

**Limitations:** no CAC or margin data available, so "acquisition is expensive relative to retention" is a reasonable inference, not a proven number.

*Tools: PostgreSQL | [View queries on GitHub →](https://github.com/NomanAslam12/Int_SQL_Project)*
