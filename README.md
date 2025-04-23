<span style="font-size: 37px;">
<u>Power Outages: Predicting Customer Impact<u>

*By Sophia Satarino (fifisata@umich.edu)*
--

<br>
<span style="font-size: 28px;">Introduction

The dataset used in this analysis a is comprehensive collection of information related to major power outage events across the United States from January 2000 to July 2016. It looks at factors such as climate, topography, electricity usage, population, and land cover across various states. It is valuable for researchers aiming to build sustainable and reliable energy infrastructure to have information on how and why power outages occur. This exploratory analysis will delve into finding possible answers for the question:

> *"What are the characteristics of power outages with high severity?"* 

I will refer to outages with high severity as outages where larger amounts of people are impacted (dataset column: CUSTOMERS.AFFECTED). For reference, each row of the dataset has information pertaining to one outage instance, with associated column data such as State, Year, and Duration of the outage. There are 55 columns and 1534 rows of data. 

[Further information on the dataset can be found here](https://www.sciencedirect.com/science/article/pii/S2352340918307182).

<br>

---

<br>
<span style="font-size: 28px;">Data Cleaning and Exploratory Data Analysis

### <u>Data Cleaning<u>

There were a few steps I needed to take to make sure that the data was  prepared to be analyzed. First, I simply had to drop the first few rows which were completely empty of data from the dataset. I then needed to reset the indexes (column names) to match what they were originally supposed to be. I also replaced all missing -- or indicated as missing --  values to NaN for consistency. Finally, I dealt with the timestamp columns. In the dataset, there were two pairs of columns called OUTAGE.START.DATE, OUTAGE.START.TIME and OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME. I wanted to explore the potential factor of pairs of specific times/days when outages occur, so I combined these into OUTAGE.START and OUTAGE.RESTORATION. 

Below is the first few rows of the cleaned dataset with some of the columns I was interested in.


| YEAR | MONTH | U.S. STATE | OUTAGE START           | OUTAGE RESTORATION      | CAUSE CATEGORY    | CUSTOMERS AFFECTED |
|------|-------|------------|------------------------|-------------------------|-------------------|--------------------|
| 2011 | 7     | Minnesota  | July 1, 2011 5:00 PM   | July 3, 2011 8:00 PM    | NaN               | 70,000             |
| 2014 | 5     | Minnesota  | May 11, 2014 6:38 PM   | May 11, 2014 6:39 PM    | intentional attack| 70,000             |
| 2010 | 10    | Minnesota  | Oct 26, 2010 8:00 PM   | Oct 28, 2010 10:00 PM   | severe weather    | 68,200             |

### <u>Univariate Analysis<u>

<!-- ### Distribution of Customers Affected (Under 2M) -->

<iframe
  src="assets/customers-affected.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This histogram shows that the distribution of customers affected is skewed left. The two leftmost bars contain 650 out of 1534 observations. This means over 40% of the data falls within 0 - 99,000 customers affected. This means that the model will need to handle potentially imbalanced data, since most outages are small, and large outages are rare but impactful.

### <u>Bivariate Analysis<u>

<!-- ### Customers Affected by Cause Category -->

<iframe
  src="assets/customers-affected-bycause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

A similar overall trend about the distribution of customers affected is displayed in this box plot. Here, we can also observe that the trend of most of the observations lying at below 100,000 customers affected holds true across all Cause Categories.

### <u>Interesting Aggregates<u>

| CAUSE CATEGORY                |   count |          mean |   median |
|:------------------------------|--------:|--------------:|---------:|
| equipment failure             |      30 | 101936        |  45451.5 |
| fuel supply emergency         |       7 |      0.142857 |      0   |
| intentional attack            |     199 |   1790.53     |      0   |
| islanding                     |      34 |   6169.09     |   2342.5 |
| public appeal                 |      21 |   7618.76     |      0   |
| severe weather                |     716 | 188740        | 110716   |
| system operability disruption |      83 | 211066        |  69000   |

This grouped table brings together the amount of observed Cause Category instances and their associated mean/median in regards to Customers Affected. I was interesting in how many of each Causes were in the dataset. I also wanted to see the mean/median points for each category. 

### <u>Imputation<u>

I opted not to impute missing values. Specifically, for key variables of interest such as CUSTOMERS.AFFECTED, I observed high variance and lacked strong predictive features that could serve as reliable indicators for imputation. Given the absence of meaningful correlations or patterns in the available features, any imputed values would likely introduce noise rather than improve model performance. Therefore, I chose to retain missing values as NaN.

<br>

---
<br>
<span style="font-size: 28px;">Framing the Prediction Problem

I frame this as a regression problem to predict the number of customers affected by a major power outage (CUSTOMERS.AFFECTED) using only information known at the time the outage begins. This target variable is valuable for emergency preparedness and infrastructure allocation, especially in the face of widespread disruptions.

The features used to make this prediction include:

- *Cause Category*

    - (CAUSE.CATEGORY), the broad cause of the outage.


- *Utility Contribution*

    - (UTIL.CONTRI), the utility industry׳s contribution to the total GSP in the State (in percentage, and higher percentage means more invenstemnet in infrastructure).

These variables are all available at the moment the outage is reported, ensuring that our prediction is realistic and usable in practice. I explicitly exclude fields such as outage duration or restoration time, as they are not known at prediction time.

I evaluate model performance using Mean Absolute Error (MAE), which calculates the average absolute difference between predicted and actual values. I chose MAE because it is easily interpretable (in terms of number of people affected) and robust to large outliers, which are expected in the context of natural disasters or widespread infrastructure failures.

<br>

---

<br>
<span style="font-size: 28px;">Baseline Model

For our baseline, I implemented a LinearRegression model using a Pipeline to encapsulate both preprocessing and model training. I used two features:

CAUSE.CATEGORY (nominal): the broad cause of the outage, encoded using OneHotEncoder.

UTIL.CONTRI (quantitative): a numeric feature representing utility contribution, scaled using StandardScaler.

This results in a total of 1 quantitative and 1 nominal feature. No ordinal features were used in this baseline model.

All preprocessing steps were implemented using a ColumnTransformer, which applies the appropriate transformations to each feature type. These transformations were integrated into a single sklearn.Pipeline with the LinearRegression estimator.

After splitting the data into training and test sets (80/20), the model was trained and evaluated. The model achieved a Mean Absolute Error (MAE) of 101817.63 on the test set.

While the model is a decent starting point, I'm not very satisfied with the performance. The Mean Absolute Error (MAE) is about 101,817, which is quite high when compared to the median value of CUSTOMERS.AFFECTED (70,464) and even the mean (143,524). This suggests the model is often off by a large number of affected customers, which limits its usefulness for predicting purposes. Still, this baseline gives a useful reference to improve upon with more features and other model tuning.

<br>

---

<br>
<span style="font-size: 28px;">Final Model

For my final model, I aimed to improve upon the baseline linear regression by introducing two new features and switching to a more flexible modeling algorithm: a Random Forest Regressor.

### <u>New Features<u>

CUSTOMER.DENSITY: Calculated as TOTAL.CUSTOMERS / POPULATION. This feature gives a sense of how concentrated utility customers are in a region, which could relate to how widespread an outage might be.

RES.CUSTOMER.RATIO: Calculated as RES.CUSTOMERS / TOTAL.CUSTOMERS. This tells us what fraction of customers are residential, which might impact the number of people affected depending on the cause of the outage.

I chose these because they relate to population distribution and customer. These are factors that intuitively affect how outages play out.


### <u>Modeling and Tuning<u>

I used a RandomForestRegressor inside a Pipeline with a ColumnTransformer that:

  - One-hot encodes the categorical feature CAUSE.CATEGORY

  - Standard-scales the numeric features

I used GridSearchCV with 5-fold cross-validation to tune the following hyperparameters:

  - n_estimators: [50, 100]

  - max_depth: [5, 10, None]

  - min_samples_split: [2, 5]

This helped me systematically search for a model with the best performance in terms of Mean Absolute Error.


### <u>Results<u>

Best Hyperparameters:
n_estimators = 100, max_depth = 5, min_samples_split = 2

Final Model MAE: 101,258.87

This is a modest improvement over the baseline MAE of 101,817.63. While the drop is small (~1%), it shows that the added features and model tuning helped make predictions slightly more accurate.

<span>