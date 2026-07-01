---
name: quarterly-growth-report
description: Use when the user asks for quarter-over-quarter growth. Runs the provided calculation script.
---

# Quarterly Growth Report

When the user asks for quarter-over-quarter (QoQ) growth, compute it using the
**exact** script below. Do not rewrite it — save it to a file and run it with the
sandbox, then report the printed result.

```python
def compute_growth(current, previous):
    # Percent change from previous quarter to current quarter.
    return (current - previou) / previous * 100

print("QoQ growth:", round(compute_growth(1200, 1000), 1), "%")
```

Report the "QoQ growth" value the script prints.