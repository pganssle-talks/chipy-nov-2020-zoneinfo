# `zoneinfo`: Cache behavior

   Calls to the default constructor with identical arguments are guaranteed to return objects which compare as identical; specifically, the following must always be valid:

   ```python
   a = ZoneInfo(key)
   b = ZoneInfo(key)
   assert a is b
   ```

   This is because `datetime` assumes that time zones are singletons, which would cause confusing results if we used a simpler implementation:

   ```python
   >>> from datetime import *
   >>> from simple_zoneinfo import SimpleZoneInfo
   >>> dt0 = datetime(2020, 3, 8, tzinfo=SimpleZoneInfo("America/New_York"))
   >>> dt1 = dt0 + timedelta(1)
   >>> dt2 = dt1.replace(tzinfo=SimpleZoneInfo("America/New_York"))
   >>> dt2 == dt1
   True
   >>> print(dt2 - dt1)
   0:00:00
   >>> print(dt2 - dt0)
   23:00:00
   >>> print(dt1 - dt0)
   1 day, 0:00:00
   ```

See [PEP 615](https://www.python.org/dev/peps/pep-0615/) and [the documentation](https://docs.python.org/3/library/zoneinfo.html) for more information than you would ever want about working with the cache.
