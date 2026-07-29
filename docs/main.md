# perf

## `time(f)`

time(f) -> elapsed milliseconds for one call of the zero-arg lambda f.

## `measure(label, f)`

measure(label, f) -> f's result. Prints "label: <ms> ms" as a side effect, so
you can drop it transparently around any call.

## `bench(f, opts = empty_map())`

bench(f, opts?) -> timing statistics in ms. opts: { iters (100), warmup (3) }.
Runs `warmup` discarded calls, then times `iters` calls individually.
