# Energy-risk live track record

Daily risk forecasts for ten North American energy stocks (XOM, CVX, COP, EOG, OXY, IMO, SU,
CNQ, CVE, ENB) and an equal-weight basket, made **before** each NYSE session and scored
afterwards. Nothing is ever backfilled or deleted.

What is forecast each session:

- 1-day Value-at-Risk and Expected Shortfall at 97.5% and 99% (GARCH-t; HS-250 and EWMA-t as benchmarks)
- each stock's oil beta (250-day rolling)
- stress bands: "if WTI falls X% today", three clearly labeled methods (NORMAL, STRESSED, VOL-SCALED)

The rules, including how forecasts are scored and when any verdict is allowed, were
pre-registered before the first forecast.

## What is in this repository

- `ledger.csv`: one row per NYSE session: status (OK, PARTIAL, LATE, MISSED, UNSCORED), the
  SHA-256 fingerprint of that session's forecast file, and when it was made and timestamped
- `proofs/`: an RFC 3161 timestamp token (`.tsr`) and an OpenTimestamps proof (`.json.ots`) per session
- `SCORECARD.md`: the running scorecard
- `VERIFY.md`: how to check any session yourself

No prices or returns are ever published here. Forecast contents and scores are withheld
until the data license for publishing them is confirmed; their fingerprints are already
public, so they can be checked when released.
