# Power-Outages-Regional-Analysis-and-Predictions
This was undertaken as a project for Data Science 80 at the University of California, San Diego. 
Author: Edward Lu

# Introduction
This project centers around the usage and analysis of the dataset "Data on major power outage events in the continental U.S." compiled by Mukherjee et al., 2018, accessed via the Purdue Laboratory for Advancing Sustainable Critical Infrastructure at https://engineering.purdue.edu/LASCI/research-data/outages. 

The dataset is a collection of major power outages in the continental United States from January of 2000 to July of 2016. Each row of the 1534 rows represents a major outage as classified by the Department of Energy, with 56 variables describing various aspects of each outage. For my evaluation of what characteristics are common among power outages with higher severity, I will be exploring only a subset of relevant variables among the 56, these being:

| Column | Description|
|----------|:--------:|
| >US._STATE | String representing state of outage |
| >CLIMATE.REGION    |   String describing the climate region classification of the area in which the outage occurred   |
| >OUTAGE.DURATION | int outage duration in minutes |
| >CLIMATE.CATEGORY |   String describing the climate classification of the area in which the outage occurred   |
| >POPULATION |   int population of a state within a year  |
| >CAUSE.CATEGORY |   String categories for causes of outages   |
| >DEMAND.LOSS.MW |   amount of peak demand lost during an outage event (in Megawatt)    |
| >TOTAL.CUSTOMERS |   int of total number of customers per state   |
| >PCT_WATER_TOT |   Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %)   |
| >PCT_WATER_INLAND |   Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %)   |
| >PCT_LAND |   Percentage of land area in the U.S. state as compared to the overall land area in the continental U.S. (in %)   |
| AREAPCT_UC |   Percentage of the land area of the U.S. state represented by the land area of the urban clusters (in %)   |


# Data Cleaning and Exploratory Data Analysis
## Data Cleaning
1. I first converted the existing columns >OUTAGE.START.DATE, >OUTAGE.START.TIME, and >OUTAGE.RESTORATION.DATE, >OUTAGE.RESTORATION.TIME into two timestamp columns in order to be useful for analysis, >OUTAGE.START and >OUTAGE.RESTORATION
2. I then dropped columns I determined were not suitable for analysis, either because they were not relevant or they contained too many missing values to be useful for analysis/prediction. 
- The variables I ended up keeping were:
	- Variables directly involved with answering research question: >US._STATE, >CLIMATE.REGION, >CLIMATE.CATEGORY, >CAUSE.CATEGORY, >DEMAND.LOSS.MW, >TOTAL.CUSTOMERS, >PCT_WATER_INLAND, >PCT_LAND, >AREAPCT_UC, >PCT_WATER_TOT, >POPULATION
	- Variables for learning more about the data: >OUTAGE.START, >OUTAGE.RESTORATION, >CUSTOMERS.AFFECTED, >DEMAND.LOSS.MW (the latter two are relevant to the question but have too many missing values to be used for analysis, so I used them to understand other columns better). 

3. I then replaced values of 0 in OUTAGE.DURATION with np.NaN, as a severe power outage will not be just 0 minutes. 

This led to my dataset appearing like:
| U.S._STATE   | CLIMATE.REGION     | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   TOTAL.CUSTOMERS |   POPULATION |   PCT_WATER_TOT |   CUSTOMERS.AFFECTED |   PCT_WATER_INLAND |   PCT_LAND |   AREAPCT_UC | OUTAGE.START        | OUTAGE.RESTORATION   |
|:-------------|:-------------------|:-------------------|:-------------------|------------------:|-----------------:|------------------:|-------------:|----------------:|---------------------:|-------------------:|-----------:|-------------:|:--------------------|:---------------------|
| Minnesota    | East North Central | normal             | severe weather     |              3060 |              nan |       2.5957e+06  |  5.34812e+06 |         8.40733 |                70000 |            5.47874 |    91.5927 |          0.6 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
| Minnesota    | East North Central | normal             | intentional attack |                 1 |              nan |       2.64074e+06 |  5.45712e+06 |         8.40733 |                  nan |            5.47874 |    91.5927 |          0.6 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
| Minnesota    | East North Central | cold               | severe weather     |              3000 |              nan |       2.58690e+06 |  5.3109e+06  |         8.40733 |                70000 |            5.47874 |    91.5927 |          0.6 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
| Minnesota    | East North Central | normal             | severe weather     |              2550 |              nan |       2.60681e+06 |  5.38044e+06 |         8.40733 |                68200 |            5.47874 |    91.5927 |          0.6 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
| Minnesota    | East North Central | warm               | severe weather     |              1740 |              250 |       2.67353e+06 |  5.48959e+06 |         8.40733 |               250000 |            5.47874 |    91.5927 |          0.6 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |


## Exploratory Data Analysis
### Univariate Analysis

Here, I wanted to take a look at how my variables of interest were distributed. 

Firstly, I wanted to take a look at the distribution of CLIMATE.REGION to get a general sense of how many outages happened in each climate region. Here, we can see that more outages occurred in the Northeast than any other region, followed by the South, West, and Central before leveling off. 

<iframe
  src="assets/outage_per_climate_region.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


I then wanted to see the amount of outages that occurred over time, plotting a time-series graph using >OUTAGE.START. Here, we can see that outage incidence steadily increased up until 2012, where it peaked before sharply declining to the most recent data point in 2016. 

<iframe
  src="assets/outage_over_time.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I then also wanted to see the distribution per state of outage incidence in a visual manner, so I visualized this with folium, where we can see the outage hotspots are in Rhode Island, Puerto Rico, and California. 

 <iframe
  src="assets/map.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
I then wanted to see the relationships between 









