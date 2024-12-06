# Power Outages Analysis

Project for DSC 80 at UCSD

By Dallas Plunkett

## Introduction

This project analyzes a dataset of reported power outages across America from January 2000 to July 2016. It affected at least 50,000 customers and caused an energy demand loss of at least 300 MW. The data, sourced from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, includes outage details along with geographic, climatic, land-uses, electricity consumption, and economic characteristics for all 50 states of the U.S.

The analysis focuses on where, when, and for how long most power outages occurred while also exploring causes and interesting facts along the way. At the end, a few models are investigated to determine whether or not the duration of an outage could be predicted given a limited amount of information.

The structure of this report begins with the cleaning of the data to correct and narrow down the scope to be within the research domain. Following is an exploratory analysis to examine possible features, predictors, and patterns within the data to better understand the context of power outages as a whole. Then an assessment of missingness is conducted to determine mechanisms and dependencies. And lastly, prior to the prediction attempts, hypothesis testing is conducted to gauge the significance of outage durations between two significant states of interest.

The original dataset contains 1,534 rows and 57 columns, but this analysis will focus on just a subset of these columns. In particular, these are the columns of interest:

| variable             | description                                                                                                              |
| :------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| `year`               | Year an outage occurred                                                                                                  |
| `month`              | Month an outage occurred                                                                                                 |
| `state`              | State an outage occurred in                                                                                              |
| `nerc_region`        | North American Electric Reliability Corporation (NERC) region involved in the outage                                     |
| `climate_region`     | U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions)                          |
| `anomaly_level`      | Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season                                    |
| `climate_category`   | Represents the climate episode—Warm, Normal or Cold—with a threshold of ± 0.5 °C defined by the Oceanic Niño Index (ONI) |
| `cause_category`     | Categories of what caused the power outage                                                                               |
| `demand_loss_mw`     | Peak demand lost during outage (in Megawatts)                                                                            |
| `customers_affected` | Number of customers affected by the outage                                                                               |
| `outage_start`       | Datetime (Year, Month, Day, Time) the outage began                                                                       |
| `outage_end`         | Datetime (Year, Month, Day, Time) the outage ended                                                                       |
| `outage_duration`    | Duration of outage (in Minutes)                                                                                          |

## Data Cleaning

1. After importing the data I began by dropping columns that were not of interest: `OBS`, `variables`, `U.S._STATE`, `RES.PRICE`, `COM.PRICE`, `IND.PRICE`, `TOTAL.PRICE`, `RES.SALES`, `COM.SALES`, `IND.SALES`, `TOTAL.SALES`, `RES.PERCEN`, `COM.PERCEN`, `IND.PERCEN`, `RES.CUST.PCT`, `COM.CUST.PCT`, `IND.CUST.PCT`, `PC.REALGSP.STATE`, `PC.REALGSP.USA`, `PC.REALGSP.REL`, `PC.REALGSP.CHANGE`, `UTIL.REALGSP`, `TOTAL.REALGSP`, `UTIL.CONTRI`, `PI.UTIL.OFUSA`, `POPULATION`, `POPPCT_URBAN`, `POPPCT_UC`, `POPDEN_URBAN`, `POPDEN_UC`, `POPDEN_RURAL`, `AREAPCT_URBAN`, `AREAPCT_UC`, `PCT_LAND`, `PCT_WATER_TOT`, `PCT_WATER_INLAND`, `HURRICANE.NAMES`, `CAUSE.CATEGORY.DETAIL`, `RES.CUSTOMERS`, `COM.CUSTOMERS`, `IND.CUSTOMERS`, `TOTAL.CUSTOMERS`.
2. Next, I specified the appropriate data types for these columns: (`YEAR`, `MONTH`, `DEMAND.LOSS.MW`, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`) became 64 bit Integers; (`ANOMALY.LEVEL`) became 64 bit Floats; (`POSTAL.CODE`, `NERC.REGION`, `CLIMATE.CATEGORY`, `CAUSE.CATEGORY`) became Categorical; (`CLIMATE.REGION`) became Strings.
3. Then I derived `OUTAGE.START` and `OUTAGE.END` datetimes from: `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE` and then dropping them.
4. Lastly, I renamed `POSTAL.CODE` to `STATE` and replaced all periods `.` with underscores `_` and lower cased all columns.

A random sample of 5 rows of the cleaned DataFrame can be seen below.

| year | month | state | nerc_region | climate_region | anomaly_level | climate_category | cause_category        | outage_duration | demand_loss_mw | customers_affected | outage_start        | outage_end          |
| ---: | ----: | :---- | :---------- | :------------- | ------------: | :--------------- | :-------------------- | :-------------- | :------------- | :----------------- | :------------------ | :------------------ |
| 2007 |     6 | CA    | WECC        | West           |          -0.3 | normal           | fuel supply emergency | \<NA\>          | \<NA\>         | \<NA\>             | 2007-06-01 13:00:00 | NaT                 |
| 2016 |     4 | TX    | TRE         | South          |           1.1 | warm             | intentional attack    | 5               | 0              | 0                  | 2016-04-27 18:00:00 | 2016-04-27 18:05:00 |
| 2015 |     6 | KY    | SERC        | Central        |             1 | warm             | intentional attack    | 108             | 2              | 110                | 2015-06-01 00:27:00 | 2015-06-01 02:15:00 |
| 2012 |     7 | PA    | RFC         | Northeast      |           0.1 | normal           | severe weather        | 3894            | \<NA\>         | 64500              | 2012-07-07 06:06:00 | 2012-07-09 23:00:00 |
| 2007 |    10 | CA    | WECC        | West           |          -1.1 | cold             | severe weather        | 21              | 451            | 90323              | 2007-10-22 14:01:00 | 2007-10-22 14:22:00 |

## Exploratory Data Analysis

Now that the dataset has been cleaned and appropriate data types assigned, effective analysis can begin. This section is broken down into three parts—univariate, bivariate and some interesting aggregates—so that the exploration phase in comprehensive and includes insights pertaining to the research question(s).

### Univariate Analysis

Within this univariate analysis I report two important distributions, but first, the questions that brought them to be.

__How long do power outages tend to occur?__

<iframe
  src="assets/outages_duration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here we can see that the distribution is Poisson in nature, indicating that most outages last only a short amount of time, while longer outages occur less and less often as time increases. This is common among durations, especially considering the nature of the data. Outages cause drastic issues in society and because of this, energy companies are often quite quick at bringing back on the power.

__But what causes these power outages?__

<iframe
  src="assets/cause_category.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As we can see, severe weather is the most common reason for power outages to occur. What's interesting to me is that intentional attacks are the second most common reason at just over 400 occurrences within this dataset.

### Bivariate Analysis

For bivariate analysis, I report another two important distributions investigating the geographic and temporal aspects of power outages.

__Where are these power outages occurring?__

<iframe
  src="assets/outages_by_state.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We can notice that California has a lot of power outages, Texas has the second highest, followed by Washington in third. These places inhabit millions of people and thus millions of people were affected by power outages each and every year. Which leads me to my final question.

__When are these power outages occurring?__

<iframe
  src="assets/outages_by_month_and_year.html"
  width="800"
  height="1200"
  frameborder="0"
></iframe>

While this graph may seem a bit intimidating, we can focus on where it is most blue. For instance, August of 2011 had 45 power outages. In fact, that year seems to have had quite a bit of outages when compared to other years. Unfortunately, there isn't much of a pattern—which was my intention with this graphic—yet provides some potential launching points for further investigation.

### Interesting Aggregates

To close the exploration phase, I'd like to share two interesting facts I found with some aggregates.

First, that Washington had 64 intentional attacks within this dataset. To get this fact I created a pivot table with the `state` and `causes_category` aggregating by size. I then sorted the table by intentional attacks in descending order. This is significant because it shows were increased security may be needed.

| state | equipment failure | fuel supply emergency | intentional attack | islanding | public appeal | severe weather | system operability disruption |
| :---- | ----------------: | --------------------: | -----------------: | --------: | ------------: | -------------: | ----------------------------: |
| WA    |                 1 |                     1 |             __64__ |         5 |             1 |             24 |                             1 |
| DE    |                 1 |                     0 |                 37 |         0 |             0 |              2 |                             1 |
| UT    |                 1 |                     0 |                 35 |         0 |             1 |              2 |                             2 |
| MD    |                 0 |                     0 |                 25 |         0 |             0 |             32 |                             1 |
| CA    |                21 |                    17 |                 24 |        28 |             9 |             70 |                            41 |

Second, that Wisconsin had a power outage that occurred for just over 75 days. To get this fact I grouped by `state` and selected `outage_duration`, aggregating by the maximum duration per state. From there I sorted the values in descending order and converted the scale of max outage_durations to days.

| state | outage_duration (days) |
| :---- | ---------------------: |
| WI    |            __75.4535__ |
| MI    |                54.4285 |
| NY    |                     42 |
| CA    |                34.3243 |
| AZ    |                  34.25 |

## Assessment of Missingness

In this section I investigate missing values and whether or not their columns have some pattern of dependency.

### NMAR Analysis

I suspect `demand_loss_mw` to be Not Missing At Random (NMAR) as from what I can tell these are from a range of utility companies with a few different kinds of reporting standards. Thus, it is hard to determine the true reason for their missingness and adds complexity when attempting to aggregate such metrics. So, I suspect this column to be error prone and lacking in reliability.

With that said, additional data corresponding to the specific utility company that is reporting `demand_loss_mw` per outage would have allowed me to determine if this variable was actually Missing At Random (MAR) as I could have ran dependency checks to gauge possibilities such as specific companies regularly not reporting data when demand loss is high.

### Missingness Dependency

To determine missingness dependencies I may have, I chose to focus on the distribution of `outage_duration`. I test against the columns `climate_category` and `cause_category`.

I first examined the `climate_category` when `outage_duration` is missing and not missing. Below is the specific hypothesis tested.

- __Null Hypothesis:__ The distribution of `climate_category` is the same when `outage_duration` is missing and not missing.
- __Alternate Hypothesis:__ The distribution of `climate_category` is different when `outage_duration` is missing and not missing.

<iframe
  src="assets/climate_category_by_outage_duration_missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above we can see the proportion of warm, normal and cold climates when `outage_duration` is missing and not missing. I then conducted a permutation test to gauge the significance of such a scenario assuming the null hypothesis is true. My observed Total Variation Distance (TVD)—which is a measure of the difference between two categorical distributions—is about 0.0624 with a p-value of 0.0000. This means I reject the null hypothesis indicating that the missingness of `outage_duration` is likely to be __dependent__ on the `climate_category`.

<iframe
  src="assets/climate_category_by_outage_duration_tvd_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I then examined the `cause_category` when `outage_duration` is missing and not missing. Below is the hypothesis tested.

- __Null Hypothesis:__ The distribution of `cause_category` is the same when `outage_duration` is missing and not missing.
- __Alternate Hypothesis:__ The distribution of `cause_category` is different when `outage_duration` is missing and not missing.

<iframe
  src="assets/cause_category_by_outage_duration_missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above we can see the proportion of causes when `outage_duration` is missing and not missing. As before, I conducted a permutation test to verify it's significance given the null hypothesis is true and used the TVD test statistic. Here, I observed a TVD of 0.0066 with a p-value of 0.7220. So, in this case I retain the null hypothesis as being likely which indicates that in the `outage_duration` missingness is likely __independent__ of the `cause_category`.

<iframe
  src="assets/cause_category_by_outage_duration_tvd_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

As one of my primary focuses is on the duration of outages, I wanted to further investigate the distributional differences between two prominent states: California and Texas. Below is the specific hypothesis used.

- __Null Hypothesis:__ The distribution of `outage_duration` is the same for California and Texas.
- __Alternative Hypothesis:__ The distribution of `outage_duration` is NOT the same for California and Texas.

<iframe
  src="assets/outage_duration_by_state_ks_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

To begin I used the Kolmogorov-Smirnov (KS) test statistic, which compares two Cumulative Distribution Functions (CDFs) to determine if they are significantly different. I think this is the most appropriate statistic as I am concerned with the similarity of two continuous Poisson distributions. I observed a KS of 0.2517 with a p-value of 0.0000. Thus, I reject the null hypothesis indicating that the distributional difference of `outage_duration` between California and Texas are at least statistically significant.

## Framing a Prediction Problem

At first, I wanted to try my hand at some form of a Linear Regression model. However, given all of the insights up to this point, I think it would be best to utilize a model that allows for more flexibility and complexity with non-linear dynamics. Thus, I will be using Decision Trees throughout the modeling attempts below.

I want to predict `outage_duration`. This is a regression problem as `outage_duration` is a numeric variable. The metrics I will be using to evaluate the models will be the Root Mean Squared Error (RMSE) and R-squared. These are good metrics as RMSE allows for easy interoperability and R-squared gives us an idea of the model's goodness of fit. At the time of prediction, we will utilize only `outage_start`, `state`, and `anomaly_level`. My hope is that with this limited information we may be able to generalize to some real-time applicable model for determining how long a power outage is likely to last based on its initial conditions.

## Baseline Model

The base model used is a Decision Tree Regressor. The features are `month`, `day`, `hour`, `state`, and `anomaly_level`. In order, the first four variables are nominal, and the last is quantitative. `month`, `day`, and `hour` are first derived from the `outage_start` variable with a function transformer, then one-hot encoded as I wanted to minimize any temporal correlation. I also one-hot encoded state to minimize any geographical correlations.

It performed decently with an RMSE of 6528.8336, or a range of about 4.5 days off on average. However, the R-squared was -0.7851 indicating a poor goodness of fit for the baseline model. Thus, I would not put much faith into this model as negative R-squared scores are not good.

## Final Model

In my final model I also used a Decision Tree Regressor. But this time I chose to try different features. For `outage_start` I derived sine and cosine variants of `month`, `day`, and `hour` to once again minimize the amount of temporal influence (in this case any seasonality that may be present) that my model had to deal with. For this model, these features remained quantitative. I kept the one-hot encoding for `state` to maintain the nominal nature of the variable. And then I performed a standard scaling on `anomaly_level` to eliminate any imbalances due to the scale of climate information.

I then conducted a grid search to determine the optimal hyperparameter's with a max depth of 2, minimum samples leaf of 8, and a minimum samples split of 2. Given all this, I observed a RMSE of 4719.40, or a range of about 3.3 days off on average and a R-squared of 0.07. Thus, an accuracy improvement of about a day was seen in the final model, and the goodness of such a model had became positive. Regardless, this R-squared is close to 0, which still indicates a poor fit overall. For reference, a perfect fit is 1.

## Fairness Analysis

To conclude, I investigated the fairness of my final model. To do this, I wanted to see the final model performed the same for warmer and colder climates. In particular, I split the data such that an `anomaly_level` greater than 0 was warmer, and less than or equal to 0 was colder. To gauge the fairness I conducted a permutation test to see if the RMSE difference between these groups was statistically significant. Below is exact hypothesis test used.

- __Null Hypothesis:__ The model's performance is fair (i.e RMSE for warmer climate periods `anomaly_level > 0` and colder climate periods `anomaly_level <= 0` is the same, and any observed difference is due to random chance)
- __Alternative Hypothesis:__ The model's performance is unfair (i.e. RMSE for warmer climate periods `anomaly_level > 0` and colder climate periods `anomaly_level <= 0` is significantly different, and this difference is not due to random chance)

<iframe
  src="assets/fairness_rmse_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

What I observed was an absolute RMSE difference of 3717.1445, or an absolute difference range of about 2.6 days, and a p-value of 0.0160. This means that the null hypothesis is unlikely to be true, but not by much. Ultimately, this indicates that my model likely faces fairness issues when it comes to warmer and colder climate conditions.
