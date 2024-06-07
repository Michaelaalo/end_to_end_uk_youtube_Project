### Explaining the SQL Code for Beginners

Here’s a step-by-step explanation of the SQL code:

```sql
/*
# 1. Row count check 

Count the total number of records (or rows) in the SQL view

*/
```

#### 1. Count Total Rows
This query counts the total number of rows in the view `view_uk_youtubers_2024`.

```sql
SELECT
    COUNT(*) AS no_of_rows
FROM
    view_uk_youtubers_2024;
```

### Explanation
- `COUNT(*)`: Counts all rows in the table/view.
- `AS no_of_rows`: Gives the count column a name `no_of_rows`.
- `FROM view_uk_youtubers_2024`: Specifies the view to count rows from.

```sql
/*
# 2. Column count check 

Count the total number of columns (or fields) in the SQL view

*/
```

#### 2. Count Total Columns
This query counts the total number of columns in the view `view_uk_youtubers_2024`.

```sql
SELECT
    COUNT(*) AS column_count
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_uk_youtubers_2024';
```

### Explanation
- `INFORMATION_SCHEMA.COLUMNS`: System table containing column details for all tables/views.
- `WHERE TABLE_NAME = 'view_uk_youtubers_2024'`: Filters the columns to only those in the specified view.
- `COUNT(*) AS column_count`: Counts the columns and names the result `column_count`.

```sql
/*
# 3. Data type check

Check the data types of each column from the view by checking the INFORMATION SCHEMA view

*/
```

#### 3. Check Data Types
This query retrieves the names and data types of all columns in the view `view_uk_youtubers_2024`.

```sql
SELECT
    COLUMN_NAME,
    DATA_TYPE
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_uk_youtubers_2024';
```

### Explanation
- `COLUMN_NAME`: Name of each column.
- `DATA_TYPE`: Data type of each column (e.g., VARCHAR, INTEGER).
- `WHERE TABLE_NAME = 'view_uk_youtubers_2024'`: Filters to only columns in the specified view.

```sql
/*
# 4. Duplicate records check

-- 1. Check for duplicate rows in the view
-- 2. Group by the channel name
-- 3. Filter for groups with more than one row

*/
```

#### 4. Check for Duplicate Records
This query checks for duplicate rows in the view `view_uk_youtubers_2024`.

```sql
-- 1.
SELECT
    channel_name,
    COUNT(*) AS duplicate_count
FROM
    view_uk_youtubers_2024

-- 2.
GROUP BY
    channel_name

-- 3.
HAVING
    COUNT(*) > 1;
```

### Explanation
- `GROUP BY channel_name`: Groups the data by the `channel_name` column.
- `COUNT(*) AS duplicate_count`: Counts the number of rows for each group.
- `HAVING COUNT(*) > 1`: Filters to only groups with more than one row, indicating duplicates.

```sql
/*

# Data quality tests 

1. The data needs to be 100 records of YouTube channels (row count test)	--- (passed!!!)
2. The data needs 4 fields (column count test)								--- (passed!!!)
3. The channel name column must be string format, and the other columns must be numerical data types (data type check)    --- (passed!!!)
4. Each record must be unique in the dataset (duplicate count check)  --- (passed!!!)


Row count - 100 
Column count - 4


Data types

channel_name = VARCHAR
total_subscribers = INTEGER
total_views = INTEGER
total_videos = INTEGER

Duplicate count = 0


*/

```

### Summary of Data Quality Tests
- **Row Count Test**: Checks that there are 100 rows. (Passed)
- **Column Count Test**: Checks that there are 4 columns. (Passed)
- **Data Type Check**: Ensures `channel_name` is a string (VARCHAR) and other columns are numerical (INTEGER). (Passed)
- **Duplicate Count Check**: Ensures no duplicate rows. (Passed)

```sql
-- 1. Row count check 

SELECT 
	COUNT(*) as no_of_rows
FROM 
	view_uk_youtubers_2024 
```

### Row Count Check
Counts the total number of rows in the view.

```sql
-- 2. Column count check 

SELECT 
	COUNT(*) as column_count 
FROM 
	INFORMATION_SCHEMA.COLUMNS
WHERE 
	TABLE_NAME = 'view_uk_youtubers_2024'
```

### Column Count Check
Counts the total number of columns in the view.

```sql
-- 3. Data type check 

SELECT 
	COLUMN_NAME,
	DATA_TYPE
FROM 
	INFORMATION_SCHEMA.COLUMNS
WHERE 
	TABLE_NAME = 'view_uk_youtubers_2024'
```

### Data Type Check
Retrieves the names and data types of columns in the view.

```sql
-- 4. Duplicate records check

SELECT 
	channel_name,
	COUNT(*) as duplicate_count
FROM 
	view_uk_youtubers_2024
GROUP BY 
	channel_name 
HAVING 
	COUNT(*) > 1 
```

### Duplicate Records Check
Checks for duplicate rows in the view by grouping by `channel_name` and filtering for groups with more than one row.

```sql
/* 

# 1. Define variables 
# 2. Create a CTE that rounds the average views per video 
# 3. Select the column you need and create calculated columns from existing ones 
# 4. Filter results by Youtube channels
# 5. Sort results by net profits (from highest to lowest)

*/

-- 1. 
DECLARE @conversionRate FLOAT = 0.02;		-- The conversion rate @ 2%
DECLARE @productCost FLOAT = 5.0;			-- The product cost @ $5
DECLARE @campaignCost FLOAT = 50000.0;		-- The campaign cost @ $50,000	
```

### Define Variables
Defines variables for conversion rate, product cost, and campaign cost.

```sql
-- 2.  
WITH ChannelData AS (
    SELECT 
        channel_name,
        total_views,
        total_videos,
        ROUND((CAST(total_views AS FLOAT) / total_videos), -4) AS rounded_avg_views_per_video
    FROM 
        youtube_db.dbo.view_uk_youtubers_2024
)
```

### Create a Common Table Expression (CTE)
Calculates and rounds the average views per video.

```sql
-- 3. 
SELECT 
    channel_name,
    rounded_avg_views_per_video,
    (rounded_avg_views_per_video * @conversionRate) AS potential_units_sold_per_video,
    (rounded_avg_views_per_video * @conversionRate * @productCost) AS potential_revenue_per_video,
    ((rounded_avg_views_per_video * @conversionRate * @productCost) - @campaignCost) AS net_profit
FROM 
    ChannelData
```

### Select and Calculate Columns
Selects columns and creates calculated columns for potential units sold, potential revenue, and net profit.

```sql
-- 4. 
WHERE 
    channel_name in ('NoCopyrightSounds', 'DanTDM', 'Dan Rhodes')    
```

### Filter Results
Filters results by specific YouTube channels.

```sql
-- 5.  
ORDER BY
	net_profit DESC
```

### Sort Results
Sorts the results by net profit in descending order.

This set of SQL queries checks data quality by verifying row and column counts, ensuring correct data types, checking for duplicates, and calculating potential revenue and profit for specific YouTube channels.
