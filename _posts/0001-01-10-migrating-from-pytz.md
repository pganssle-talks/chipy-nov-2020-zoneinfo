# Migrating from `pytz`

If you have any public-facing interface that returns `pytz` timezones (or `datetimes` localized with `pytz`), it will be a **breaking change** to move away from `pytz`:

```python
def pre_migration():
    return pytz.timezone("America/New_York").localize(datetime(2020, 1, 1))

def post_migration():
    return datetime(2020, 1, 1, tzinfo=ZoneInfo("America/New_York"))

def sixty_days_later(dt: datetime) -> datetime:
    non_normalized_dt = dt + timedelta(days=60)
    return dt.tzinfo.normalize(non_normalized_dt)
```

<br/>

<!-- Bizarrely, some combination of reveal.js, jekyll-reveal and the
highlighter / markdown processor wants to close tags and normalize them, and I
think this happens *twice*, so &lt;tag> won't work, you need &amplt;tag>,
otherwise <Tag> gets turned into <tag></tag> -->

```python
>>> sixty_days_later(pre_migration())
datetime.datetime(2020, 3, 1, 0, 0, tzinfo=&amplt;DstTzInfo 'America/New_York' EST-1 day, 19:00:00 STD>)

>>> sixty_days_later(post_migration())
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
&amplt;ipython-input-7-b71365e0022f> in &amplt;module>
----> 1 sixty_days_later(post_migration())

&amplt;ipython-input-5-f165abf34e2a> in sixty_days_later(dt)
      7 def sixty_days_later(dt: datetime) -> datetime:
      8     non_normalized_dt = dt + timedelta(days=60)
----> 9     return dt.tzinfo.normalize(non_normalized_dt)

AttributeError: 'zoneinfo.ZoneInfo' object has no attribute 'normalize'
```

--

# `pytz-deprecation-shim`

[`pytz-deprecation-shim`](https://pytz-deprecation-shim.readthedocs.io/en/latest/) is a mostly backwards-compatible implementation of `pytz`'s interface that is *also* a thin wrapper around `zoneinfo`. It can be used exactly as a `zoneinfo.ZoneInfo` object would be:

```python
>>> import pytz_deprecation_shim as pds
>>> from datetime import datetime, timedelta
>>> LA = pds.timezone("America/Los_Angeles")

>>> dt = datetime(2020, 10, 31, 12, tzinfo=LA)
>>> print(dt)
2020-10-31 12:00:00-07:00

>>> dt.tzname()
'PDT'
```

But also exposes `pytz`'s interface, raising a `DeprecationWarning` when `pytz`-specific features are used:

```python
>>> dt = LA.localize(datetime(2020, 10, 31, 12))
&amplt;stdin>:1: PytzUsageWarning: The localize method is no longer necessary, as
this time zone supports the fold attribute (PEP 495). For more details on
migrating to a PEP 495-compliant implementation, see
https://pytz-deprecation-shim.readthedocs.io/en/latest/migration.html

 >>> print(dt)
2020-10-31 12:00:00-07:00
>>> dt.tzname()
'PDT'
```

--

# `pytz-deprecation-shim`: Helper functions

- `pytz_deprecation_shim.wrap_zone(tz, key=...)`: Wrap an existing `zoneinfo` object in a shim object.
    <br/>
- `pytz_deprecation_shim.is_pytz_zone(tz)`: Detect whether you have a `pytz` zone or not, without needing to be able to import `pytz`.
    <br/>
- `pytz_deprecation_shim.upgrade_tzinfo(tz)`:

    -  "Unwraps" a shim zone into the underlying `zoneinfo` or `dateutil.tz` (Python 2.7) implementation.
    - Turns a `pytz` zone into its `zoneinfo` / `dateutil.tz` equivalent (raises an exception if no equivalent exists)

--

# `pytz-deprecation-shim`: Difference in arithmetic semantics

It is not possible to make a shim time zone with the same arithmetic semantics as both `pytz` and `zoneinfo` `datetimes`:

```python
def one_day_later(dt: datetime) -> datetime:
    next_day_non_normalized = dt + timedelta(days=1)
    return dt.tzinfo.normalize(next_day_non_normalized)
```
<br/>

```python
>>> dt_pytz = pytz.timezone("America/New_York").localize(datetime(2020, 10, 31, 12))
>>> print(one_day_later(dt_pytz))
2020-11-01 11:00:00-05:00

>>> dt_shim = pds.timezone("America/New_York").localize(datetime(2020, 10, 31, 12))
>>> print(one_day_later(dt_shim))
2020-11-01 12:00:00-05:00
```

Other than the deprecation warning, this will be a silent behavior change!
