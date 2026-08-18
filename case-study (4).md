# Lead Commitment Dashboard

**In one line:** Turned a single national CPL average into a state-specific, live commitment tool — cutting the two failure modes it was built to fix: over-promising in expensive states, under-selling in efficient ones.

**Problem:** Sales managers were committing lead volumes to clients off a flat national CPL average. No state-level visibility before a call.

**What I built:** A Looker Studio dashboard, fed by Google Sheets, converting live CPL into a floor-to-stretch lead range per state — before the call happens.

- **Floor** = what gets promised. **Stretch** = the internal target.
- Forecast = weighted 3-month average (40/35/25) — rebuilt after a true rolling-90-day version proved unreliable in practice.
- Every state auto-classified: **Recommend / Watch / See projected leads**.

**Verified before shipping:** traced the commitment formula to the live CPL feed — confirmed it's dynamic, not hardcoded. Forecast weights sum to 1.0 and correctly favor the most recent month.

![State CPL forecast drives the commitment range in real time](images/lead-commitment-dashboard.png)

**Limitations:** forecast moves in monthly steps, not a continuous window. State-level figures come from the live dashboard, not a raw dataset I re-queried myself.

*Tools: Google Sheets, Looker Studio*
