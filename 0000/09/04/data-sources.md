# PEP 615: Support for the IANA Time Zone Database in the Standard Library

<br/>

- Introduced in Python 3.9
- Adds the `zoneinfo` module
- Introduces the `tzdata` first-party PyPI package

<br/>

<div style="text-align:center">
<img 
    src="images/zoneinfo-documentation.png"
    alt="A screenshot of Python 3.9's zoneinfo documentation."/>
</div>

<br/>


--

# `zoneinfo` data source: IANA Time Zones

- Provides historical time zone information
- Standard open source (public domain) source for time zone information
- Shipped with many operating systems
- Source for `dateutil` and `pytz`'s data.
- 2-21 releases per year (average 9)

<br/>
<div style="text-align: center">
<img src="images/all_zones.png" alt="Map of IANA time zones"/>
</div>

--

# `zoneinfo` design parameters

1. Whenever the system has IANA time zone data available, it should be preferred.

2. End users should be able to specify where their time zone data is deployed.

3. It should be simple to use `zoneinfo` on any platform.

<br/>
<br/>

# Solution <!-- .element: class="fragment" data-fragment-index="1" -->

<ol class="fragment" data-fragment-index="1">
<li><strong><tt>zoneinfo.TZPATH</tt></strong>: Time zone search path
    <ul>
        <li>Defaults to well-known deployment locations</li>
        <li>Configurable in-program using <tt>zoneinfo.reset_tzpath()</tt></li>
        <li>Configurable with environment variable <tt>PYTHONTZPATH</tt></li>
        <li>Default can be set at compile time (for distro packagers)</li>
    </ul>
    <br/>
</li>

<li><strong><tt>tzdata</tt></strong>: First party data-only fallback library
    <ul>
     <li>Not bundled with Python by default yet</li>
     <li>Required on Windows</li>
     <li>Doesn't hurt anything on other platforms</li>
     </ul>
</li>
</ol>

Notes:

**Switch to camera 2**
