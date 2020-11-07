# Complicated time zones

## Non-integer offsets

- `Australia/Adelaide` (+09:30)
- `Asia/Kathmandu` (+05:45)
- `Africa/Monrovia` (+00:44:30) (Before 1979)

## Change of DST status without offset change <!-- .element: class="fragment" data-fragment-index="1" -->

<ul class="fragment" data-fragment-index="1">
    <li>
    Portugal, 1992
    <ul>
        <li><tt>WET (+0 STD) → WEST (+1 DST) 1992-03-29</tt></li>
        <li><tt>WEST (+1 DST) → CET (+1 STD) 1992-09-27</tt></li>
    </ul>
    </li>
    <li>
    Portugal, 1996
    <ul>
        <li><tt>WET  (+0 STD) -> WEST (+1 DST)  1992-03-29</tt></li>
        <li><tt>WEST (+1 DST) -> CET  (+1 STD)  1992-09-27</tt></li>
    </ul>
</ul>

--

# Complicated time zones

## Zone name change without offset change

* Aleutian Islands, 1983:
  * `BST (-11 STD)` -> `BDT (-10 DST)`, `1983-04-24`
  * `BDT (-10 DST)` -> `AHST (-10 STD)`, `1983-10-30`
  * `AHST (-10 STD)` -> `HST (-10 STD)`, `1983-11-30` (Zone renamed)

## More than one DST transition per year  <!-- .element: class="fragment" data-fragment-index="1" -->

<ul class="fragment" data-fragment-index="1">
    <li>
    Morroco, 2012
    <ul>
        <li><tt>WET  (+0 STD) -> WEST (+1 DST)  2012-04-29</tt></li>
        <li><tt>WEST (+1 DST) -> WET  (+0 STD)  2012-07-20</tt></li>
        <li><tt>WET  (+0 STD) -> WEST (+1 DST)  2012-08-20</tt></li>
        <li><tt>WEST (+1 DST) -> WET  (+0 STD)  2012-09-30</tt></li>
    </ul>
    </li>
</ul>

<span class="fragment" data-fragment-index="1">... and Morocco in 2013-present, and Egypt in 2010 and 2014, and Palestine in 2011.</span>

--

# Complicated time zones

## Missing days

* Christmas Island (Kiritimati), December 31, 1994  (`UTC-10 → UTC+14`)


```python
dt_before = datetime(1994, 12, 30, 23, 59, tzinfo=ZoneInfo('Pacific/Kiritimati'))
dt_after = add_absolute(dt_before, timedelta(minutes=2))

print(dt_before)
print(dt_after)
```

<br/>
<pre>
1994-12-30 23:59:00-10:00
1995-01-01 00:01:00+14:00
</pre>


Also Samoa on January 29, 2011.

--

# Complicated time zones

## Double days
* Kwajalein Atoll, 1969


```python
dt_before = datetime(1969, 9, 30, 11, 59, tzinfo=tz.gettz('Pacific/Kwajalein'))
dt_after = add_absolute(dt_before, timedelta(minutes=2))

print(dt_before)
print(dt_after)
```
<br/>

<pre>
1969-09-30 11:59:00+11:00
1969-09-30 12:01:00+11:00
</pre>

--

<!-- .slide: data-transition="slide-in none" -->

## `Asia/Shanghai`
![Asia/Shanghai time zone map](images/china_shanghai.png)

--

<!-- .slide: data-transition="none slide-out" -->

## `Asia/Urumqi`
![Asia/Shanghai time zone map](images/china_overlay.png)

--

# Why do we need to work with time zones at all?
<br/>

```python
from dateutil import rrule as rr
from datetime import datetime, timezone
from zoneinfo import ZoneInfo

# Close of business in New York on weekdays
closing_times = rr.rrule(freq=rr.DAILY, byweekday=(rr.MO, rr.TU, rr.WE, rr.TH, rr.FR),
                         byhour=17, dtstart=datetime(2020, 3, 5, 17), count=5)

NYC = ZoneInfo("America/New_York")
for dt in closing_times:
    print(dt.replace(tzinfo=NYC))
```
<pre style="margin-top: 0.5em">
2020-03-05 17:00:00-05:00
2020-03-06 17:00:00-05:00
2020-03-09 17:00:00-04:00
2020-03-10 17:00:00-04:00
2020-03-11 17:00:00-04:00
</pre>
<br/>

```python
# Get close of business in UTC
for dt in closing_times:
    print(dt.replace(tzinfo=NYC).astimezone(timezone.utc))
```
<pre style="margin-top: 0.5em">
2020-03-05 22:00:00+00:00
2020-03-06 22:00:00+00:00
2020-03-09 21:00:00+00:00
2020-03-10 21:00:00+00:00
2020-03-11 21:00:00+00:00
<pre>
