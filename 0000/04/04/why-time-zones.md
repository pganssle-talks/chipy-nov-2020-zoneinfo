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
