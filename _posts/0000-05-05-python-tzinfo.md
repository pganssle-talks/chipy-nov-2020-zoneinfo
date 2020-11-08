# Python's Time Zone Model
## `tzinfo`

* Time zones are provided by *subclassing* `tzinfo`.
* Information provided is a function of the datetime:

    * `tzname`: The (usually abbreviated) name of the time zone at the given datetime
    * `utcoffset`: The offset from UTC at the given datetime
    * `dst`: The size of the `datetime`'s DST offset (usually 0 or 1 hour)
