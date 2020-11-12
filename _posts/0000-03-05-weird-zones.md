# Complicated time zones

## Non-integer offsets

- `Australia/Adelaide` (+09:30)
- `Asia/Kathmandu` (+05:45)
- `Africa/Monrovia` (+00:44:30) (Before 1979)

<br/><br/>

## Change of DST status without offset change <!-- .element: class="fragment" data-fragment-index="1" -->

<ul class="fragment" data-fragment-index="1">
    <li>
    Portugal, 1992
    <ul>
        <li><tt>WET (+0 STD) → WEST (+1 DST) 1992-03-29</tt></li>
        <li><tt>WEST (+1 DST) → CET (+1 STD) 1992-09-27</tt><br/></li>
    </ul>
    </li>
    <li>
    Portugal, 1996
    <ul>
        <li><tt>CET  (+1 STD) -> WEST (+1 DST)  1996-03-31</tt></li>
        <li><tt>WEST (+1 DST) -> WET  (+0 STD)  1996-10-27</tt></li>
    </ul>
</ul>


Notes:

Now that we've got the basics down, let's move into the mandatory part of any time zone talk where the presenter tells you about all the weird and scandalous stuff people get up to with their local timekeeping.

You may have a friend — it's OK, I know you are asking for a friend — who thinks that there are only 24 time zones, and that they are all 1-hour increments away from UTC, but if you go to Australia or India, you'll find time zones that are at half-hour offsets, and if you go to Nepal, you'll find they even have one with a 15-minute offset. And if you look at historical data, like in Liberia before 1979, there are offsets that aren't even a whole number of *minutes* away from UTC.

Your friend may also think that the only time that the only time UTC offsets will change is during a daylight saving time change and vice versa — that the offset always changes when DST status changes, but in Portugal in 1992, they decided they didn't want to be on Western European Time anymore, but they wanted to join the UTC+1 time zone, Central European Time, ...

--

# Complicated time zones
<br/>
<br/>

## More than one DST transition per year
<br/>

<ul>
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
<br/>
<br/>

<span >... and Morocco in 2013-present, and Egypt in 2010 and 2014, and Palestine in 2011.</span>

--

# Complicated time zones

<br/>

## Missing days

* Christmas Island (Kiritimati), December 31, 1994  (`UTC-10 → UTC+14`)


```python
>>> dt_before = datetime(1994, 12, 30, 23, 59, tzinfo=ZoneInfo('Pacific/Kiritimati'))
>>> dt_after = add_absolute(dt_before, timedelta(minutes=2))

>>> print(dt_before)
1994-12-30 23:59:00-10:00

>>> print(dt_after)
1995-01-01 00:01:00+14:00
```

<br/>
<br/>


Also Samoa on January 29, 2011.

--

# Complicated time zones

<br/>

## Double days

<br/>

* Kwajalein Atoll, 1969


```python
>>> dt_before = datetime(1969, 9, 30, 11, 59, tzinfo=tz.gettz('Pacific/Kwajalein'))
>>> dt_after = add_absolute(dt_before, timedelta(minutes=2))

>>> print(dt_before)
1969-09-30 11:59:00+11:00

>>> print(dt_after)
1969-09-30 12:01:00+11:00
```

<br/>
<br/>


--

<!-- .slide: data-transition="slide-in none" -->

## `Asia/Shanghai`
![Asia/Shanghai time zone map](images/china_shanghai.png)

--

<!-- .slide: data-transition="none slide-out" -->

## `Asia/Urumqi`
![Asia/Shanghai time zone map](images/china_overlay.png)
