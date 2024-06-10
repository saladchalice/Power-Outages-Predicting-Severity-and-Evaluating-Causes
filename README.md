# Power-Outages-Regional-Analysis-and-Predictions
This was undertaken as a project for Data Science 80 at the University of California, San Diego. 
Author: Edward Lu

# Introduction
This project centers around the usage and analysis of the dataset "Data on major power outage events in the continental U.S." compiled by Mukherjee et al., 2018, accessed via the Purdue Laboratory for Advancing Sustainable Critical Infrastructure at https://engineering.purdue.edu/LASCI/research-data/outages. 

The dataset is a collection of major power outages in the continental United States from January of 2000 to July of 2016. Each row of the 1534 rows represents a major outage as classified by the Department of Energy, with 56 variables describing various aspects of each outage. For my evaluation of what characteristics are common among power outages with higher severity, I will be exploring only a subset of relevant variables among the 56, these being:

| Column | Description|
|----------|:--------:|
| US._STATE | Centered |
| CLIMATE.REGION    |   Data   |
| CLIMATE.CATEGORY |   Data   |
| CAUSE.CATEGORY |   Data   |
