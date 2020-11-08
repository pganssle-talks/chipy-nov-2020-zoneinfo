# Using `zoneinfo`

First introduced in Python 3.9. Available as a backport to Python 3.6+:

```python
try:
    from backports import zoneinfo
except ImportError:
    import zoneinfo
```

`tzdata` is available for all versions of Python (including 2.7).

  - Safest: declare an unconditional dependency on `tzdata` if you need time zone data.
  - Good enough: Only depend on `tzdata` on Windows: `tzdata; sys_platform == 'win32'`

--

# Using `zoneinfo`

Construct aware datetime:

```python
>>> dt = datetime(2020, 11, 12, 19, tzinfo=ZoneInfo("America/Chicago"))
>>> print(dt)
2020-11-12 19:00:00-06:00

>>> print(datetime.now(ZoneInfo("America/Chicago")))
2020-11-12 19:46:21.211438-06:00
```

<br/>

Convert between time zones

```python
>>> print(dt.astimezone(ZoneInfo("Europe/Paris")))
2020-11-13 02:00:00+01:00
```

--

# Using `zoneinfo`: Absolute time semantics

Many `pytz` users will be surprised by the "wall time" semantics of `datetime`. To deliberately use absolute time semantics, convert to UTC first:

```python
def absolute_add(dt: datetime, td: timedelta) -> datetime:
    dt_utc = dt.astimezone(timezone.utc)
    rv_utc = dt_utc + td
    return rv_utc.astimezone(dt.tzinfo)

def absolute_diff(dt1: datetime, dt2: datetime) -> timedelta:
    dt1_utc = dt1.astimezone(timezone.utc)
    dt2_utc = dt2.astimezone(timezone.utc)

    return dt1 - dt2
```

--

# `zoneinfo`: IANA keys

## `str` and `repr` behavior:

```python
>>> NYC = zoneinfo.ZoneInfo("America/New_York")
>>> str(NYC)
'America/New_York'
>>> repr(NYC)
zoneinfo.ZoneInfo(key='America/New_York')
```

The `str` representation falls back to the `repr` if `key=None`:

```
>>> with open("/path/to/zoneinfo/America/New_York", "rb") as f:
...    NYC_f = ZoneInfo.from_file(f)
...
>>> str(NYC_f)
zoneinfo.ZoneInfo(<_io.BufferedReader name='/usr/share/zoneinfo/America/New_York'>)
```

Access the IANA key from the `ZoneInfo.key` attribute:

```python
>>> NYC.key
'America/New_York'
>>> print(NYC_f.key)
None
```

--

## Available time zones

The `zoneinfo.available_timezones()` function returns a `set` object containing all valid keys available on the system:

```python
import random
import zoneinfo

def random_zone() -> str:
    return random.sample(zoneinfo.available_timezones(), 1)[0]
```
<br/>

```
>>> random_zone()
'Asia/Bishkek'
>>> random_zone()
'Asia/Magadan'
>>> random_zone()
'Europe/Chisinau'
>>> random_zone()
'Egypt'
>>> random_zone()
'Pacific/Wallis'
>>> random_zone()
'America/Santiago'
```

**Warning**: *Don't display this to users!* IANA keys are not intended to be user-facing strings. See [the note in the documentation](https://docs.python.org/3.9/library/zoneinfo.html#zoneinfo.ZoneInfo.key) about this.
