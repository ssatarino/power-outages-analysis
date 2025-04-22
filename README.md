# Power Outages: Predicting Customer Impact #

*By Sophia Satarino (fifisata@umich.edu)*

---

## Introduction

The dataset used in this analysis a comprehensive collection of information related to major power outage events across the United States from January 2000 to July 2016. It looks at factors such as climate, topography, electricity usage, population, and land cover across various states. It is valuable for researchers aiming to build sustainable and reliable energy infrastructure to have information on how and why power outages occur. This exploratory analysis will delve into finding possible answers for "What are the characteristics of major power outages with higher severity?" We will refer to outages with high severity as outages where larger amounts of people are impacted (column: CUSTOMERS.AFFECTED). Each row of the dataset has information pertaining to one outage instance, with associated column data such as State, Year, and Duration of the outage. There are 55 columns and 1534 rows of data. 

[Further information on the dataset can be found here](https://www.sciencedirect.com/science/article/pii/S2352340918307182).

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

There were a view steps I needed to take to make sure that the data was  prepared to be analyzed. First, I simply had to drop the first few rows which were completely empty of data from the dataset. I then needed to reset the indexes (column names) to match what they were originally supposed to be. I also replaced all missing -- or indicated as missing --  values to NaN for consistency. Finally, I dealt with the timestamp columns. In the dataset, there were two pairs of columns called OUTAGE.START.DATE, OUTAGE.START.TIME and OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME. I wanted to explore the potential factor of pairs of specific times/days when outages occur, so I combined these into OUTAGE.START and OUTAGE.RESTORATION. 

Below is the first few rows of the cleaned dataset.
| YEAR | MONTH | U.S._STATE | OUTAGE.START           | OUTAGE.RESTORATION      | CAUSE.CATEGORY   | CUSTOMERS.AFFECTED |
|------|-------|------------|------------------------|-------------------------|------------------|--------------------|
| 2014 | 5     | Minnesota  | May 11, 2014 6:38 PM   | May 11, 2014 6:39 PM    | intentional attack| NaN                |
| 2010 | 10    | Minnesota  | Oct 26, 2010 8:00 PM   | Oct 28, 2010 10:00 PM   | severe weather   | 70,000             |
| 2012 | 6     | Minnesota  | Jun 19, 2012 4:30 AM   | Jun 20, 2012 11:00 PM   | severe weather   | 68,200             |

### Univariate Analysis

### Distribution of Customers Affected (Under 2M)

<iframe
  src="assets/customers-affected.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

### Customers Affected by Cause Category

<iframe
  src="assets/customers-affected-by-cause-category.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>




---

## Framing a Prediction Problem

Explain what you're trying to predict and what features you’re using. Why is it framed as regression or classification?

---

## Baseline Model

Talk about your first attempt at modeling — e.g., linear regression. Include evaluation metrics like MAE, MSE, etc.

---

## Final Model

Discuss your improved model. Mention feature engineering, tuning, model type, and performance gains over the baseline.
