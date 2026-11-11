# Superior Tips settled picks: Top picks, Daily Accumulator and VIP

An open record of the picks Superior Tips puts in front of readers to play: the daily Top picks
(rank 1 is the Bet of the Day), the Daily Accumulator and the VIP channel. Every pick is locked
before kickoff and graded after the final whistle, wins and losses both. One file per product per
month.

Licensed **CC-BY 4.0**: use it for anything, including commercially, as long as you say where it
came from. See [Attribution](#attribution).

Maintained by the Superior Football Desk at [superiortips.com](https://superiortips.com), run by a
team with more than 15 years in sports betting.

## The data

UTF-8, RFC 4180, one header row per file. `day` is the UTC day the pick belongs to; prices are
decimal odds as printed with the pick.

### `data/top-picks/YYYY-MM.csv`

Up to five football picks a day, in the order they were published. From 2026-09-26, the first day
the list was frozen.

| Column | Meaning |
| --- | --- |
| `day` | The day of the list. |
| `rank` | 1 to 5. |
| `bet_of_the_day` | `yes` on the Bet of the Day, rank 1. |
| `kickoff_utc` | Kickoff, ISO 8601 UTC. |
| `country`, `league` | The competition. |
| `home`, `away` | The teams. |
| `pick` | The pick as printed. |
| `price` | The price printed with the pick. |
| `probability` | The chance printed beside the pick, in percent. |
| `result` | Final score, 90 minutes plus stoppage. |
| `grade` | `WON`, `LOST` or `VOID`. |

### `data/daily-accumulator/YYYY-MM.csv`

One football ticket a day, one row per game on it. The ticket columns repeat on every row of the
same day. From 2026-08-27, the day every market began to settle under one definition.

| Column | Meaning |
| --- | --- |
| `day` | The day of the ticket. |
| `ticket_price` | The ticket's total price. |
| `ticket_grade` | `WON` or `LOST`. |
| `leg` | The game's place on the ticket, from 1. |
| `kickoff_utc`, `country`, `league`, `home`, `away` | The game. |
| `pick`, `price`, `probability` | The pick on that game, its price and printed chance in percent. |
| `result` | Final score, 90 minutes plus stoppage. |
| `leg_grade` | `WON` or `LOST`; empty if the site shows no grade for that game. |

### `data/vip/YYYY-MM.csv`

Every pick posted in the VIP channel, football and basketball, as the record on
[superiortips.com/vip](https://superiortips.com/vip) prints it. From 2026-11-01.

| Column | Meaning |
| --- | --- |
| `day` | The VIP day the pick was posted for. |
| `sport` | `football` or `basketball`. |
| `kickoff_utc`, `country`, `league`, `home`, `away` | The game. |
| `pick` | The pick as posted. |
| `price` | The price posted with the pick. |
| `stake_pct` | The stake, in percent of the bankroll. |
| `result` | Final score. |
| `grade` | `WON`, `HALF_WON`, `VOID`, `HALF_LOST` or `LOST`. |

## Rules

- **Locked before kickoff.** Top picks and the accumulator are frozen once per day and every VIP pick
  is posted before its game. A pick is never changed after kickoff.
- **Settled only, seven days later.** A day joins the files once it is seven days old, so every row
  is settled and nothing live is published here.
- **Settlement.** Football settles on 90 minutes plus stoppage. A basketball pick settles as its
  text says, overtime included. `VOID` means the stake came back.
- **Append only.** A published row is never rewritten. Changes are dated in
  [CHANGELOG.md](CHANGELOG.md).

## Attribution

CC-BY 4.0 requires credit. In a paper, a post or a product, this is enough:

> Superior Tips settled picks dataset, superiortips.com, CC-BY 4.0.

If you publish something built on this, we would like to hear about it: contact@superiortips.com.

## Reproducing it

Every row comes from the public endpoint the site itself renders:

```
GET https://api.superiortips.com/prediction/top-picks?date=YYYY-MM-DD
GET https://api.superiortips.com/accumulator/v3?date=YYYY-MM-DD
GET https://api.superiortips.com/vip/record?month=YYYY-MM
```

## Not in this dataset

No live or upcoming picks. No picks outside these three products. No personal data of any kind.
