# `pytz`'s time zone model

* `tzinfo` is attached *by the time zone object itself*:

```python
>>> LOS_p = pytz.timezone('America/Los_Angeles')
>>> dt = LOS_p.localize(datetime(2017, 8, 11, 14, 0))
>>> print_tzinfo(dt)


2017-08-11 14:00:00-0700
    tzname:   PDT;      UTC Offset:  -7.00h;        DST:      1.0h
```

* `tzinfo`s are all *static offsets*:

```python
>>> print(repr(LOS_p))
&amp;lt;DstTzInfo 'America/Los_Angeles' LMT-1 day, 16:07:00 STD>

>>> print(repr(dt.tzinfo))
&amp;lt;DstTzInfo 'America/Los_Angeles' EDT-1 day, 20:00:00 DST>
```

* Python's model is designed to be lazy, but `pytz`'s model is *eager*

--

# Handling ambiguous and imaginary times in `pytz`

Because offsets are eagerly evaluated, it is possible to represent `datetime`s that differ only in their offset by attaching different offsets to them. `pytz`'s `is_dst` has three modes:

<br/>

- `is_dst=False` (default): choose the STD side if ambiguous

```python
>>> ambiguous = datetime(2004, 10, 31, 1, 30)

>>> print_tzinfo(LOS_p.localize(ambiguous, is_dst=False))
2004-10-31 01:30:00-0500
    tzname:   EST;      UTC Offset:  -5.00h;        DST:      0.0h
```
<br/>

- `is_dst=True`: choose the DST side if ambiguous

```python
>>> print_tzinfo(LOS_p.localize(ambiguous, is_dst=True))
2004-10-31 01:30:00-0400
    tzname:   EDT;      UTC Offset:  -4.00h;        DST:      1.0h
```
<br/>

- `is_dst=None`: Throw an error if ambiguous

```python
>>> LOS_p.localize(ambiguous, is_dst=None)
AmbiguousTimeError                        Traceback (most recent call last)
...
    362         if is_dst is None:
--> 363             raise AmbiguousTimeError(dt)

AmbiguousTimeError: 2004-10-31 01:30:00
```

--

# Problems with `pytz`'s time zone model

* Requires eager calculation — directly attaching a `pytz` timezone gives the wrong results:

```
>>> dt = datetime(2020, 5, 1, tzinfo=LOS_p)
>>> print_tzinfo(dt)
2020-05-01 00:00:00-0753
    tzname:   LMT;      UTC Offset:  -7.88h;        DST:      0.0h
```

* You must `normalize()` datetimes after you've done some arithmetic on them:


```python
>>> dt = LOS_p.localize(datetime(2020, 5, 1))
>>> dt_add = dt + timedelta(days=180)

>>> print_tzinfo(dt_add)
2018-02-07 14:00:00-0700
    tzname:   PDT;      UTC Offset:  -7.00h;        DST:      1.0h

>>> print_tzinfo(LOS_p.normalize(dt_add))
2018-02-07 13:00:00-0800
    tzname:   PST;      UTC Offset:  -8.00h;        DST:      0.0h
```
