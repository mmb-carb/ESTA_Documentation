# ESTA Surrogate Guide

ESTA is a gridding model that takes raw emissions and applies spatial and temporal surrogates. Of course, ESTA users can include any new spatial or temporal surrogates they want in their modeling. Several spatial surrogates are included in ESTA for gridding on-road emissions in California and since they are included with the model, they should be explained. This page describes all the spatial and temporal surrogates included in ESTA by default, where they came from, and their purpose.


## Table of Contents

* [Spatial Surrogates](#spatial-surrogates)
  - [ARB On-Road Spatial Surrogates](#arb-on-road-spatial-surrogates)
    + [CalVAD VMT by Period](#calvad-vmt-by-period)
    + [Linehaul](#linehaul)
    + [City Population](#city-population)
    + [Distribution Centers](#distribution-centers)
    + [Idling Locations](#idling-locations)
    + [30 Idle - 70 Dist](#30-idle---70-dist)
    + [90 Idle - 10 Dist](#90-idle---10-dist)
* [Temporal Surrogates](#temporal-surrogates)
  - [CalVAD-Based On-Road Diurnal Temporal Surrogates](#calvad-based-on-road-diurnal-temporal-surrogates)
  - [CalVAD-Based On-Road Day-of-Week Temporal Surrogates](#calvad-based-on-road-day-of-week-temporal-surrogates)


## Spatial Surrogates

Several spatial surrogates are included in ESTA. These are all examples of on-road spatial surrogates designed to disaggregate EMFAC emissions on the California modeling grid. They are divided into two cases: one for a simple DTIM-like run and a second for a more modern simulation using a wider variety of data sources.


### ARB On-Road Spatial Surrogates

ARB uses several spatial surrogates when modeling on-road emissions. Most of these surrogates are derived from real, measured traffic data from the [CalVAD database](https://www.arb.ca.gov/research/apr/past/11-316.pdf) to distribute VMT-based on-road emissions across the modeling grid. However, for linehual trucks the [CalTrans CSTDM](http://www.dot.ca.gov/hq/tpp/offices/omsp/statewide_modeling/cstdm.html) data was used, as it was believed to be a more cohesive view of the VMT for those vehicle types.  Unlike previous on-road models, ESTA also includes a series of spatial surrogates for the non-moving emissions from on-road vehicles: starting and resting emissions.

#### CalVAD VMT by Period 

> NOTE: In the near future we may switch from CalVAD to ITN for VMT surrogates.

The [CalVAD database](https://www.arb.ca.gov/research/apr/past/11-316.pdf) uses a variety of real-life traffic measurements to build a real picture of the traffic at the link-level across California. Because the CalTrans database uses real, measured traffic data, it is an extremely desirable ground-truth data set. Unfortunately, it does not include a lot of data about the vehicle types in traffic, as it is more expensive to collect that sort of data. Since the number of wheel axes were recorded for each measured vehicle, it was determined that heavy-heavy-duty vehicles (HHDV) could easily be subtracted from the data set, leaving a very representative sample for light-duty vehicles (LDV) and light-medium-duty vehicles (LMDV).

Because the real-life nature of CalVAD data makes the hourly variation quite messy, it was decided to use spatial surrogate for each of four daily time periods (matching those in the [CalTrans CSTDM](http://www.dot.ca.gov/hq/tpp/offices/omsp/statewide_modeling/cstdm.html) modeling):

* am peak: 6 AM to 10 AM
* midday:   10 AM to 3 PM
* pm peak:  3 PM to 7 PM
* off peak: 7 PM to 6 AM

Any surrogate given to ESTA can be given alone, or as a set of four for the different CalVAD time periods. In the config.ini file you simply have to name easy surrogate with the time-period endings: `_am`, `_mid`, `_pm`, and `_off`.

Here is an example plot of the CalVAD-based VMT surrogate for California in 2012, during the "AM Peak" (or "Morning Rush Hour"):

![CalVAD VMT for 2012 AM](resources/CA_809_4km_2012_ldv2016.png)

#### Linehaul VMT by Period

The [CalTrans CSTDM](http://www.dot.ca.gov/hq/tpp/offices/omsp/statewide_modeling/cstdm.html) is a link-level travel-demand model, run every five years using a huge variety of real data.  To produce a spatial surrogate for linehaul vehicles, the 2010 CSTDM outputs were obtained. Then, in ArcMap, the V10 vehicle class was extracted for all links labeled as "major highway" and these links were weighted by VMT.  

Eight of the EMFAC 2014 vehicle categories are defined by the EMFAC model as "linehaul vehicles":

* T6 CAIRP Heavy
* T6 Instate Heavy
* T6 OOS Heavy
* T7 CAIRP
* T7 NNOOS
* T7 NOOS
* T7 Single
* T7 Tractor

The result is probably the most reliable VMT spatial surrogate for linehaul vehicles that has ever been produced for California. Here is an example plot of the CSTDM-based VMT surrogate for California, during the "AM Peak" (or "Morning Rush Hour"):

![ARB Linehaul Surrogate AM](resources/CA_800AM_4km_2015_hdv2016.png)

#### City Population

Some on-road emissions are best distributed by human population. For instance, the multi-day diurnal resting loss from LDV traffic is probably best distributed by number of households since cars are generally parked at home when not in use for several days.

Older on-road gridding models, like DTIM, tried to use Census data to locate these emissions but Census data is done by "traffic analysis zone" or "census tract". In rural or mountainous regions, these areas can be as big as a whole county and this results in spatial allocation spread evenly over a large area. People living in these areas are concentrated in towns, not evenly distributed over mountains, desert, or barren lands.

To improve upon this, 2012 Census information on the number of households in each city was taken for the several hundred cities in California. That information was added to an ArcGIS shapefile of the city boundaries. The result was a shapefile that represents the population in cities. This file had to be edited slightly as the legal boundaries of coastal cities extend into the ocean three miles, which is inappropriate for on-road vehicle emissions. The ocean areas were trimmed off and the shapefile was weighted by the number of households per square area to create our "City Population" spatial surrogate:

![ARB City Population Surrogate](resources/ON_ROAD_CA_840_4km_2013.png)

#### Distribution Centers

This surrogate is not used directly as an ESTA default, but rather as part of combined surrogates with idling locations.

The Distribution Centers surrogate data set was originally the work of Cheryl Taylor at the ARB, along with several stakeholders in the California SIP Inventory Working group. In 2015, Cheryl accessed the data from ARB Equipment Registration (ARBER) data set. ARBER is an online registration program for the Transportation Refrigeration Unit and the Drayage Truck Regulations. In cooperation with Carolyn Craig and Rich Boyd of the ARBER team, a shapefile was created that included all kinds of distribution centers in California which likely have HDV traffic. 

#### Idling Locations

This surrogate is not used directly as an ESTA default, but rather as part of combined surrogates with distribution centers.

The data for this surrogate came from two sources:

1. The US EPA's "idling locations" dataset. This was retrieved from their [FTP servers](ftp://ftp.epa.gov/EmisInventory/2011v6/v2platform/spatial_surrogates/NewShapeFiles_Surrogatesv6.2_Platform.zip) in early April, 2016 as a shapefile.
2. The CalTrans "rest stops" dataset. This was retrieved as a shapefile from [their website](http://www.dot.ca.gov/hq/tsip/gis/datalibrary/Metadata/RestArea.html).

Many duplicate locations were found between the two datasets, and were removed. The results were combined into a shapefile. To weight which locations had more traffic than others, a detailed, manual hand-count was undertaken on Google Earth. The number of parking spots available, big enough for a single linehaul truck with trailer, were counted for each and every location from each of the above two data sets. The process was laborious, but yielded a shapefile that could be easily weight by the number of parking spots in each location.

The number of parking spots was determined to be the best way to represent how active an individual idling location or rest stop may be. This is thought to be the most analytical and complete comparison possible until such a time as extensive measurements are made of idling time at all of California's linehaul truck idling locations.

#### 30 Idle - 70 Dist

This surrogate is simply a weighted sum of 30 percent of the Idling Locations surrogate above with 70 percent of the Distribution Centers surrogate above. In counties or GAIs where neither surrogate has any value, the missing data is filled in with the CalTrans CSTDM linehaul trucking surrogate. This surrogate is used for the starting and idling emissions of heavy-duty diesel vehicles.

The 30/70 split was derived from the data on [this page](http://energy.gov/eere/vehicles/fact-917-march-21-2016-work-truck-daily-idle-time-industry) of the energy.gov website.

This source was used as a reference because the EMFAC team also used it as a reference, and it is useful that both models have the same underlying assumptions. The 30/70 split itself was derived by taking a weighted average of the time spent idling in different locations, across all non-line-haul heavy-duty vehicle types. The idling time range from 24-32%, for all types of HD trucks. So this surrogate is based on the time-split between how much idling is done at the kinds of places linehaul trucks idle versus other locations, like distribution centers.

![30/70 Idling Surrogate](resources/ON_ROAD_CA_853_4km_2017.png)

#### 90 idle - 10 dist

This surrogate is simply a weighted sum of 90 percent of the Idling Locations surrogate above with 10 percent of the Distribution Centers surrogate. In counties or GAIs where neither surrogate has any value, the missing data is filled in with the CalTrans CSTDM linehaul trucking surrogate. This surrogate is used for idling emissions from linehaul vehicles.

This 90/10 split was designed to match the EMFAC2014 assumption that nearly all of the idling done by linehaul trucks happens at places like truck stops. Linehaul truckers can, and frequently must, sleep in the cab of their vehicle with the engine running in an idling mode.

There is some discussion among ARB staff of whether 90/10 or 95/5 is a better split for this surrogate. Perhaps this ratio will change in the future versions of EMFAC. If so, it will be changed to match in ESTA.

![90/10 Idling Surrogate](resources/ON_ROAD_CA_859_4km_2017.png)


## Temporal Surrogates

Several temporal surrogates are included in ESTA. These are all examples of on-road temporal surrogates designed to disaggregate daily EMFAC emissions into hourly periods.


### CalVAD-Based On-Road Diurnal Temporal Surrogates

The [CalVAD database](https://www.arb.ca.gov/research/apr/past/11-316.pdf) uses a variety of real-life traffic measurements to build a real picture of the traffic at the link-level across California.  Because the CalTrans database uses real, measured traffic data, it is an extremely desirable ground-truth data set.  However, it does not include a lot of data about the vehicle types in traffic, as it is more expensive to collect that sort of data.  Since the number of wheel axes were recorded for each measured vehicle, it was determined that heavy-heavy-duty vehicles (HHDV) could easily be subtracted from the data set, leaving a very representative sample for light-duty vehicles (LDV) and light-medium-duty vehicles (LMDV).

Spatial surrogates were averaged from the entire 2012 data set, for six different representative days:

* Monday
* Tuesday-Wednesday-Thursday
* Friday
* Saturday
* Sunday
* Holiday

Thus, it was possible, for every county (or GAI) in California to generate a diurnal profile for three different vehicle categories and six different representative days of the week; for a total of 18 different temporal surrogates.

Upon discussion with the EMFAC team it was discovered that the EMFAC model assumes school busses only run for three hours in the morning and three hours in the evening on non-holiday weekdays. To keep the two models consistent, a fourth vehicle type was added to the Calvad surrogates, matching the assumptions in EMFAC.


### CalVAD-Based On-Road Day-of-Week Temporal Surrogates

Similar to the diurnal surrogates described above, day-of-week surrogates were built from the VMT recorded in the Calvad database. The reason day-of-week surrogates are needed is because the EMFAC model only estimates on-road emissions for "average weekday" traffic. The VMT used in EMFAC represents the average Tuesday, Wednesday, or Thursday, which is consistent with the use in transporation models. However, holidays and weekends frequently have far less traffic, and Fridays frequently have far more.

To accurately model on-road emissions, the VMT from each Calvad vehicle class was summed for all road network links in each GAI, by day-of-week. Then fractions were made for each day-of-the-week relative to the EMFAC representative Tuesday-through-Thursday. These fractions are then used to manually raise or lower the on-road emissions calculated by EMFAC to better match day-of-week traffic patterns.

Additionally, a minor change was made so that the school bus vehicle category has zero emissions on weekends and holidays.
