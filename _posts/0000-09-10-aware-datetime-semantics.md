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
