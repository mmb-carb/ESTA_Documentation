# ESTA Surrogate Documentation

ESTA is a gridding model that takes raw emissions and applies spatial and temporal surrogates. Of course, ESTA users can include any new spatial or temporal surrogates they want in their modeling. But, several spatial surrogates are included in ESTA for gridding on-road emissions in California. And since they are included with the model, they should be explained. This page describes all the spatial and temporal surrogates included in ESTA by default, where they came from, and what their purpose.


## Table of Contents

* [Spatial Surrogates](#spatial-surrogates)
  - [DTIM On-Road Spatial Surrogates](#dtim-on-road-spatial-surrogates)
  - [ARB On-Road Spatial Surrogates](#arb-on-road-spatial-surrogates)
    + [Calvad VMT by Day-of-Week and Period](#calvad-vmt-by-day-of-week-and-period)
    + [Linehaul](#linehaul)
    + [City Population](#city-population)
    + [30 Idle - 70 Dist](#30-idle---70-dist)
    + [90 Idle - 10 Dist](#90-idle---10-dist)
* [Temporal Surrogates](#temporal-surrogates)
  - [Calvad-Based On-Road Temporal Surrogates](#calvad-based-on-road-temporal-surrogates)


## Spatial Surrogates

Several spatial surrogates are included in ESTA. These are all examples of on-road spatial surrogates designed to disaggregate EMFAC emissions on the California modeling grid. They are divided into two cases: one for a simple DTIM-like run and a second for a more modern simultation using a wider variety of data sources.


### DTIM On-Road Spatial Surrogates

TODO


### ARB On-Road Spatial Surrogates

ARB uses several spatial surrogates when modeling on-road emissions. Most of these surrogates come real, measured traffic data from the [Calvad database](http://www.its.uci.edu/research) to distribute VMT-based on-road emissions across the modeling grid. Though for linehual trucks the [CalTrans CSTDM](http://www.dot.ca.gov/hq/tpp/offices/omsp/statewide_modeling/cstdm.html) data was used, as it was believed to be a more cohesive view of the VMT for those vehicle types.  Unlike previous on-road models, ESTA also includes a series of spatial surrogates for the non-moving emissions from on-road vehicles: starting and resting emissions.

#### Calvad VMT by Day-of-Week and Period 

The [Calvad database](http://www.its.uci.edu/research) uses a variety of real-life traffic measurements to build a real picture of the traffic at the link-level across California.  Because the CalTrans database uses real, measured traffic data, it is an extremly desirable ground-truth data set.  But it does not include a lot of data about the vehicle types in traffic, as it is more expensive to collect that sort of data.  Since the number of wheel axis were recorded for each measured vehicle, it was determined that heavy-duty vehicles (HDV) could easily be subtracted from the data set, leaving a very representative sample for light-duty vehicles (LDV) and medium-duty vehiles (MDV).

Because the real-life nature of Calvad data makes the hourly variation quite messy, it was decided to use spatial surrogate for each of four daily time periods (matching those in the [CalTrans CSTDM](http://www.dot.ca.gov/hq/tpp/offices/omsp/statewide_modeling/cstdm.html) modeling):

* off peak: 6 AM to 10 AM
* midday:   10 AM to 3 PM
* pm peak:  3 PM to 7 PM
* off peak: 7 PM to 6 AM

Spatial surrogates were averaged from the entire 2012 data set, for eight different representative days:

* Monday
* Tuesday
* Wednesday
* Thursday
* Friday
* Saturday
* Sunday
* Holiday

Thus, between the four time periods and eight days, ARB uses 32 Calvad-based VMT spatial surrogates when griding EMFAC on-road emissions.

#### Linehaul

The [CalTrans CSTDM](http://www.dot.ca.gov/hq/tpp/offices/omsp/statewide_modeling/cstdm.html) is a link-level travel-demand model, run every five years using a huge variety of real data.  To produce a spatial surrogate for linehaul vehicles, the 2010 CSTDM outputs were obtained. Then, in ArcMap, the V10 vehicle class was extracted for all links labeled as "major highway", and these links were weighted by VMT.  

The result is probably the most reliable VMT spatial surrogate for linehaul vehicles that has ever been produced for California:

![ARB Linehaul Surrogate](resources/ON_ROAD_CA_100_4km_2010.png)

#### City Population

Some on-road emissions are best distributed by human population. For instance, the multi-day diurnal resting loss from LDV traffic is probably best distribution by human population, or the number of households. The idea being that these emissions are spatially distributed by where personal cars are stored, and they are not really present on highways.

Older models, like DTIM, tried to use Census data to locate these emissions. But Census data is done by "traffic analysis zone" or "census tract". And in rural or mountainous regions these areas can be as big as a whole county, and this ruins the spatial allocation entirely.

To improve upon this, 2012 Census information on the number of households in each city was taken for the several hundred cities in California. That information was added to an ArcGIS shapefile of the city boundaries. The result was a shapefile that represents the population in cities. This file had to be edited slightly as the legal boundaries of coastal cities extend into the ocean three miles, which is inappropriate for on-road vehicle emissions. The ocean areas were trimmed off and the shapefile was weighted by the number of households per square area to create our "City Population" spatial surrogate:

![ARB City Population Surrogate](resources/ON_ROAD_CA_110_4km_2013.png)

#### 30 Idle - 70 Dist

TODO

#### 90 idle - 10 dist

TODO


## Temporal Surrogates

Several temporal surrogates are included in ESTA. These are all examples of on-road temporal surrogates designed to disaggregate daily EMFAC emissions into hourly periods.


### Calvad-Based On-Road Temporal Surrogates

TODO
