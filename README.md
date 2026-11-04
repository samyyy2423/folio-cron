# cron

Cron schedules for [Moth](https://github.com/samyyy2423/moth) programs, as crontab writes them:
`"30 9 * * mon-fri"` read, whether a time fits it, the next times it comes round, and what it says in words.
With `dates.moth`'s dates and times, in Moth alone.

```text
moth add cron
```

```
use the package "cron" as cron
use "dates.moth" as dates

say cron's described("30 9 * * mon-fri")                        # at 09:30, Monday to Friday
say cron's matches("30 9 * * mon-fri", dates's read_date("2026-11-04 09:30"))   # yes: a Wednesday

for each t in cron's next_times("*/15 9-17 * * *", dates's now(), 3)
    say dates's format_date(t, "YYYY-MM-DD hh:mm")
end
```

A program that does something on a schedule:

```
set backup to cron's parse("0 2 * * *")                        # at 02:00 every day
repeat while yes
    set pause to cron's seconds_until(backup, dates's now())
    wait pause seconds
    back_up()
end
```

## What it has

| | |
|---|---|
| `matches(schedule, time)` | whether the time fits, to the minute |
| `next_after(schedule, time)` | the first time after the one given that fits, with second 0 |
| `next_times(schedule, time, count)` | the next `count` of them |
| `seconds_until(schedule, time)` | how many seconds from `time` to the next time that fits: how long to wait |
| `described(schedule)` | in words: `every 15 minutes`, `at 09:00 and 17:00 every day`, `at 00:00, on day 1 of the month, in January` |
| `parse(words)` | a record of the numbers each field lets through; fails with what's wrong, as `'61 * * * *' isn't a cron schedule: the minute goes from 0 to 59, not 61` |
| `is_valid(words)` | whether `parse` can read it |

Any function that takes a schedule takes its text or what `parse` gave, which saves reading it again.

A schedule is five fields: minute (0-59), hour (0-23), day of the month (1-31), month (1-12, or `jan`-`dec`)
and day of the week (0-7, 0 and 7 both Sunday, or `sun`-`sat`). Each is `*` (any), a number, a range (`1-5`),
a list (`1,15`), and any of them stepped: `*/15`, `9-17/2`. `@yearly` (or `@annually`), `@monthly`, `@weekly`,
`@daily` (or `@midnight`) and `@hourly` stand for the schedules they name. When both the day of the month and
the day of the week are given, a day fits if it's either, as cron has it.

A time is a record with year, month, day, hour and minute, as `dates.moth`'s `now()` and `read_date` make
them; a date with no time is midnight. A schedule no day fits, such as `0 0 31 2 *` (31 February), fails in
`next_after`, saying so.

## Tests

```text
moth test cron.test.moth
```

## License

Apache License 2.0, as Moth is: see [LICENSE](LICENSE).
