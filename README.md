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

