# perf

Measure the performance of your own [Ecko](https://ecko.sh) code — time a call,
wrap it with timing, or benchmark it best-of-N. Written in Ecko over
`std.time.monotonic`; pure, no capabilities. All durations are milliseconds.

## Install

```bash
ecko add https://github.com/ecko-sh/perf
```

## Usage

```ecko
import perf

perf.time(|| work())                   # 12.3  (ms, one call)

# measure() returns the result and prints "label: <ms> ms"
data = perf.measure("load", || load_data())

r = perf.bench(|| parse(src), { iters: 1000 })
# { min: 0.42, max: 1.10, mean: 0.51, median: 0.48,
#   total: 510.0, iters: 1000, ops_per_sec: 1960.8 }
```

## API

| Function | Description |
|---|---|
| `time(f)` | Elapsed **ms** for one call of the zero-arg lambda `f` (result discarded) |
| `measure(label, f)` | Runs `f`, prints `"label: <ms> ms"`, and **returns `f`'s result** |
| `bench(f, opts?)` | Runs `f` `iters` times (after `warmup` discarded runs) and returns timing stats |

`bench` options: `{ iters (default 100), warmup (default 3) }`. It returns
`{ min, max, mean, median, total, iters, ops_per_sec }` — durations in ms,
`ops_per_sec` derived from the mean.

## Notes

- Each iteration is timed individually, so `bench` reports a real distribution
  (min / median / mean / max). For sub-microsecond operations the two clock
  reads bracket the call, so lean on the mean and a high `iters`.
- The warmup runs settle caches and branch prediction before measurement.

## Testing

```bash
ecko test tests/
```

Timing is non-deterministic, so the tests assert invariants (shape, ordering,
`ops_per_sec > 0`, `measure` transparency) rather than exact durations.

## License

MIT — see [LICENSE](LICENSE).
