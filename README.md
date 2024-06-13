# POWER OUTAGE ANALYSIS
A UCSD DSC 80 Project, by Ethan Lau

## Introduction
### 1.1: General Overview
This project centralizes around the power outages dataset which contains a listing of 1,534 major power outages occuring between January of 2000 to July 2016. The outages reported in this data file affected a single U.S. state at the time of each outage occurrence.

### 1.2: Research Question
The primary question of research to which this project will analyze how geographical and urban charteristics of each state effect the restoration time of a power outage. Investigating how different demographic factors (such as urban vs. rural populations, population density, and percentage of urban population) correlate with power outage impacts (customers affected, duration) can help in understanding which communities are most vulnerable and need targeted interventions.

| Selected Column Name   | Description                                    | Variable Type   |
|-------------------------|------------------------------------------------|-----------------|
| OBS                     | Index                                          | Index           |
| YEAR                    | Indicates the year when the outage event occurred | Numeric         |
| MONTH                   | Indicates the month when the outage event occurred | Numeric         |
| U.S._STATE              | The U.S. state where the outage occurred      | Categorical     |
| POSTAL.CODE             | Postal code of the affected area               | Categorical     |
| NERC.REGION             | NERC (North American Electric Reliability Corporation) region code of the affected area | Categorical |
| CLIMATE.REGION          | U.S. Climate regions as specified by National Centers for Environmental Information | Categorical     |
| ANOMALY.LEVEL           | This represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season | Numeric |
| CLIMATE.CATEGORY        | This represents the climate episodes corresponding to the years | Categorical |
| CAUSE.CATEGORY          | Categories of all the events causing the major power outages | Categorical |
| OUTAGE.DURATION         | Duration of the outage in minutes              | Numeric         |
| CUSTOMERS.AFFECTED      | Number of customers affected by the outage     | Numeric         |
| RES.CUSTOMERS     | Annual number of customers served in the residential electricity sector of the U.S. state | Integer       |
| COM.CUSTOMERS     | Annual number of customers served in the commercial electricity sector of the U.S. state  | Integer       |
| IND.CUSTOMERS     | Annual number of customers served in the industrial electricity sector of the U.S. state  | Integer       |
| TOTAL.CUSTOMERS   | Annual number of total customers served in the U.S. state                                 | Integer       |
| RES.CUST.PCT      | Percent of residential customers served in the U.S. state (in %)                          | Float         |
| COM.CUST.PCT      | Percent of commercial customers served in the U.S. state (in %)                           | Float         |
| IND.CUST.PCT      | Percent of industrial customers served in the U.S. state (in %)                           | Float         |
| POPULATION              | Population in the U.S. state in a year          | Numeric         |
| POPPCT_URBAN            | Percentage of the total population of the U.S. state represented by the urban population | Percentage |
| POPPCT_UC               | Percentage of the total population of the U.S. state represented by the population of the urban clusters | Percentage |
| POPDEN_URBAN            | Population density of the urban areas (persons per square mile) | Numeric         |
| POPDEN_UC               | Population density of the urban clusters (persons per square mile) | Numeric         |
| AREAPCT_URBAN           | Percentage of the land area of the U.S. state represented by the land area of the urban areas | Percentage     |
| AREAPCT_UC              | Percentage of the land area of the U.S. state represented by the land area of the urban clusters | Percentage |
| PCT_LAND                | Percentage of land area in the U.S. state as compared to the overall land area in the continental U.S. | Percentage     |


## Data Cleaning and Exploratory Data Analysis
### 2.1: Data Cleaning
The process for which the data was cleaned is as follows:
1. Raw data xlsx excel file was converted into a CSV file to accomodate pandas' read_csv functionality.
2. Columns regarding outage start and end time/date were combined to form columns 'OUTAGE.START' and 'OUTAGE.RESTORATION' which are pandas timestamp types.
3. Previous columns regarding outage start and end time/date were subsequently dropped
4. All columns not pertinent to research question were dropped. The filtered dataset exists under variable name 'data'
5. A new column was created called AFFECTED_PCT which is the number of affected persons divided by total customers per state

### 2.2: Accessing Missingness
When OUTAGE.DURATION equals zero:
- Given that it would be sensible that out DEMAND.LOSS.MW and CUSTOMERS.AFFECTED would not exist if duration is zero, then any presence of outages being zero in the dataframe are not useful and are removed because an outage lasting 0 duration means it did not occur.
- Given OUTAGE.DURATION is our dependent variable, all NP.NAN values must be removed. Luckily, only 49 are missing.

In regards to the missingness of particular column values from out selected pool of information, eight contained nan values. They were accessed as follows:
- MONTH: Only 9 entries were missing. Given it would be difficult to assign an estimate to when this event would occur, these rows were removed as the number should have a significant effect on the size of the data. This also has the benefit of eliminating missingness of two other columns completely.
- CLIMATE.REGION: Only 5 values are missing. However, they occur only for Hawaii and Alaska given their location outside the relative structure of United States regional categorization. They will be given the terms "Tropical Pacific" and "Artic Pacific" respectively given the significant climate differences of their respect areas relative to the rest of the United States.
- CUSTOMERS.AFFECTED: It's difficult to access the missingness of customers affected. It seems to be not missing at random. To be discussed later.
- POPDEN_UC and POPDEN_RURAL: Both occur simultaneously and are likely missing due to not being collected. Given the minimal impact due to the few number, these were simply removed from the data.

It is important to note that OUTAGE.DURATION has been converted to hours.

<div style="overflow-x:auto;">
  <table style="border-collapse: collapse; width: 100%;">
    <thead style="background-color: #f2f2f2;">
      <tr>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">OBS</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">YEAR</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">MONTH</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">U.S. STATE</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">POSTAL CODE</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">NERC REGION</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">CLIMATE REGION</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">ANOMALY LEVEL</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">CLIMATE CATEGORY</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">CAUSE CATEGORY</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">OUTAGE DURATION</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">...</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">COM CUST PCT</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">IND CUST PCT</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">POPULATION</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">POPPCT URBAN</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">POPPCT UC</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">POPDEN URBAN</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">POPDEN UC</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">AREAPCT URBAN</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">AREAPCT UC</th>
        <th style="padding: 5px; border: 1px solid #ddd; text-align: left;">PCT LAND</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="padding: 5px; border: 1px solid #ddd;">1</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2011</td>
        <td style="padding: 5px; border: 1px solid #ddd;">7.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">Minnesota</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MN</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MRO</td>
        <td style="padding: 5px; border: 1px solid #ddd;">East North Central</td>
        <td style="padding: 5px; border: 1px solid #ddd;">-0.3</td>
        <td style="padding: 5px; border: 1px solid #ddd;">normal</td>
        <td style="padding: 5px; border: 1px solid #ddd;">severe weather</td>
        <td style="padding: 5px; border: 1px solid #ddd;">51.000000</td>
        <td style="padding: 5px; border: 1px solid #ddd;">...</td>
        <td style="padding: 5px; border: 1px solid #ddd;">10.644005</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.411181</td>
        <td style="padding: 5px; border: 1px solid #ddd;">5348119</td>
        <td style="padding: 5px; border: 1px solid #ddd;">73.27</td>
        <td style="padding: 5px; border: 1px solid #ddd;">15.28</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2279.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">1700.5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2.14</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.6</td>
        <td style="padding: 5px; border: 1px solid #ddd;">91.592666</td>
      </tr>
      <tr>
        <td style="padding: 5px; border: 1px solid #ddd;">2</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2014</td>
        <td style="padding: 5px; border: 1px solid #ddd;">5.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">Minnesota</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MN</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MRO</td>
        <td style="padding: 5px; border: 1px solid #ddd;">East North Central</td>
        <td style="padding: 5px; border: 1px solid #ddd;">-0.1</td>
        <td style="padding: 5px; border: 1px solid #ddd;">normal</td>
        <td style="padding: 5px; border: 1px solid #ddd;">intentional attack</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.016667</td>
        <td style="padding: 5px; border: 1px solid #ddd;">...</td>
        <td style="padding: 5px; border: 1px solid #ddd;">10.791609</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.374820</td>
        <td style="padding: 5px; border: 1px solid #ddd;">5457125</td>
        <td style="padding: 5px; border: 1px solid #ddd;">73.27</td>
        <td style="padding: 5px; border: 1px solid #ddd;">15.28</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2279.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">1700.5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2.14</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.6</td>
        <td style="padding: 5px; border: 1px solid #ddd;">91.592666</td>
      </tr>
      <tr>
        <td style="padding: 5px; border: 1px solid #ddd;">3</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2010</td>
        <td style="padding: 5px; border: 1px solid #ddd;">10.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">Minnesota</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MN</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MRO</td>
        <td style="padding: 5px; border: 1px solid #ddd;">East North Central</td>
        <td style="padding: 5px; border: 1px solid #ddd;">-1.5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">cold</td>
        <td style="padding: 5px; border: 1px solid #ddd;">severe weather</td>
        <td style="padding: 5px; border: 1px solid #ddd;">50.000000</td>
        <td style="padding: 5px; border: 1px solid #ddd;">...</td>
        <td style="padding: 5px; border: 1px solid #ddd;">10.687018</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.392361</td>
        <td style="padding: 5px; border: 1px solid #ddd;">5310903</td>
        <td style="padding: 5px; border: 1px solid #ddd;">73.27</td>
        <td style="padding: 5px; border: 1px solid #ddd;">15.28</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2279.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">1700.5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2.14</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.6</td>
        <td style="padding: 5px; border: 1px solid #ddd;">91.592666</td>
      </tr>
      <tr>
        <td style="padding: 5px; border: 1px solid #ddd;">4</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2012</td>
        <td style="padding: 5px; border: 1px solid #ddd;">6.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">Minnesota</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MN</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MRO</td>
        <td style="padding: 5px; border: 1px solid #ddd;">East North Central</td>
        <td style="padding: 5px; border: 1px solid #ddd;">-0.1</td>
        <td style="padding: 5px; border: 1px solid #ddd;">normal</td>
        <td style="padding: 5px; border: 1px solid #ddd;">severe weather</td>
        <td style="padding: 5px; border: 1px solid #ddd;">42.500000</td>
        <td style="padding: 5px; border: 1px solid #ddd;">...</td>
        <td style="padding: 5px; border: 1px solid #ddd;">10.682239</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.422355</td>
        <td style="padding: 5px; border: 1px solid #ddd;">5380443</td>
        <td style="padding: 5px; border: 1px solid #ddd;">73.27</td>
        <td style="padding: 5px; border: 1px solid #ddd;">15.28</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2279.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">1700.5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2.14</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.6</td>
        <td style="padding: 5px; border: 1px solid #ddd;">91.592666</td>
      </tr>
      <tr>
        <td style="padding: 5px; border: 1px solid #ddd;">5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2015</td>
        <td style="padding: 5px; border: 1px solid #ddd;">7.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">Minnesota</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MN</td>
        <td style="padding: 5px; border: 1px solid #ddd;">MRO</td>
        <td style="padding: 5px; border: 1px solid #ddd;">East North Central</td>
        <td style="padding: 5px; border: 1px solid #ddd;">1.2</td>
        <td style="padding: 5px; border: 1px solid #ddd;">warm</td>
        <td style="padding: 5px; border: 1px solid #ddd;">severe weather</td>
        <td style="padding: 5px; border: 1px solid #ddd;">29.000000</td>
        <td style="padding: 5px; border: 1px solid #ddd;">...</td>
        <td style="padding: 5px; border: 1px solid #ddd;">10.811320</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.367005</td>
        <td style="padding: 5px; border: 1px solid #ddd;">5489594</td>
        <td style="padding: 5px; border: 1px solid #ddd;">73.27</td>
        <td style="padding: 5px; border: 1px solid #ddd;">15.28</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2279.0</td>
        <td style="padding: 5px; border: 1px solid #ddd;">1700.5</td>
        <td style="padding: 5px; border: 1px solid #ddd;">2.14</td>
        <td style="padding: 5px; border: 1px solid #ddd;">0.6</td>
        <td style="padding: 5px; border: 1px solid #ddd;">91.592666</td>
      </tr>
    </tbody>
  </table>
</div>

### 1.4: Exploratory Data Analysis
Firstly, our dependent variable is outage duration. This can be calculated by subtracting the outage restoration datetime from the outage start datetime. However, this is redundant given the presence of variable OUTAGE.DURATION—note that both metrics have been verified as being equivalent.

1. Time Considerations:
    - As urbanization increases, the amount of locations requiring power also increase. This auguments the number of possible faliure points. As time increases, there is a noticible increase in the number of outages per year from 2018 onwards.
    - It seems that there are longer outages during the Fall and Winter months of the year on average, likely due to more inclimate weather. There are also higher quantaties of outages during said months of the year.

<iframe
  src="figs/plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

2. Cause differentiations:
    - When comparing the different causes for outages, weather is the most significant reason in quantity. It is also results on average in longer outages when compared to the other causes therefore this likely needs to be controlled for.

<iframe
  src="figs/plot2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

3. Number of Customers Affected:
    - There appears to be a somewhat inverse relationship between number of customers affected and outage duration. Likely, the larer number of customers affected, there is more incentive for the company to want to fix the outage. That being said, it is hardly a strong relationship given the pattern density being quite low

<iframe
  src="figs/plot3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="figs/plot3-5.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

4. Population Density
    - The weight of population affected by an outage seems to have a minimal affect.
    - Similar to # of customers affected, outage duration decreases as percentage of urban clusters affected increases. Similar justification to wanting to more expiendently deal with larger problems results in faster recovery.

<iframe
  src="figs/plot4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<div style="overflow-x: auto;">

| CLIMATE.REGION       | (-0.001, 0.00208] | (0.00208, 0.00871] | (0.00871, 0.0164] | (0.0164, 0.0243] | (0.0243, 0.0354] | (0.0354, 0.0633] | (0.0633, 1.055] |
|----------------------|-------------------|--------------------|-------------------|-------------------|-------------------|-------------------|------------------|
| Central              | 38.614%           | 37.659%            | 30.498%           | 52.065%           | 42.851%           | 72.933%           | 76.238%          |
| East North Central   | 92.340%           | 3.194%             | 65.379%           | 48.868%           | 69.617%           | 80.919%           | 77.249%          |
| Northeast            | 3.630%            | 74.265%            | 57.497%           | 62.440%           | 58.727%           | 80.088%           | 85.485%          |
| Northwest            | 5.534%            | 14.783%            | 97.583%           | 66.000%           | 39.413%           | 64.910%           | 148.097%         |
| South                | 15.548%           | 28.059%            | 45.348%           | 74.736%           | 57.126%           | 58.412%           | 156.722%         |
| Southeast            | 69.140%           | 11.717%            | 34.024%           | 14.966%           | 41.818%           | 44.206%           | 52.389%          |
| Southwest            | 6.611%            | 1.883%             | 3.400%            | 1.756%            | 40.417%           | 27.021%           | 12.979%          |
| West                 | 26.175%           | 41.262%            | 19.522%           | 22.511%           | 37.860%           | 52.617%           | 119.704%         |
| West North Central   | 0.000%            | -                  | 160.000%          | -                 | -                 | -                 | 3.708%           |

</div>

<div style="overflow-x: auto;">
  
| NERC.REGION        | ECAR       | RFC        | SERC       | SPP        | MRO        | NPCC       | WECC       | TRE        | FRCC       | FRCC, SERC |
|--------------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|
| CLIMATE.REGION     |            |            |            |            |            |            |            |            |            |            |
| Central            | 111.161    | 46.692     | 22.765     | 155.956    | -          | -          | -          | -          | -          | -          |
| East North Central | 84.726     | 108.236    | -          | -          | 47.551     | -          | -          | -          | -          | -          |
| Northeast          | -          | 45.148     | -          | -          | -          | 54.370     | -          | -          | -          | -          |
| Northwest          | -          | -          | -          | -          | -          | -          | 21.408     | -          | -          | -          |
| South              | 87.938     | 5.000      | 49.619     | 40.558     | 227.500    | -          | 6.353      | 49.343     | -          | -          |
| Southeast          | -          | 19.400     | 23.737     | -          | -          | -          | -          | -          | 71.185     | 6.200      |
| Southwest          | -          | 0.017      | -          | 1.267      | 35.667     | -          | 26.589     | -          | -          | -          |
| West               | -          | -          | 3.717      | -          | -          | -          | 27.254     | -          | -          | -          |
| West North Central | -          | 0.983      | -          | 2.650      | 29.383     | -          | 0.727      | -          | -          | -          |

</div>

## Assessment of Missingness

### 3.1: NMAR
One potential candidate for NMAR could be our dependent variable OUTAGE.DURATION. This variable indicates the duration of power outages, and its missingness might be directly related to the severity or type of the outage itself. For instance, if an outage was particularly short or insignificant, it might not have been reported or recorded with the same rigor as a more severe or longer-lasting outage. This means that the missingness of OUTAGE.DURATION could be inherently related to the value of OUTAGE.DURATION itself, making it NMAR.

To further investigate and possibly reclassify this missingness to MAR, additional data could be gathered on the reporting practices of different utility companies or regions. Understanding the criteria and circumstances under which certain outage durations are left unreported would help in assessing if there's an underlying pattern that explains the missingness.

### 3.2: Missingness Mechanism
We will analyze the missingness of OUTAGE.DURATION. This was done by running a permutation test against NERC.REGION and POPPCT_UC. If regional management affects reporting, this may give reason to possible missingness being a result of companies not wishing to report their failings. The ladder column was selected due to duration affected accuracy.

#### 3.2.1 NERC Region affect on Outage Duration
- Null Hypothesis: The distribution of Cause Category is the same when Duration is missing vs not missing.
- Alternate Hypothesis: The distribution of NERC Region is different when Duration is missing vs not missing.

There is an observed TVD of 0.315 with a pvalue of 0, indicating a strong rejection of the null hypothesis at a 95% confidence level. Therefore, the distribution of NERC Region is different when Duration is missing vs not missing.


<iframe
  src="figs/plotTVD.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


#### 3.2.2 Customers Affected on Outage Duration
- Null Hypothesis: The distribution of Customers Affected is the same when Duration is missing vs not missing.
- Alternate Hypothesis: The distribution of Customers Affected is different when Duration is missing vs not missing.

There is an observed Difference in Means of -20599.733 with a pvalue of 0.6181, indicating we cannot reject the null hypothesis at a 95% confidence level. Therefore, the distribution of Customers Affected is different when Duration is missing vs not missing.

<iframe
  src="figs/plotTVD2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

We will test whether the average outage duration differ between different climate regions. The relevant columns for this test are OUTAGE.DURATION and CLIMATE.REGION.
- Null Hypothesis (H0): The average outage duration is the same for all climate regions.
- Alternative Hypothesis (H1): The average outage duration is different for at least one climate region compared to others.

We will use the ANOVA (Analysis of Variance) test to compare the means of outage durations across different climate regions. ANOVA is suitable here because we are comparing the means of more than two groups.

Significance Level
We will use a significance level of 0.05.

The p-value is 0.0002, indicating we have a strong rejection of the null hypothesis at a 95% confidence level. Therefore, the average outage duration is different for at least one climate region compared to others.

<iframe
  src="figs/plotTVD3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>










## Framing a Prediction Problem

Prediction Problem: Predicting the duration of a power outage (OUTAGE.DURATION) based on various demographic and geographical characteristics of the affected area.

Type: Regression
Response Variable: OUTAGE.DURATION (Duration of the outage in minutes)


Justification for Response Variable:
OUTAGE.DURATION is chosen as the response variable because it directly measures the impact of a power outage in terms of its duration. Understanding the factors influencing outage duration can help utilities and policymakers in allocating resources more effectively for outage management and restoration efforts.


Features for Prediction:

Geographical Characteristics:
- POPULATION (Population in the U.S. state)
- POPPCT_URBAN (Percentage of the total population represented by the urban population)
- POPDEN_URBAN (Population density of the urban areas)
- AREAPCT_URBAN (Percentage of land area represented by the urban areas)
- PCT_LAND (Percentage of land area in the U.S. state)

Demographic Characteristics:
- RES.CUSTOMERS (Annual number of customers served in the residential electricity sector)
- COM.CUSTOMERS (Annual number of customers served in the commercial electricity sector)
- IND.CUSTOMERS (Annual number of customers served in the industrial electricity sector)
- TOTAL.CUSTOMERS (Annual number of total customers served)
- RES.CUST.PCT (Percentage of residential customers served)
- COM.CUST.PCT (Percentage of commercial customers served)
- IND.CUST.PCT (Percentage of industrial customers served)
- POPPCT_UC (Percentage of the total population represented by the population of the urban clusters)
- POPDEN_UC (Population density of the urban clusters)
- AREAPCT_UC (Percentage of land area represented by the urban clusters)

Justification for Features:
These features are chosen because they represent different aspects of geographical and demographic characteristics that may influence the restoration time of a power outage. For instance, population density and the proportion of urban population might affect the ease of access for repair crews and the complexity of the electrical infrastructure in an area, thus impacting outage duration.


Metric for Evaluation: Mean Absolute Error (MAE)
Justification for Metric: MAE is chosen as the evaluation metric because it provides a straightforward interpretation of the average magnitude of errors in the predicted outage duration. It is easy to understand and useful for comparing the performance of different models. Additionally, MAE is less sensitive to outliers compared to other metrics like Mean Squared Error (MSE).

## Baseline Model

For the baseline model, I'll use a simple linear regression model with two features: "POPULATION" and "POPPCT_URBAN". These features represent demographic and geographical characteristics that might influence the duration of a power outage.

Here's how I'll implement the baseline model using scikit-learn:

Feature Transformation: Since "U.S._STATE" and "NERC.REGION" are categorical variables, I'll encode them using one-hot encoding. For simplicity, I'll ignore other categorical variables for this baseline model.

Model Training: I'll use a simple linear regression model to predict the duration of a power outage based on the transformed features.


## Step 7: Final Model

We will improve upon the baseline model by engineering new features, encoding categorical variables, and performing hyperparameter tuning.

Categorical Encoding:
Encode the CLIMATE.REGION categorical feature using an appropriate method, such as One-Hot Encoding.

Pipeline Implementation:
Use a Pipeline from sklearn to ensure all steps (feature transformation, encoding, model training) are applied consistently.
Hyperparameter Tuning:

Use GridSearchCV to search for the best hyperparameters for the chosen model.
Tune parameters such as tree depth for decision trees or number of estimators for ensemble methods.

Model Evaluation:
Evaluate the final model using the same unseen and seen datasets as in the baseline model to ensure fair comparison.

Model Selection:
- Lasso with and without polynomial features
- Ridge with and without polynomial features











