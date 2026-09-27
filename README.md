# Superior Tips settled football predictions

An open, append-only record of football predictions published before kickoff and graded after the
final whistle, wins and losses both. One row per settled selection, one file per month.

Licensed **CC-BY 4.0**: use it for anything, including commercially, as long as you say where it
came from. See [Attribution](#attribution).

Maintained by the Superior Football Desk at [superiortips.com](https://superiortips.com), run by a
team with more than 15 years in sports betting.

## Why this exists

A headline accuracy figure means something only when the record behind it can be recomputed. This
is that record: every selection we put in front of a reader, the price it was published at, the
probability our model gave it, and the result. Every losing selection sits in the file beside the
winners, so any figure built on it can be checked row by row.

The site's own published record reads the same settled selections, so the numbers on
superiortips.com and the numbers in this file come from one source.

## The data

`data/YYYY-MM.csv`, UTF-8, RFC 4180, one header row per file:

| Column | Meaning |
| --- | --- |
| `date_utc` | Kickoff, ISO 8601 UTC. This is the fixture's time, not the publication time. |
| `league` | Competition name as we hold it. Left empty if the competition record is missing. |
| `home` | Home team. |
| `away` | Away team. |
| `market` | Normalised market bucket, for example `Over/Under Goals` or `Asian Handicap`. |
| `pick` | The selection as published, for example `Over 2.5` or `Home -0.5`. |
| `price_at_publish` | Decimal odds at the moment the selection was locked. Left empty if no price was stored. |
| `model_probability` | Our goals model's probability for this selection, 0 to 1, four decimals, read off the pre-match score grid locked with the pick. Empty when that grid carries no usable scoring rates. |
| `result` | Final score as `home-away`, regulation time (90 minutes plus stoppage). |
| `grade` | `WON`, `LOST` or `VOID`. |
| `snapshot_frozen_at` | When the model snapshot behind the row was written, ISO 8601 UTC. |

### Rules that make the rows comparable

- **Published before kickoff.** A selection and its model snapshot are stored before the match
  starts and are never recalculated from the result. `snapshot_frozen_at` is that instant.
- **Settled only, with a seven day lag.** A row appears here once the fixture is graded and its
  kickoff is more than seven days in the past. Every row is a settled record; live and upcoming
  predictions stay on the site.
- **Regulation time.** Football rows settle on the 90 minute score. Extra time and penalties leave
  the grade of a pre-match selection unchanged.
- **`VOID` means the stake came back.** A push on a level handicap or an exact total line returns
  the stake and counts as neither a win nor a loss. Leave `VOID` rows out of both sides when you
  compute a win rate from this file.
- **Append only.** A published row is never rewritten. A grading rule change applies to matches
  settled after it, and each one is dated in [CHANGELOG.md](CHANGELOG.md).

### Which columns are populated, and from when

Eight columns are filled on every row: `date_utc`, `league`, `home`, `away`, `market`, `pick`,
`price_at_publish` and `grade`. The other three are recorded from the month shown for each below
and are empty on earlier rows. Measured on the 47,025 settled rows of the twelve months to
2026-09-04:

| Column | Populated from | Coverage |
| --- | --- | --- |
| `model_probability` | 2026-04, in full from 2026-05 | 6,444 rows |
| `result` | 2026-07, in full from 2026-08 | 3,084 rows |
| `snapshot_frozen_at` | 2026-07 | 2,908 rows |

If you are studying calibration, filter to rows where `model_probability` is present and state that
sample size. Treat an empty cell as missing, never as a zero.

### Notes on the data

- **Published selections.** Every row is a selection published on superiortips.com. Our model prices
  many more fixtures than it publishes a selection for, so the file measures the published picks
  specifically: the same set a reader of the site received.
- **Settlement rules by date.** From 2026-08-27 every selection settles under one shared definition
  of each market, and a level Asian handicap (a `+0` line) on a drawn match is graded `VOID`. Rows
  settled before that date carry the grade recorded at the time, when that same case was graded
  `WON`. Published rows are never regraded. Filter on `date_utc` if that distinction matters to your
  work; [CHANGELOG.md](CHANGELOG.md) has the dated entry.
- **`model_probability` is the raw reading of our goals model**, taken from the score grid locked
  with the pick before kickoff and never recomputed. It is a separate figure from the chance printed
  beside a pick on the site and from any market probability, and it can be checked against `grade`
  on every row where it is present.
- **Coverage follows our own publication schedule**, so leagues, kickoff times and markets appear in
  the proportions we published, which differ from world football as a whole.

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
the two describe one record.

One difference worth knowing if you reconcile the totals: the site's aggregate counters include
only fixtures that also carry an accumulator probability. That makes the site's monthly totals
slightly smaller than this file's, by roughly half a percent in the months where they differ at
all, and identical in the most recent ones. This file is the broader set: every settled published
selection.

## Not in this dataset

No live or upcoming predictions. No odds feeds or bookmaker data beyond the single published price.
No personal data of any kind. No basketball: it settles on the final score including overtime, and
keeping it in a separate record means every row here follows the one football settlement rule.
