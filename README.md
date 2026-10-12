# Perf - Ecko Std Lib Package

Measure the performance of your own [Ecko](https://ecko.sh) code - time a call,
wrap it with timing, or benchmark it best-of-N. Written in Ecko over
`std.time.monotonic`. All durations are milliseconds.

## Install

```bash
ecko get github.com/ecko-lang/perf
```

## Usage

```ecko
import perf

perf.time(fn() work())  # 12  (ms, one call)

# measure() returns the result and prints "label: <ms> ms"
data = perf.measure("load", fn() load_data())

r = perf.bench(fn() parse(src), { iters: 1000 })
# { min: 0, max: 2, mean: 0.51, median: 1,
#   total: 510, iters: 1000, ops_per_sec: 1960.78 }
```

## API

| Function | Description |
|---|---|
| `time(f)` | Elapsed **ms** for one call of the zero-arg lambda `f` (result discarded) |
| `measure(label, f)` | Runs `f`, prints `"label: <ms> ms"`, and **returns `f`'s result** |
| `bench(f, opts?)` | Runs `f` `iters` times (after `warmup` discarded runs) and returns timing stats |

`bench` options: `{ iters (default 100), warmup (default 3) }`. It returns
`{ min, max, mean, median, total, iters, ops_per_sec }` - durations in ms,
`ops_per_sec` derived from the mean.

## Notes

- Each iteration is timed individually, so `bench` reports a real distribution
  (min / median / mean / max). The clock counts whole milliseconds, so a call
  faster than that times as `0` or `1`; for sub-millisecond operations lean on
  the mean and a high `iters`.
- The warmup runs settle caches and branch prediction before measurement.
- Works on Ecko before and after 0.58, which changed `time.monotonic()` from
  Float seconds to Int milliseconds: the clock checks which one it got. Before
  0.38.1 a measurement on 0.58 came out a thousand times too large.

## Testing

```bash
ecko test tests/
```

Timing is non-deterministic, so the tests assert invariants (shape, ordering,
`ops_per_sec > 0`, `measure` transparency) rather than exact durations.

## License

MIT - see [LICENSE](LICENSE).
