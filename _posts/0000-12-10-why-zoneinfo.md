# Why `zoneinfo`?

- It's *fast* (numbers from `backports.zoneinfo`'s benchmark suite):

```
Running constructor in zone America/New_York
c_zoneinfo: mean: 214.65 ns ± 43.48 ns; min: 190.88 ns (k=5, N=1000000)
pytz: mean: 1.21 µs ± 78.31 ns; min: 1.10 µs (k=5, N=200000)
dateutil: mean: 1.33 µs ± 117.35 ns; min: 1.23 µs (k=5, N=200000)

Running from_utc in zone America/New_York
c_zoneinfo: mean: 658.55 ns ± 28.92 ns; min: 617.08 ns (k=5, N=500000)
pytz: mean: 5.12 µs ± 515.26 ns; min: 4.70 µs (k=5, N=50000)
dateutil: mean: 10.64 µs ± 746.99 ns; min: 10.20 µs (k=5, N=20000)

Running to_utc in zone America/New_York
c_zoneinfo: mean: 616.13 ns ± 16.14 ns; min: 604.76 ns (k=5, N=500000)
pytz: mean: 848.44 ns ± 28.10 ns; min: 806.72 ns (k=5, N=500000)
dateutil: mean: 8.03 µs ± 509.75 ns; min: 7.55 µs (k=5, N=50000)

Running utcoffset in zone America/New_York
c_zoneinfo: mean: 373.89 ns ± 5.76 ns; min: 368.24 ns (k=5, N=1000000)
pytz: mean: 564.55 ns ± 13.65 ns; min: 552.88 ns (k=5, N=500000)
dateutil: mean: 7.95 µs ± 642.62 ns; min: 7.44 µs (k=5, N=50000)
```

Because of the C backend, `zoneinfo` is faster than `pytz` and `dateutil` on every metric.

--

# Why `zoneinfo`?

- `pytz` will soon be deprecated and replaced with a wrapper around `zoneinfo`:

<div style="text-align:center">
<img src="images/pytz_deprecation.png"
     alt="Text from the PEP 615 discussion from stub42, maintainer of pytz: <!--
--><!--
-->       Hi. First, thanks for working on this. I’ve managed to put off similar work for about a decade now. I look forward being able to deprecate pytz, making it a thin wrapper around the standard library when run with a supported Python. This kind of needs to happen before 2038, as pytz dates from before the newest tzfile format and does not handle the later timestamps."/>
</div>

- `zoneinfo` is an improved implementation of `dateutil`'s IANA time zones, and `dateutil` will be updated to be a drop-in replacement.

- Both `dateutil` and `pytz` currently suffer from a year 2038 bug, which has been exacerbated by some changes to the default compilation mode of the time zone database.
