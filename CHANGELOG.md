# Changelog

The data is append only: a published row is never rewritten. When a rule that produces rows changes,
it applies forward and the change is recorded here, so a reader can tell which rule a row was
settled under from its `date_utc` alone.

## 2026-09-04

First release. Rows begin at the start of the retained window and are added nightly.

## Rule history that affects rows already in this dataset

- **2026-08-27, settlement rules unified.** Before this date several parts of the system held partial
  definitions of what a stored bet string meant, and the worst of them settled every Asian handicap on
  a substring match, which resolved a DRAW as a win on both sides of a level handicap. Selections
  settled from this date onward use one shared definition, and a push now leaves the row `VOID`
  instead of `WON`. Earlier rows keep the verdict they were published with. Filter on `date_utc` if
  the distinction matters.
- **2026-08-27, selection rule changed.** From this date the published selection is ranked by the
  bookmaker price and vetoed by our own model, rather than by a 60 day strike rate keyed on the bet
  text. This changes which fixtures appear, not how any row is graded.
