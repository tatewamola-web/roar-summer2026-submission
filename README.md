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
| steering base divisor | 120 | **116** | slightly more steering per unit speed |

## Measured performance

Times are for the full 3-lap race, on the Monza v1.1 map.

| | median | best | worst | spread | collisions |
|---|---|---|---|---|---|
| baseline (Spring 2026 winner) | 320.40 | 320.35 | 320.40 | 0.05 | 0 |
| **this submission** | **320.05** | 320.05 | 320.10 | **0.05** | **0** |

Measured over 5 repeated runs each, same machine, same conditions.

A note on method. Times here vary slightly run to run: identical configurations
can differ by a tick or two because tiny floating-point differences shift a
braking decision. We found configurations whose spread reached 0.30 s — larger
than the differences we were trying to measure. Ranking candidates on a single
race is therefore unreliable, and our first choice of configuration turned out
to be 0.15 s slower than this one once we measured properly.

We selected this configuration on its **median across repeated runs** and on its
**stability**: its slowest observed run (320.10) is still faster than any other
candidate's median, and its 0.05 s spread was the tightest we measured. We
preferred that to a configuration with a faster single sample and a wider
spread, since the latter is likelier to disappoint on unfamiliar hardware.

The solution completes all three laps with **zero collisions**.
