# Power Outages: Predicting Severity and Evaluating Causes
This was undertaken as a project for Data Science 80 at the University of California, San Diego. 
Author: Edward Lu

# Introduction
This project centers around the usage and analysis of the dataset "Data on major power outage events in the continental U.S." compiled by Mukherjee et al., 2018, accessed via the Purdue Laboratory for Advancing Sustainable Critical Infrastructure at https://engineering.purdue.edu/LASCI/research-data/outages. 

The dataset is a collection of major power outages in the continental United States from January of 2000 to July of 2016. Each row of the 1534 rows represents a major outage as classified by the Department of Energy, with 56 variables describing various aspects of each outage. For my evaluation of what characteristics are common among power outages with higher severity, I will be exploring only a subset of relevant variables among the 56, these being:

| Column | Description|
|----------|:--------:|
| US._STATE | String representing state of outage |
| CLIMATE.REGION    |   String describing the climate region classification of the area in which the outage occurred   |
| OUTAGE.DURATION | int outage duration in minutes |
| CLIMATE.CATEGORY |   String describing the climate classification of the area in which the outage occurred   |
| POPULATION |   int population of a state within a year  |
| CAUSE.CATEGORY |   String categories for causes of outages   |
| DEMAND.LOSS.MW |   amount of peak demand lost during an outage event (in Megawatt)    |
| TOTAL.CUSTOMERS |   int of total number of customers per state   |
| PCT_WATER_TOT |   Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %)   |
| PCT_WATER_INLAND |   Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %)   |
| PCT_LAND |   Percentage of land area in the U.S. state as compared to the overall land area in the continental U.S. (in %)   |
| AREAPCT_UC |   Percentage of the land area of the U.S. state represented by the land area of the urban clusters (in %)   |


# Data Cleaning and Exploratory Data Analysis
## Data Cleaning
1. I first converted the existing columns `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, and `OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME` into two timestamp columns in order to be useful for analysis, `OUTAGE.START` and `OUTAGE.RESTORATION`.
2. I then dropped columns I determined were not suitable for analysis, either because they were not relevant or they contained too many missing values to be useful for analysis/prediction.
   - The variables I ended up keeping were:
     - Variables directly involved with answering the research question: `US._STATE`, `CLIMATE.REGION`, `CLIMATE.CATEGORY`, `CAUSE.CATEGORY`, `DEMAND.LOSS.MW`, `TOTAL.CUSTOMERS`, `PCT_WATER_INLAND`, `PCT_LAND`, `AREAPCT_UC`, `PCT_WATER_TOT`, `POPULATION`
     - Variables for learning more about the data: `OUTAGE.START`, `OUTAGE.RESTORATION`, `CUSTOMERS.AFFECTED`, `DEMAND.LOSS.MW` (the latter two are relevant to the question but have too many missing values to be used for analysis, so I used them to understand other columns better).

3. I then replaced values of 0 in `OUTAGE.DURATION` with `np.NaN`, as a severe power outage will not be just 0 minutes.

This led to my dataset appearing like (only a select number of columns have been included for aesthetic purposes):

| U.S._STATE   | CLIMATE.REGION     | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   DEMAND.LOSS.MW |
|:-------------|:-------------------|:-------------------|:-------------------|------------------:|-----------------:|
| Minnesota    | East North Central | normal             | severe weather     |              3060 |              nan |
| Minnesota    | East North Central | normal             | intentional attack |                 1 |              nan |
| Minnesota    | East North Central | cold               | severe weather     |              3000 |              nan |
| Minnesota    | East North Central | normal             | severe weather     |              2550 |              nan |
| Minnesota    | East North Central | warm               | severe weather     |              1740 |              250 |


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
I then wanted to see the relationships between the percentage of water area in the state compared to the overall water in the U.S vs. the duration of an outage. We can see that a true linear trend does not arise, and the graph is not terribly useful. 
 <iframe
  src="assets/water_vs_outage.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I then wanted to see the severity of outages per climate region, as measured by outage duration by climate region. I visualized this with multiple boxplots, from which we can see that average outage duration is highest in East North Central, while lowest average outage duration seems to be in in the West North Central region. 
 <iframe
  src="assets/region_vs_dur.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Grouping and Aggregates

For my aggregation, I wanted a table that would show the average values of outage duration and customers affected per cause category per region, while also maintaining a count of how many instances of an outage type occurred in each region. To do this, I created a grouped dataframe indexed at the first level by cause category and at the second level by climate region. This is significant because it allows us to gauge the severity of causes in each region. For instance, we are able to see in the first few rows of the dataframe below that outage duration of outages caused by equipment failure is significantly longer in the East North Central area. 

|                                             |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   count |
|:--------------------------------------------|------------------:|---------------------:|--------:|
| ('equipment failure', 'Central')            |           322     |              87750   |       7 |
| ('equipment failure', 'East North Central') |         26435.3   |                nan   |       3 |
| ('equipment failure', 'Northeast')          |           269.75  |              38101   |       4 |
| ('equipment failure', 'Northwest')          |           702     |              46651.5 |       2 |
| ('equipment failure', 'South')              |           295.778 |              62721.7 |      10 |

# Assessment of Missingness

## NMAR Analysis

A variable in the data that I believe is likely NMAR is cause category. My reasoning is that cause category may not be reported by a local agency if the cause category itself was decided to be too sensitive to reveal or was a cause that is difficult enough to detect that it was not detected. This means that the missingness of cause category depends on the values of the column itself. To better explain the missingness and make it MAR, I would like to have data on the outage cause category reporting protocol and if it differs between states, as it is possible that different states/regions have different outage reporting protocols or infrastructure that leads to some outage causes being missing and others not. 

## MAR Analysis
To test for MAR, I will focus on the distribution of the missingness of `CUSTOMERS.AFFECTED`, which I believe is not trivial. 

### `CLIMATE.REGION`

**Null Hypothesis:** The distribution of `CLIMATE.REGION` is the same for when `CUSTOMERS.AFFECTED` is missing and when it is not missing, with any deviations being due to random chance. 

**Alternate Hypothesis:** The distribution of `CLIMATE.REGION` is DIFFERENT for when `CUSTOMERS.AFFECTED` is missing and when it is not missing. 

My test statistic will be tvd, and I will be performing a permutation test. 

 <iframe
  src="assets/region_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


In my permutation test, I found an observed TVD of 0.2605, which, after comparing to 1000 simulated TVDs, yielded a p-value of 0, which falls below my p-value threshold of 0.05. This means that I reject the null hypothesis in favor of the alternate hypothesis that the distribution of `CLIMATE.REGION` is indeed significantly different depending on the missingness of `CUSTOMERS.AFFECTED`, indicating that `CUSTOMERS.AFFECTED` is likely MAR dependent on `CLIMATE.REGION`. 

 <iframe
  src="assets/region_ptest.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### CLIMATE.CATEGORY

Similarly, I will now test on `CLIMATE.CATEGORY` to see if `CUSTOMERS.AFFECTED` is also MAR dependent on whether a climate is categorized as cold, hot, or normal. 

**Null Hypothesis:** The distribution of `CLIMATE.CATEGORY` is the same for when `CUSTOMERS.AFFECTED` is missing and when it is not missing, with any deviations being due to random chance. 

**Alternate Hypothesis:** The distribution of `CLIMATE.CATEGORY` is DIFFERENT for when `CUSTOMERS.AFFECTED` is missing and when it is not missing. 

My test statistic will be tvd, and I will be performing a permutation test. 


 <iframe
  src="assets/cat_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


In my permutation test, I found an observed TVD of 0.0349, which, after comparing to 1000 simulated TVDs, yielded a p-value of 0.373, which does not fall below my p-value threshold of 0.05. This means that I fail to reject the null hypothesis that the distribution of `CLIMATE.CATEGORY` is NOT significantly different depending on the missingness of `CUSTOMERS.AFFECTED`, indicating that `CUSTOMERS.AFFECTED` is most likely NOT MAR dependent on `CLIMATE.CATEGORY`. 

 <iframe
  src="assets/cat_ptest.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Hypothesis Testing

I want to investigate if the climate in which an outage occurs is a significant vector for outage severity in terms of outage duration. To do this, I will perofrm a hypothesis test testing if outage duration during warm weather outages is more or less than outage duration from cold weather outages to see if weather is an impactful factor in outage severity. This is a good way to answer my question because it splits outage duration between two broad climate classifications, warm or cold, which will inform on whether climate in general impacts outage duration. 

**Null:** Outage duration in cold regions is longer than outage duration in warm regions

**Alt:**  Outage duration in cold regions is shorter than outage duration in warm regions

**Test Statistic:** Difference in Group Means, Mean of Cold Regions minus Mean of Warm Regions

These hypotheses are 

I performed a permutation test in which I shuffled an indicator variable for whether an outage took place in a cold or warm climate category. My observed test statistic (difference in group means) was 63.976, indicating that the mean outage duration of cold climate outages was greater than that of warm climate outages. After performing 1000 permutations, I found a p-value of 0.28, above my p-value threshold of 0.05. This means that I fail to reject the null hypothesis that outage duration in cold regions is longer than outage duration in warm regions, and that it is highly likely that coldness of climate correlates to longer/more severe outages. 


 <iframe
  src="assets/hyp_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Prediction Modeling
The problem I want to solve via prediction is the issue of predicting outage severity in terms of outage duration with immediately knowable factors. 

The response variable of my model is thus `OUTAGE.DURATION`. I chose this framework as my prediction problem because I believe it is valuable for observers and policymakers to be able to factor in immediately available variables, such as climate region, outage duration, water percentage, and population to get quick estimates for how the severity of an outage in terms of its direct downtime. In addition, it has very few missing values. The metric I am using to gauge my model's accuracy is R^2. 

## Baseline Model
The features utilized in my baseline model are:

| Column | Description|
|----------|:--------:|
| CLIMATE.REGION    |   String describing the climate region classification of the area in which the outage occurred   |
| OUTAGE.DURATION | int outage duration in minutes |
| CLIMATE.CATEGORY |   String describing the climate classification of the area in which the outage occurred   |
| CAUSE.CATEGORY |   String categories for causes of outages   |
| TOTAL.CUSTOMERS |   int of total number of customers per state   |
| PCT_WATER_TOT |   Percentage of inland water area in the U.S. state as compared to the overall inland water area in the continental U.S. (in %)   |
| AREAPCT_UC |   Percentage of the land area of the U.S. state represented by the land area of the urban clusters (in %)   |

`CLIMATE.REGION`, `CLIMATE.CATEGORY`, and `CAUSE.CATEGORY` were the only nominal features, which I one-hot encoded for analysis via linear regression using a pipeline implementing the sklearn OneHotEncoder(). 

### Performance
I measured performance with R^2, though I have also included RMSE in the table below for a fuller picture. 

| Metric | Value|
|----------|:--------:|
| Train RMSE   |   6565.890789350957   |
| Test RMSE | 4316.823117558086 |
| Train R^2 | 0.00024112351941119048|
| Test R^2 | -0.004545917560775381 |

This baseline model performed extremely poorly by R^2 metrics, with a nearly non-zero correlation coefficient for both training and testing. I believe this current linear regression model is extremely poor. Not only is it not correlating with the training data, it is also not generalizing to unseen data (the test R^2). 

## Final Model
In my final model, I have switched from sklearn LinearRegression() to sklearn RandomForestRegressor(), a function that uses Random Forests for regression rather than classification. Random Forest Regression is a regression model that trains multiple decision trees during training and outputs the average prediction of those trees when presented with features to predict with. It is an ensemble learning model with hyperparameters such as max depth and the minimum amount of samples required to split a node. 

In addition to switching away from LinearRegression(), I have altered the numerical features present in the baseline model.
- I have first used the FunctionTransformer() to log transform `TOTAL.CUSTOMERS`, which I did in order to counter an extremely faint positive skew in the plot of `TOTAL.CUSTOMERS` and `OUTAGE.DURATION`. I believe this helped transform the data in a way that made it easier for the model to capture the relationship between the two variables. 
- I have also square root function transformed `PCT_LAND`, because when I plotted `PCT_LAND` on `OUTAGE.DURATION`, there were faint hints of a parabolic function that made me want to linearize the column. I believe this new scaled feature was a positive addition to prediction because it helped linearize the data, making it easier for the model to capture the relationship between `PCT_LAND` and `OUTAGE.DURATION`.
- The final transformation I made was to standard scale all quantitative variables, which I did to allow the model to converge faster and make it easier for the model to understand the importance of each feature in prediction. 

I then used GridSearchCV to determine the best parameters for my RandomForestRegressor.
- max_depth': None
- min_samples_leaf': 16
- min_samples_split': 5


Finally, I measured performance with R^2 once more. 
| Metric | Value|
|----------|:--------:|
| Train RMSE  5827.65933307443 |      
| Test RMSE | 3683.5793885981757 |
| Train R^2 |0.21241711159109478 |
| Test R^2 | 0.26855554151707384 |

Here we can see an immense improvement in my model's ability to capture the relationship between the given variables in the dataset and `OUTAGE.DURATION`. Correlation has soared up to .21 for training and .26 for test, while RMSE for both train and test have dropped. This final model's ability to predict `OUTAGE.DURATION` is a marked step up from the baseline model on the metric of R^2. 

# Fairness Analysis
My model seeks to predict outage duration given a variety of factors such as climate region, cause category, total customers in a state, etc. It's primary use case is evaluating the potential severity of an outage when it first occurs, without all the information. As such, it is imperative that it is effective for all demographics. In this case, I want to know if my model is fair in its predictions for two groups: areas with more vs. less total customers in a state. This way, I can tell if my model predicts fairly for states with less customers vs. areas with more customers, ensuring that neither one of these groups is favored with greater accuracy. 

The evaluation metric I will use is the difference between the R^2 for `TOTAL.CUSTOMERS` values greater than the median minus the R^2 for values less than the median, as my model is a regression, not a classifier. 

**Null Hypothesis:** The R^2 of predictions for outages in states with more customers than the median is the same as the R^2 for outages in states with less customers than the median, with any differences being due to chance. 

**Alternate Hypothesis:** The R^2 of predictions for outages with more customers than the median is greater than than for outages with less customers than the median. 

My observed R^2 difference came out to be 0.0188, which, after completing 1_000 shuffles for my permutation test, yielded a p-value of 0.402, which is far above my p-value threshold of 0.05. This means that I fail to reject the null hypothesis that the R^2 difference between outages in states with more or less customers in the data is not significantly different and the model performs similarly for both groups. My conclusion is that my model has most likely achieved R^2 parity, and these two groups are treated fairly by the model.

  <iframe
  src="assets/fair.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<div style="overflow-x: visible;">


