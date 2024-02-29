<div style="border-radius: 10px; padding: 15px; background-color: rgba(173, 216, 230, 0.5); text-align: center;">
    <h1 align="center"><font color="#9B60A1">Sales Dynamics and </font></h1> 
    <h1 align="center"><font color="#9B60A1">Shipping Efficiency Analysis</font></h1>
</div>

## Table of Contents
1. [Data Extraction and Preparation (SQL)](#data-extraction-and-preparation-sql)
   - [Step 1: Data Extraction](#step-1-data-extraction)
   - [Step 2: Data Cleaning](#step-2-data-cleaning)
2. [Preliminary Data Analysis](#preliminary-data-analysis)
3. [Feature Engineering](#feature-engineering)
4. [A/B Testing (Python)](#ab-testing)
   - [Step 1: Importing Required Libraries](#libraries)
   - [Step 2: Establishing Database Connections](#connections)
   - [Step 3: Data Extraction](#extraction)
   - [Step 4: Data Analysis](#analysispython)
   - [Step 5: Data Cleaning](#cleaning)
   - [Step 6: Setting Up the Test](#setup)
   - [Step 7: Conducting the Test](#test)
   - [Step 8: Insights Based on the Test](#insights)
5. [PowerBI Report for Sales Analysis](#sales-analysis)
6. [PowerBI Report for Shipping Day Analysis](#ship-day-analysis)
  
   
<div style="border-radius: 10px; padding: 15px; background-color: rgba(173, 216, 230, 0.5);">
    <h2 align="left"><font color="#9B60A1">Introduction:</font></h2> 
    <p>In this data-driven project, we delve into the sales and shipping data of a retail superstore to uncover insights that can drive better business decisions. The project is structured around the extraction, analysis, and visualization of key data points, including sales figures, customer segments, and shipping details. A focal point of our investigation is the A/B testing conducted to understand the impact of different shipping modes on sales performance. Our aim is to answer critical business questions: Does faster shipping lead to higher sales? Are there discernible patterns in purchasing behaviors across different customer segments? By integrating SQL for data extraction, Python for rigorous data analysis, including A/B testing, and PowerBI for dynamic visualization, this project presents a holistic approach to understanding the intricate dynamics of retail operations. The insights gained from this analysis are intended to guide strategic decisions, particularly around shipping policies and customer engagement strategies. By examining the causal relationship between shipping speed and sales through A/B testing, we provide actionable recommendations that could potentially enhance customer satisfaction and optimize operational efficiency.</p>
</div>


<div style="border-radius: 10px; padding: 15px; background-color: rgba(173, 216, 230, 0.5);">
    <h2 align="left"><font color="#9B60A1">Prerequisites:</font></h2> 
    <ul>
        <li>PostgreSQL 14</li>
        <li>Python 3.9.13</li>
        <li>Pandas library (Python)</li>
        <li>SQLAlchemy library (Python)</li>
        <li>SciPy library (Python)</li>
    </ul>
</div>

<h2 id="data-extraction-and-preparation-sql"><font color="#9B60A1">Data Extraction and Preparation (SQL)</font></h2>

### <h2 id ="step-1-data-extraction" align="left"><font color="#9B60A1">Step 1: Data Extraction</font></h2>

<div style="border-radius: 10px; padding: 15px; background-color: rgba(173, 216, 230, 0.5); font-size: 130%; text-align: left;">

<h2 align="left"><font color="#9B60A1">Problem:</font></h2>
We start by selecting relevant columns from the superstore database that are crucial for our sales analysis. The selected columns include information about order dates, sales amounts, customer segments, product categories, geographical details, and more.  
</div>

```
SELECT
    Order_Date, Sales, Segment, Category, Sub_Category, 
    City, State, Region, Ship_Date, Order_ID, Customer_ID, 
    Customer_Name, Country, Postal_Code, Product_ID, 
    Product_Name, Ship_Mode
FROM 
    superstore;

```

### <h2 id = "step-2-data-cleaning" align="left"><font color="#9B60A1">Step 2: Data Cleaning</font></h2>
<div style="border-radius: 10px; padding: 15px; background-color: rgba(173, 216, 230, 0.5);">
Our next step involves cleaning the data to ensure accuracy and consistency in our analysis. This includes standardizing date formats, removing null values, and eliminating duplicate records.
</div>

- __Standardize Date Columns:__ We standardize the Order_Date and Ship_Date columns to the DATE type for consistency.
  
```
ALTER TABLE superstore
ALTER COLUMN Order_Date TYPE DATE USING to_date(Order_Date, 'DD/MM/YYYY'),
ALTER COLUMN Ship_Date TYPE DATE USING to_date(Ship_Date, 'DD/MM/YYYY');
```

- __Remove Null Values:__ This step involves filtering out any records that contain null values in key columns.
  
```
DELETE FROM superstore
WHERE Order_Date IS NULL OR Sales IS NULL;
```

<h2 id="preliminary-data-analysis"><font color="#9B60A1">Preliminary Data Analysis (SQL)</font></h2>
<div style="border-radius: 10px; padding: 15px; background-color: rgba(173, 216, 230, 0.5); font-size: 100%; text-align: left;">
Using SQL, we perform preliminary data analysis to understand the distribution of sales across different segments, categories, and over time.
</div>

- __Group by Month for Time Series Analysis:__ Analyzing total sales by month helps in understanding the sales trend.
```
SELECT 
    EXTRACT(Month FROM Order_Date) as Month, 
    ROUND(SUM(Sales)) as Total_Sales 
FROM 
    superstore 
GROUP BY 
    Month 
ORDER BY 
    Month;
```
```
Output
1	94069
2	57261
3	189020
4	135694
5	151036
6	137350
7	137202
8	153996
9	294607
10	186668
11	336225
12	312126
```


- __Group by Year for Time Series Analysis:__ Analyzing total sales year by year helps in understanding the sales trend.
  
```
SELECT 
    EXTRACT(YEAR FROM Order_Date) as Year, 
    ROUND(SUM(Sales)) as Total_Sales 
FROM 
    superstore 
GROUP BY 
    Year 
ORDER BY 
    Year;
```
```
# Output
2015	469906
2016	436541
2017	583900
2018	694904
```


- __Analysis by Regions:__ Understanding sales performance across different regions, states, cities.
```
SELECT Region, SUM(Sales) AS Total_Sales
FROM superstore
GROUP BY Region
ORDER BY Total_Sales DESC;
```
```
# Output
"West"	691663.6565
"East"	649973.644
"Central"	480307.6132
"South"	363306.7250
```

```
SELECT Region, ROUND(SUM(Sales)) AS Total_Sales
FROM superstore
GROUP BY Region
ORDER BY Total_Sales DESC;
```
```
# Output
"California"	437914
"New York"	292497
"Texas"	163256
"Washington"	128646
"Pennsylvania"	116177
```

- __Analysis by Customer Segements:__ Understanding sales performance across different customer segments.
```
SELECT 
    Segment, 
    SUM(Sales) as Total_Sales, 
    AVG(Sales) as Average_Sales 
FROM 
    superstore 
GROUP BY 
    Segment;
```
```
# Output
"Consumer"	1116690.8760	226.5093054766734280
"Corporate"	655576.8718	228.5036151272220286
"Home Office"	412983.8909	245.5314452437574316
```

### <h2 id = "feature-engineering" align="left"><font color="#9B60A1">Feature Engineering</font></h2>
- __New Date Columns:__ Creating year and month columns indicates order_year and order month
```
ALTER TABLE superstore
ADD order_year INT;
```
```
ALTER TABLE superstore
ADD order_month INT;
```
```
UPDATE superstore
SET 
    order_year = EXTRACT(YEAR FROM order_date), 
    order_month = EXTRACT(MONTH FROM order_date);

-- Create columns that calculates number of business days for shipping
ALTER TABLE superstore
ADD COLUMN shipping_business_days INTEGER;
```
- __Shipping Business Days:__ Creating shipping business days by using ship date and order date
```
UPDATE superstore
SET shipping_business_days = 
    CASE 
        WHEN order_date <= ship_date THEN 
            CASE 
                WHEN EXTRACT(DOW FROM order_date) IN (0,6) THEN (ship_date - order_date) - 2 
                ELSE (ship_date - order_date) + 1 
            END
        ELSE NULL 
    END;
```
- __Logical Test:__ Shipping days can not be negative
```
DELETE FROM superstore
WHERE shipping_business_days < 0;
```

- __Average Shipping Days by Ship Modes:__ 
```
SELECT 
    ship_mode,
    AVG(shipping_business_days) AS avg_shipping_business_days
FROM 
    superstore
GROUP BY 
    ship_mode;
```
```
# Output
"Second Class"	3.2359136387572407
"Standard Class"	4.9127241673783091
"Same Day"	1.0502958579881657
"First Class"	2.4398848092152628
```
- __Average Shipping Days by Segments:__ 
```
SELECT 
    segment,
    AVG(shipping_business_days) AS avg_shipping_business_days
FROM 
    superstore
GROUP BY 
    segment;
```
```
# Output
"Consumer"	4.0472616632860041
"Corporate"	4.0805158591843848
"Home Office"	4.1575505350772889
```
- __Average Shipping Days by Categories:__ 
```
SELECT 
    category,
    AVG(shipping_business_days) AS avg_shipping_business_days
FROM 
    superstore
GROUP BY 
    category;
```
```
# Output
"Furniture"	4.0882793017456359
"Office Supplies"	4.0768153980752406
"Technology"	4.0641680863145940
```

<h2 id = "ab-testing" align="left"><font color="#9B60A1">A/B Testing (Python)</font></h2> 

<h2 id = "libraries" align="left"><font color="#9B60A1">Step 1: Importing Required Libraries</font></h2>

```
import pandas as pd
from sqlalchemy import create_engine
from scipy import stats
import getpass
```

<h2 id = "connections" align="left"><font color="#9B60A1">Step 2: Establishing Database Connection</font></h2>
We use SQLAlchemy to establish a connection to the PostgreSQL database. Replace 'localhost' with your database server's address and 'superstore' with your database name.

```
import getpass
username = getpass.getpass('Username: ')
password = getpass.getpass('Password: ')
engine = create_engine(f'postgresql://{username}:{password}@localhost:5432/superstore')
```

<h2 id = "extraction" align="left"><font color="#9B60A1">Step 3: Data Extraction</font></h2>
Extract data from the 'superstore' table and load it into a pandas DataFrame:

```
query = "SELECT * FROM superstore;"
df_original = pd.read_sql_query(query, engine)
df = df_original.copy()
```

<h2 id = "analysispython" align="left"><font color="#9B60A1">Step 4: Preliminary Data Analysis</font></h2>
Perform initial analysis to understand the dataset structure and identify any data inconsistencies:

```
# Dataset Information
print("The Dataset has {} columns and {} rows.".format(df.shape[1], df.shape[0]))
print("The DataFrame has {} duplicated values and {} missing values.".format(df.duplicated().sum(), df.isnull().sum().sum()))
print(df.info())

# Checking the minimum value in 'shipping_business_days' column
print(df['shipping_business_days'].min())

# Null Distribution
null_distribution = df.isnull().sum().to_frame().rename(columns = {0: 'count'})
null_distribution['%'] = (null_distribution['count'] / len(df)) * 100
null_distribution = null_distribution.sort_values(by='%', ascending=False)
print(null_distribution)
```

<h2 id="cleaning" align="left"><font color="#9B60A1">Step 5: Data Cleaning</font></h2>
Handle missing and duplicate data:

```
df.dropna(inplace=True)
```

### <h2 id="setup" align="left"><font color="#9B60A1">Step 6: Setting Up the Test</font></h2>
Identify unique shipping modes and create an indicator for 'Same Day' shipping mode:
```
unique_ship_modes = df['ship_mode'].unique()
df['same_day_indicator'] = (df['ship_mode'] == 'Same Day').astype(int)
```

### <h2 id="test" align="left"><font color="#9B60A1">Step 7: Conducting the Test</font></h2>
Perform a T-test to compare sales between 'Same Day' (fast shipping) and other shipping modes (standard shipping):

```
fast_shipping = df[df['same_day_indicator'] == 1]['sales']
standard_shipping = df[df['same_day_indicator'] == 0]['sales']

t_stat, p_value = stats.ttest_ind(fast_shipping, standard_shipping, equal_var=False)

# Interpretation of results
alpha = 0.05
if p_value < alpha:
    print("Fast shipping mode leads to statistically significantly higher sales than standard shipping mode.")
else:
    print("There is no statistically significant difference between fast and standard shipping modes.")
```

<h2 id="insights" align="left"><font color="#9B60A1">Step 8: Insights Based on T-test</font></h2>
Based on the result "There is no statistically significant difference in sales between fast and standard shipping modes," we can draw the following insights:

1. __Shipping Mode Impact:__ The choice between fast and standard shipping modes does not have a significant effect on sales outcomes.
2. __Customer Preference:__ Customers may not perceive a substantial difference in value between fast and standard shipping options when making purchasing decisions.
3. __Cost Consideration:__ Businesses might consider optimizing shipping strategies based on cost-effectiveness rather than focusing solely on delivery speed, especially if there is no significant impact on sales.

However, it's worth noting that for certain customer segments, fast shipping may still be significantly important. Customer segmentation could help identify these groups. However, such detailed analysis is beyond the scope of this project.

<h2 id="sales-analysis" align="left"><font color="#9B60A1">PowerBI Report for Sales Analysis </font></h2>

[![PowerBI Report 1](/assets/BI_Report_1.png)]

<h2 "ship-day-analysis" align="left"><font color="#9B60A1">PowerBI Report for Shipping Day Analysis </font></h2>

[![PowerBI Report 2](/assets/BI_Report_2.png)]
