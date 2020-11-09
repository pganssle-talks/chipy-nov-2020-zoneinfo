# What you can do

- Be an early adopter!
  <br/>

- Help add support for `zoneinfo` zones in popular libraries

    - `pytz-deprecation-shim` may work with Django, but `zoneinfo` won't out of the box (yet)
    - `pandas` makes extensive use of the private API for `pytz` and `dateutil`, and will not work with `zoneinfo` (as of 1.1.4)

      <br/>
- Start updating documentation and StackOverflow questions to recommend `zoneinfo`
