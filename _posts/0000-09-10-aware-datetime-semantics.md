# A curious case...

```python
>>> LON = ZoneInfo("Europe/London")

>>> x = datetime(2007, 3, 25, 1, 0, tzinfo=LON)
>>> ts = x.timestamp()
>>> y = datetime.fromtimestamp(ts, LON)
>>> z = datetime.fromtimestamp(ts, ZoneInfo.no_cache("Europe/London"))
```
<br/>

```python
>>> x == y
False
```
<fragment/>
<br/>


```python
>>> x == z
False
```
<fragment/>
<br/>

```python
>>> y == z
True
```
<fragment/>

--

# Hint

`2007-03-25 01:00:00` is imaginary in London!

```python
>>> print(x)                                # x (LON)
2007-03-25 01:00:00+01:00

>>> print(x.astimezone(timezone.utc))       # x (LON → UTC)
2007-03-25 00:00:00+00:00

>>> print(x.astimezone(timezone.utc).
...        .astimezone(LON))                # x (LON → UTC → LON)
2007-03-25 00:00:00+00:00
```

--


# What does equality mean?

1. Wall time semantics: compare only naïve portions

    - `x == y   # False`
    - `x == z   # False`
    - `y == z   # True`

2. Absolute time semantics: convert to UTC

    - `x == y   # True`
    - `y == z   # True`
    - `x == z   # True`

<br/>

## Another hint <!-- .element: class="fragment" data-fragment-index="1" -->

```python
>>> x.tzinfo is y.tzinfo
True
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```python
>>> x.tzinfo is z.tzinfo
False
```
<!-- .element: class="fragment" data-fragment-index="1" -->

--

# Semantics of aware datetime comparison:

1. When two `datetime`s are in the *same zone*, only the naïve portion is compared (wall time semantics).

2. When they are in *different zones*, both are converted to UTC first, then compared (absolute time semantics).

3. Two `datetime`s are in the "same zone" only if `dt1.tzinfo is dt2.tzinfo`.

<br/>
<br/>

## Mystery solved: <!-- .element: class="fragment" data-fragment-index="1" -->

<div class="fragment" data-fragment-index="1" style="text-align:center">
<table>
<tr>
    <td></td>
    <td>Wall</td>
    <td>Absolute</td>
    <td><tt>datetime</tt></td>
</tr>
<tr>
    <td><tt>x == y</tt></td>
    <td>False</td>
    <td>True</td>
    <td>False</td>
</tr>
<tr>
    <td><tt>x == z</tt></td>
    <td>False</td>
    <td>True</td>
    <td>True</td>
</tr>
<tr>
    <td><tt>y == z</tt></td>
    <td>True</td>
    <td>True</td>
    <td>True</td>
</tr>
</table>

</div>

---

# Semantics of aware datetime arithmetic

An analogous problem for comparison semantics is that addition across a DST boundary is not well-defined:

```python
>>> NYC = ZoneInfo("America/New_York")
>>> dt1 = datetime(2020, 3, 7, 13, tzinfo=NYC)
>>> dt2 = d1 + timedelta(days=1)
```

Given that there is a DST transition between `dt1` and `dt2`, there are two options:

```python
>>> print(wall_add(dt1, timedelta(days=1)))  # Next calendar day at the same time
2020-03-08 13:00-04:00

>>> print(absolute_add(dt1, timedelta(days=1)))  # 24 elapsed hours after dt1
2020-03-08 12:00-04:00
```

--

# Semantics of aware datetime arithmetic


Datetime always uses wall-time semantics when interacting with a `timedelta`:


```python
>>> print(wall_add(dt1, timedelta(days=1)))
2020-03-08 13:00-04:00

>>> print(absolute_add(dt1, timedelta(days=1)))
2020-03-08 12:00-04:00

>>> print(dt1 + timedelta(days=1))
2020-03-08 13:00-04:00
```

When two `datetime`s are subtracted, the behavior is different for same-zone and different-zone subtractions:

```
>>> dt2 = datetime(2020, 3, 8, 13, tzinfo=NYC)
>>> dt1_same = datetime(2020, 3, 7, 13, tzinfo=NYC)
>>> dt1_different = dt1_same.astimezone(timezone.utc)  # dt1_same == dt1_different!

>>> print(dt2 - dt1_same)
1 day, 0:00:00

>>> print(dt2 - dt1_different)
23:00:00
```

*See my blog post "Semantics of timezone-aware datetime arithmetic" (https://blog.ganssle.io/articles/2018/02/aware-datetime-arithmetic.html) for a more thorough analysis.*
