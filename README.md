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
| >CAUSE.CATEGORY |   String categories for causes of outages   |
| >DEMAND.LOSS.MW |   amount of peak demand lost during an outage event (in Megawatt)    |
| >TOTAL.CUSTOMERS |   int of total number of customers per state   |
| >PCT_WATER_INLAND |   Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %)   |
| >PCT_LAND |   Percentage of land area in the U.S. state as compared to the overall land area in the continental U.S. (in %)   |
| AREAPCT_UC |   Percentage of the land area of the U.S. state represented by the land area of the urban clusters (in %)   |


# Data Cleaning and Exploratory Data Analysis
## Data Cleaning
1. I first converted the existing columns >OUTAGE.START.DATE, >OUTAGE.START.TIME, and >OUTAGE.RESTORATION.DATE, >OUTAGE.RESTORATION.TIME into two timestamp columns in order to be useful for analysis, >OUTAGE.START and >OUTAGE.RESTORATION
2. I then dropped columns I determined were not suitable for analysis, either because they were not relevant or they contained too many missing values to be useful for analysis/prediction. 
	- Variables I decided not to use for analysis due to too many missing values were: CUSTOMERS.AFFECTED and DEMAND.LOSS.MW, missing 443 and 705 rows respectively. 
	- The variables I ended up keeping were:
		- Variables directly involved with answering research question: >US._STATE, >CLIMATE.REGION, >CLIMATE.CATEGORY, >CAUSE.CATEGORY, >DEMAND.LOSS.MW, >TOTAL.CUSTOMERS, >PCT_WATER_INLAND, >PCT_LAND, >AREAPCT_UC
		- Variables for learning more about the data: OUTAGE.START, OUTAGE.RESTORATION
3. I then replaced values of 0 in OUTAGE.DURATION with np.NaN, as a severe power outage will not be just 0 minutes. 

This led to my dataset appearing like: 


