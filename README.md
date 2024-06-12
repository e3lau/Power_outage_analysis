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

| OBS | YEAR | MONTH | U.S._STATE | POSTAL.CODE | NERC.REGION | CLIMATE.REGION | ANOMALY.LEVEL | CLIMATE.CATEGORY | CAUSE.CATEGORY | OUTAGE.DURATION | ... | COM.CUST.PCT | IND.CUST.PCT | POPULATION | POPPCT_URBAN | POPPCT_UC | POPDEN_URBAN | POPDEN_UC | AREAPCT_URBAN | AREAPCT_UC | PCT_LAND |
|-----|------|-------|-------------|--------------|--------------|-----------------|-----------------|------------------|----------------|------------------|-----|--------------|---------------|------------|---------------|------------|--------------|-----------|----------------|-------------|----------|
| 1   | 2011 | 7.0   | Minnesota   | MN           | MRO          | East North Central | -0.3            | normal           | severe weather | 51.000000        | ... | 10.644005    | 0.411181      | 5348119    | 73.27         | 15.28      | 2279.0       | 1700.5    | 2.14           | 0.6         | 91.592666 |
| 2   | 2014 | 5.0   | Minnesota   | MN           | MRO          | East North Central | -0.1            | normal           | intentional attack | 0.016667        | ... | 10.791609    | 0.374820      | 5457125    | 73.27         | 15.28      | 2279.0       | 1700.5    | 2.14           | 0.6         | 91.592666 |
| 3   | 2010 | 10.0  | Minnesota   | MN           | MRO          | East North Central | -1.5            | cold             | severe weather | 50.000000        | ... | 10.687018    | 0.392361      | 5310903    | 73.27         | 15.28      | 2279.0       | 1700.5    | 2.14           | 0.6         | 91.592666 |
| 4   | 2012 | 6.0   | Minnesota   | MN           | MRO          | East North Central | -0.1            | normal           | severe weather | 42.500000        | ... | 10.682239    | 0.422355      | 5380443    | 73.27         | 15.28      | 2279.0       | 1700.5    | 2.14           | 0.6         | 91.592666 |
| 5   | 2015 | 7.0   | Minnesota   | MN           | MRO          | East North Central | 1.2             | warm             | severe weather | 29.000000        | ... | 10.811320    | 0.367005      | 5489594    | 73.27         | 15.28      | 2279.0       | 1700.5    | 2.14           | 0.6         | 91.592666 |



### 1.4: Exploratory Data Analysis
Firstly, our dependent variable is outage duration. This can be calculated by subtracting the outage restoration datetime from the outage start datetime. However, this is redundant given the presence of variable OUTAGE.DURATION—note that both metrics have been verified as being equivalent.

1. Time Considerations:
    - As urbanization increases, the amount of locations requiring power also increase. This auguments the number of possible faliure points. As time increases, there is a noticible increase in the number of outages per year from 2018 onwards.
    - It seems that there are longer outages during the Fall and Winter months of the year on average, likely due to more inclimate weather. There are also higher quantaties of outages during said months of the year.
2. Cause differentiations:
    - When comparing the different causes for outages, weather is the most significant reason in quantity. It is also results on average in longer outages when compared to the other causes therefore this likely needs to be controlled for.
3. Number of Customers Affected:
    - There appears to be a somewhat inverse relationship between number of customers affected and outage duration. Likely, the larer number of customers affected, there is more incentive for the company to want to fix the outage. That being said, it is hardly a strong relationship given the pattern density being quite low
4. Population Density
    - The weight of population affected by an outage seems to have a minimal affect.
    - Similar to # of customers affected, outage duration decreases as percentage of urban clusters affected increases. Similar justification to wanting to more expiendently deal with larger problems results in faster recovery.
  

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
| West North Central   | 0.000%            | NaN                | 160.000%          | NaN               | NaN               | NaN               | 3.708%           |




| NERC.REGION        | ECAR       | RFC        | SERC       | SPP        | MRO        | NPCC       | WECC       | TRE        | FRCC       | FRCC, SERC |
|--------------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|
| CLIMATE.REGION     |            |            |            |            |            |            |            |            |            |            |
| Central            | 111.161    | 46.692     | 22.765     | 155.956    | NaN        | NaN        | NaN        | NaN        | NaN        | NaN        |
| East North Central | 84.726     | 108.236    | NaN        | NaN        | 47.551     | NaN        | NaN        | NaN        | NaN        | NaN        |
| Northeast          | NaN        | 45.148     | NaN        | NaN        | NaN        | 54.370     | NaN        | NaN        | NaN        | NaN        |
| Northwest          | NaN        | NaN        | NaN        | NaN        | NaN        | NaN        | 21.408     | NaN        | NaN        | NaN        |
| South              | 87.938     | 5.000      | 49.619     | 40.558     | 227.500    | NaN        | 6.353      | 49.343     | NaN        | NaN        |
| Southeast          | NaN        | 19.400     | 23.737     | NaN        | NaN        | NaN        | NaN        | NaN        | 71.185     | 6.200      |
| Southwest          | NaN        | 0.017      | NaN        | 1.267      | 35.667     | NaN        | 26.589     | NaN        | NaN        | NaN        |
| West               | NaN        | NaN        | 3.717      | NaN        | NaN        | NaN        | 27.254     | NaN        | NaN        | NaN        |
| West North Central | NaN        | 0.983      | NaN        | 2.650      | 29.383     | NaN        | 0.727      | NaN        | NaN        | NaN        |


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











