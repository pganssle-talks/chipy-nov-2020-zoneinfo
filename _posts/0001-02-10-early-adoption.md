# What's next?
<br/>

- Ambiguous and imaginary time detection?
    - Currently available via `dateutil.tz`:
        - `tz.datetime_ambiguous`
        - `tz.datetime_exists`
        - `tz.resolve_imaginary`
  <br/><br/>

- Improvements to `datetime` arithmetic

    - Separate built-in functions for absolute and wall time
    - Possible minor changes to the semantics of datetime subtraction
  <br/><br/>

- Leap second support

    - Needs changes to the `datetime` class
    - Leap second data is included in `tzdata` and IANA database
    - Looking for real use cases
  <br/><br/>

--

# What you can do

<br/>

- Be an early adopter!
    <br/><br/>

- Help add support for `zoneinfo` zones in popular libraries

    - `pytz-deprecation-shim` may work with Django, but `zoneinfo` won't out of the box (yet)
    - `pandas` makes extensive use of the private API for `pytz` and `dateutil`, and will not work with `zoneinfo` (as of 1.1.4)

    <br/><br/>
- Start updating documentation and StackOverflow questions to recommend `zoneinfo`
    <br/><br/>
- Bring me your real-world leap second use cases!
