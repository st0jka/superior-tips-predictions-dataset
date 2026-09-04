# Superior Tips settled football predictions

An open, append-only record of football predictions that were published before kickoff and graded
after the final whistle. Wins and losses both. One row per settled selection, one file per month.

Licensed **CC-BY 4.0**: use it for anything, including commercially, as long as you say where it
came from. See [Attribution](#attribution).

Maintained by the Superior Football Desk at [superiortips.com](https://superiortips.com).

## Why this exists

Prediction sites publish headline accuracy figures and almost none publish the record behind them.
A percentage nobody can recompute is not evidence. This is the underlying data: every selection we
put in front of a reader, the price we published it at, the probability our model gave it, and what
actually happened. You can recompute any claim we make, and you can find the matches where we were
wrong, because they are in here too.

It is also the input to our own published calibration work, so the numbers on the site and the
numbers in this file come from the same place by construction.

## The data

`data/YYYY-MM.csv`, UTF-8, RFC 4180, one header row per file:

| Column | Meaning |
| --- | --- |
| `date_utc` | Kickoff, ISO 8601 UTC. This is the fixture's time, not the publication time. |
| `league` | Competition name as we hold it. Empty when the competition record is missing. |
| `home` | Home team. |
| `away` | Away team. |
| `market` | Normalised market bucket, for example `Over/Under Goals` or `Asian Handicap`. |
| `pick` | The selection as published, for example `Over 2.5` or `Home -0.5`. |
| `price_at_publish` | Decimal odds at the moment the selection was frozen. Empty if none was stored. |
| `model_probability` | Our model's probability for this selection, 0 to 1, four decimals, read off the frozen pre-match grid. Empty when that snapshot carries no usable rates. |
| `result` | Final score as `home-away`, regulation time (90 minutes plus stoppage). |
| `grade` | `WON`, `LOST` or `VOID`. |
| `snapshot_frozen_at` | When the model snapshot behind the row was written, ISO 8601 UTC. |

### Rules that make the rows comparable

- **Published before kickoff.** A selection and its model snapshot are stored before the match
  starts and are never recalculated from the result. `snapshot_frozen_at` is that instant.
- **Settled only, with a seven day lag.** A row appears here only after the fixture is graded, and
  only once its kickoff is more than seven days in the past. Nothing in this repository is a live
  tip, and nothing here can be read as a forecast.
- **Regulation time.** Football rows settle on the 90 minute score. Extra time and penalties do not
  change the grade of a pre-match selection.
- **`VOID` means the stake came back.** A push on a level handicap or an exact total is not a win
  and not a loss. Excluding it from both is the only way a win rate computed from this file means
  anything. Do not fold `VOID` into either column.
- **Append only.** A row that has been published here is never rewritten. If a grading rule changes,
  it applies to matches settled after the change, and the change is written into
  [CHANGELOG.md](CHANGELOG.md) rather than applied backwards.

### Which columns are populated, and from when

Seven columns are complete on every row ever published here: `date_utc`, `league`, `home`, `away`,
`market`, `pick`, `price_at_publish` and `grade`. The other three arrived with later versions of the
system and are empty before then, because an empty cell is the only honest way to write a number we
never recorded. Measured on the 47,025 settled rows of the twelve months to 2026-09-04:

| Column | Populated from | Coverage |
| --- | --- | --- |
| `model_probability` | 2026-04, in full from 2026-05 | 6,444 rows |
| `result` | 2026-07, in full from 2026-08 | 3,084 rows |
| `snapshot_frozen_at` | 2026-07 | 2,908 rows |

If you are studying calibration, filter to rows where `model_probability` is present and state that
sample size. Do not treat an empty cell as a zero.

### Known limitations, stated up front

- **This is the shortlist, not every forecast.** Our model prices many more fixtures than it
  publishes a selection for. These are the selections that were actually put in front of a reader.
  Treat the file as a record of published picks, not as a sample of the model's whole output.
- **Grading is forward only.** Selections settled before 2026-08-27 were graded by an earlier rule
  that resolved some pushed Asian handicaps as wins. Those rows keep the verdict they were published
  with, because a public record that edits itself is not a record. Rows from 2026-08-27 onward use
  the current settlement rules. Filter on `date_utc` if that distinction matters to your work.
- **`model_probability` is our model's own number, not a market probability.** It is not calibrated
  perfectly and we say so in public: on the selections our model favours it has read high against
  observed frequency. That is exactly why the column is here.
- **Coverage follows our own publication schedule** and is not a random sample of world football.
  Leagues, kickoff times and markets are all unevenly represented.

## Attribution

CC-BY 4.0 requires credit. In a paper, a post or a product, this is enough:

> Superior Tips settled football predictions dataset, superiortips.com, CC-BY 4.0.

If you publish something built on this, we would like to hear about it: contact@superiortips.com.

## Reproducing it

Every row comes from one public endpoint, which is also what the site itself reads:

```
GET https://api.superiortips.com/prediction/proof-ledger?format=csv&days=365
```

The monthly files are that response, filtered to kickoffs older than seven days and split by month.
The site's own published record at [superiortips.com/proof](https://superiortips.com/proof) and
[superiortips.com/accuracy](https://superiortips.com/accuracy) reads the same settled selections, so
the two describe one record rather than two.

One difference worth knowing if you reconcile the totals: the site's aggregate counters additionally
require a computed accumulator probability on the fixture, a leftover condition from an older query.
That makes the site's monthly totals slightly smaller than this file's, by roughly half a percent in
the months where they differ at all, and identical in the most recent ones. This file is the broader
set: every settled published selection.

## Not in this dataset

No live or upcoming predictions. No odds feeds or bookmaker data beyond the single published price.
No personal data of any kind. No basketball: that lane grades on a different rule (final score
including overtime) and mixing the two in one file would put two settlement rules in one column.
