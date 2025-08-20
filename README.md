# Demand Engine Plugins #

This repository contains the public documentation and plugins used by the Demand Engine.

The contents of the repository are monitored by the engine and changes to plugin files will be live reloaded.


## Commodities ##

This document uses the term commodity to describe what is being moved between locations. This is currently only passengers, however the engine can handle other types
such as cargo and so commodity is used generically.


## Plugin Format ##

The features of the Demand Engine are driven by a combination of generation and completion filters.
Plugin files contain a fixed header use by the plugin loader followed by a [JSON](https://en.wikipedia.org/wiki/JSON) formatted object whose key-value pairs
change the behaviour of these filters.

Each tick of the engine uses a weighted random algorithm to select a plugin for this tick based on the relative weight of each plugin. Generation then begins with a
blank generation request and without any further adjustment will result in a group being generated between two random airports anywhere
in the world.  Any deviation from this is the result of plugin parameters.

The mandatory parts of a plugin are:

- The header Id field
- The header Name field
- The header Weight field
- A commodity type
- One of the passenger count selectors
  
Thus the most basic plugin looks like this:

```
// Id: minimal_plugin
// Name: A Minimal Plugin
// Weight: 100
{
    "commodityType": "passenger",
    "passengerCount": 4,
}

```


### Generation Filters ###

Generation filters are used when generating new original demand. The generation request is passed through each filter in turn and the filter is able to change attributes
of that request prior it being converted from a request to a commodity group.

The list of parameters affecting generation filters can be found in [Generation Filters](/Docs/GenerationFilters.md).


### Completion Filters ###

Completion filters are used when a commodity is unloaded at the destination. The default behaviour is for the commodity group to be fully complete at this point, however
completion filters are able to take this original group and turn it into additional new demand such as return trips or follow-on flights. The original commodity group is
available to each completion filter to reference attributes like the routing, passenger information, etc. when creating any additional demand.

The list of parameters affecting completion filters can be found in [Completion Filters](/Docs/CompletionFilters.md).


### Detailed Example ###

Here is an example of a larger, more complete example taken from the Island Hopper plugin:

```
// Id: island_hopper
// Name: Island Hopping Service
// Weight: 40
{
    "commodityType": "passenger",
    "passengerCountMinimum": 2,
    "passengerCountMaximum": 6,
    "originMaximumWaterRunways": 0,
    "originMinimumHardRunways": 1,
    "destinationMaximumWaterRunways": 0,
    "destinationMinimumHardRunways": 1,
    "minimumDistance": 20,
    "maximumDistance": 150,
    "passengerClass": "economy",
    "originCountry": ["PH", "ID", "JP", "GR", "HR", "FJ", "MH"],
    "destinationCountry": ["PH", "ID", "JP", "GR", "HR", "FJ", "MH"],
    "sameCountry": true,
    "randomNextHop": true,
    "timeToLive": 3
}
```

This results in the following limitations on the generation:

- The commodity group has the commodity type passenger, this information is used by the both the Demand Engine and the FSCharter platform in many areas
- The number of passengers will be randomly chosen to be between two and six people
- The chosen origin airport must have no water runways
- The chosen origin airport must have at least one hard runway
- The chosen destination airport must have no water runways
- The chosen destination airport must have at least one hard runway
- The distance between departure and arrival airport must be between 20nm and 150nm
- The passenger group will be fixed at economy class
- The origin country will be restricted to those listed
- The destination country will be restricted to those listed
- The origin and destination must be within the same country (technically making the destination country restriction above not required)
- On completion of the initial group a follow-on group will be created starting at the destination airport, using the same parameters and the
  same passenger names. This will happen a maximum of three times only, after which no more follow-up groups will be created.
  
