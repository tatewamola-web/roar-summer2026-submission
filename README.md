# ROAR Summer 2026 — Monza Submission

## Running it

No special setup beyond the standard competition environment:

```
conda activate roar_competition
cd competition_code
python competition_runner.py
```

`competition_runner.py` and `infrastructure.py` are the **unmodified** versions
from the competition skeleton. All solution code is in `submission.py` and its
support modules; waypoint data is loaded from `competition_code/waypoints/`.

Dependencies: `numpy` only (already present in the competition environment) —
see `requirements.txt`.

## What this solution is

It builds on the **Spring 2026 first-place solution** by Racing Cognition
(Advay Bansal, James Farmer, Jacob Lam), published at
<https://github.com/advaybansal/roar_feb>, which the ROAR blog encourages using
as a starting baseline. That solution's architecture is unchanged here:
pure-pursuit lateral control with a radius-based speed controller and
per-section tuning.

Our contribution is an **empirical parameter search** against the simulator.
We built a harness that races candidate configurations in parallel (multiple
CARLA servers on separate RPC ports), and ran 339 evaluations over a 30-parameter
space. Three constants improved on the baseline:

| parameter | baseline | ours | effect |
|---|---|---|---|
| `brake_ticks_div` | 3.0 | **2.5** | shorter brake pulses |
| `mu_s3_new` | 3.6 | **3.7** | higher assumed grip, section 3 |
| `mu_s6` | 3.3 | **3.275** | slight trim, section 6 |

## Measured performance

Times are for the full 3-lap race, on the Monza v1.1 map.

| | median | best | worst | collisions |
|---|---|---|---|---|
| baseline (Spring 2026 winner) | 320.40 | 320.40 | 320.40 | 0 |
| **this submission** | **320.20** | 320.10 | 320.25 | **0** |

Both were measured over repeated runs on the same machine. A note on honesty:
the baseline is perfectly repeatable (320.40 on every run), while this
configuration shows about 0.15 s of run-to-run variation — small floating-point
differences can shift a braking decision by a tick. We therefore report the
**median** rather than our best single sample (320.05). Even the slowest
observed run is faster than the baseline.

The solution completes all three laps with **zero collisions**.
