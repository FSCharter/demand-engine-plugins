# Available completion filters

***

### Return trip filters

##### returnTrip
The plugin will immediately generate a return trip for the completed commodity group
```
// Immediately fly the same group back again
{
    "returnTrip": true
}
```

##### returnAfterDays
The return flight will be scheduled after the specified number of days. This filter is silently ignored if `returnTrip` is not active.

```
// Create a return flight 14 days from the completion of the original flight
{
    "returnAfterDays": 14
}
```

##### returnAfterHours
The return flight will be scheduled after the specified number of hours. This filter is silently ignored if `returnTrip` is not active.

```
// Create a return flight 6 hours from the completion of the original flight
{
    "returnAfterHours": 6
}
```

##### returnAfterMinutes
The return flight will be scheduled after the specified number of minutes. This filter is silently ignored if `returnTrip` is not active.

```
// Create a return flight 25 minutes from the completion of the original flight
{
    "returnAfterMinutes": 25
}
```

##### returnOn
The return flight will be scheduled for the specified date and time. This filter is silently ignored if `returnTrip` is not active.
For information on possible values see [here](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.parse?view=net-7.0).
```
// Return on the 24th August 2025
{
    "returnOn": "2025-08-24"
}
```

### Follow-on job filters

##### randomNextHop
Once the initial flight has completed choose a subsequent destination for the same passenger from the provided list.
```
// After their trip from EGCC to KLAX randomly hop the passenger between three other airports.
The origin and destination airports can also be part of the random selection, the passenger's subsequent destination will always be different to their current location.

{
    "originIcao": "EGCC",
    "destinationIcao": "KLAX",
    "randomNextHop": [ "KJFK", "YSSY", "LFPO" ],
    "timeToLive": 10
}
```

##### timeToLive
This filter is for use in combination with other follow-on job filters to limit how many times a subsequent job will be created.
```
// After their trip from EGCC to KLAX randomly hop the passenger between three other airports and stop when they have done a total of 10 trips.
{
    "originIcao": "EGCC",
    "destinationIcao": "KLAX",
    "randomNextHop": [ "KJFK", "YSSY", "LFPO" ],
    "timeToLive": 10
}
```
