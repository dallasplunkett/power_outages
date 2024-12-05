# Power Outages Analysis

Project for DSC 80 at UCSD

By Dallas Plunkett

## Introduction

This project analyzes a dataset of reported power outages across America from January 2000 to July 2016. It affected at least 50,000 customers and caused an energy demand loss of at least 300 MW. The data, sourced from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, includes outage details along with geographic, climatic, land-uses, electricity consumption, and economic characteristics for all 50 states of the U.S.

The analysis focuses on where, when, and for how long most power outages occurred while also exploring causes and interesting facts along the way. At the end, a few models are investigated to determine whether or not the duration of an outage could be predicted given a limited amount of information.

The structure of this report begins with the cleaning of the data to correct and narrow down the scope to be within the research domain. Following is an exploratory analysis to examine possible features, predictors, and patterns within the data to better understand the context of power outages as a whole. Then an assessment of missingness is conducted to determine mechanisms and dependencies. And lastly, prior to the prediction attempts, hypothesis testing is conducted to gauge the significance of outage durations between two significant states of interest.

The original dataset contains 1,534 rows and 57 columns, but this analysis will focus on just a subset of these columns. In particular, these are the columns of interest:

- `year` Year an outage occurred
- `month` Month an outage occurred
- `state` State an outage occurred in
- `nerc_region` North American Electric Reliability Corporation (NERC) region involved in the outage
- `climate_region` U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions)
- `anomaly_level` Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season
- `climate_category` Represents the climate episode—Warm, Normal or Cold—with a threshold of ± 0.5 °C defined by the Oceanic Niño Index (ONI)
- `cause_category` Categories of what caused the power outage
- `demand_loss_mw` Peak demand lost during outage (in Megawatts)
- `customers_affected` Number of customers affected by the outage
- `outage_start` Datetime (Year, Month, Day, Time) the outage began
- `outage_end` Datetime (Year, Month, Day, Time) the outage ended
- `outage_duration` Duration of outage (in Minutes)

## Data Cleaning

1. After importing the data I began by dropping columns that were not of interest: `OBS`, `variables`, `U.S._STATE`, `RES.PRICE`, `COM.PRICE`, `IND.PRICE`, `TOTAL.PRICE`, `RES.SALES`, `COM.SALES`, `IND.SALES`, `TOTAL.SALES`, `RES.PERCEN`, `COM.PERCEN`, `IND.PERCEN`, `RES.CUST.PCT`, `COM.CUST.PCT`, `IND.CUST.PCT`, `PC.REALGSP.STATE`, `PC.REALGSP.USA`, `PC.REALGSP.REL`, `PC.REALGSP.CHANGE`, `UTIL.REALGSP`, `TOTAL.REALGSP`, `UTIL.CONTRI`, `PI.UTIL.OFUSA`, `POPULATION`, `POPPCT_URBAN`, `POPPCT_UC`, `POPDEN_URBAN`, `POPDEN_UC`, `POPDEN_RURAL`, `AREAPCT_URBAN`, `AREAPCT_UC`, `PCT_LAND`, `PCT_WATER_TOT`, `PCT_WATER_INLAND`, `HURRICANE.NAMES`, `CAUSE.CATEGORY.DETAIL`, `RES.CUSTOMERS`, `COM.CUSTOMERS`, `IND.CUSTOMERS`, `TOTAL.CUSTOMERS`.
2. Next, I specified the appropriate data types for these columns: (`YEAR`, `MONTH`, `DEMAND.LOSS.MW`, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`) became 64 bit Integers; (`ANOMALY.LEVEL`) became 64 bit Floats; (`POSTAL.CODE`, `NERC.REGION`, `CLIMATE.CATEGORY`, `CAUSE.CATEGORY`) became Categorical; (`CLIMATE.REGION`) became a String.
3. Then I derived `OUTAGE.START` and `OUTAGE.END` datetimes from: `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE` and then dropping them.
4. Lastly, I renamed `POSTAL.CODE` to `STATE` and replaced all periods `.` with underscores `_` and lower cased all columns.

A random sample of the cleaned DataFrame can be seen below.

<!-- TODO -->

## Exploratory Data Analysis

Now that the dataset has been cleaned and appropriate data types assigned, effective analysis can begin. This section is broken down into three parts—univariate, bivariate and some interesting aggregates—so that the exploration phase in comprehensive and includes insights pertaining to the research question(s).

### Univariate Analysis

Within this univariate analysis I report two important distributions, but first, the questions that brought them to be.

How long do power outages tend to occur?

<!-- TODO -->

Here we can see that the distribution is Poisson in nature, indicating that most outages last only a short amount of time, while longer outages occur less and less often as time increases. This is common among durations, especially considering the nature of the data. Outages cause drastic issues in society and because of this, energy companies are often quite quick at bring back the power.

But what causes these power outages?

<!-- TODO -->

As we can see, severe weather is the most common reason for power outages to occur. What's interesting to me is that intentional attacks are the second most common reason at just over 400 occurrences within this dataset.

### Bivariate Analysis

For bivariate analysis, I report another two important distributions.

Where are these power outages occurring?

<!-- TODO -->

Here we can notice that California has a lot of power outages. Texas has the second highest followed by Washington in third. These places inhabit millions of people and thus millions of people are affected by power outages each and every year. Which leads me to my final question.

When are these power outages occurring?

<!-- TODO -->

While this graph may seem a bit intimidating, we can focus on where it is most blue. For instance, August of 2011 had 45 power outages. In fact, that year seems to have had quite a bit of outages when compared to other years. Unfortunately, there isn't much of a pattern—which was my intention with this graphic—yet provides some potential launching points for further investigation.

### Interesting Aggregates

To close the exploration phase, I'd like to share two interesting facts I found with some aggregates.

That Washington had 64 intentional attacks within this dataset.

<!-- TODO -->

And that Wisconsin had a power outage that occurred for just over 75 days.

<!-- TODO -->

## Assessment of Missingness

### NMAR Analysis

I suspect the `anomaly_level` to be Not Missing At Random (NMAR) as from what I can tell these are reliably uploaded on ONI's website. Thus, it is likely an acquisition issue or a reporting issue by the authors of the data.

With that said, additional data that I could have collected to determine if `anomaly_level` is Missing At Random (MAR) instead could be from the ONI website itself. This would have allowed me to determine if the massiveness depended on ONI or not.

### Missiness Dependency

Hypothesis Testing

## prediction Problem Framing

## Baseline Model

## Final Model

## Fairness Analysis