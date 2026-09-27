# Changelog

The data is append only: a published row is never rewritten. When a rule that produces rows changes,
it applies forward and the change is dated here, so the rule behind any row can be read from its
`date_utc` alone.

## 2026-09-04

First release. Rows begin at the start of the retained window and are added nightly.

## Rule history for rows in this dataset

- **2026-08-27, one settlement definition.** From this date every selection settles under one shared
  definition of each market, and a level Asian handicap (a `+0` line) on a drawn match is graded
  `VOID`. Rows settled before this date keep the grade recorded at the time, when that case was
  graded `WON` on either side of the line. Filter on `date_utc` if the distinction matters.
- **2026-08-27, selection rule changed.** From this date the published selection for each fixture is
  the likeliest bet on its list as priced by the market, and it must also pass our model's checks
  before publication. The previous ranking used each bet type's 60 day hit rate. This changes which
  fixtures appear; grading is unchanged.
