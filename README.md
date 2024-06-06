# Excel to Power BI end to end Project 

![excel-to-powerbi-animated-diagram](assets/images/kaggle_to_powerbi.gif)

# Table of Contents

- [Objective](#objective)
- [Data Source](#data-source)
- [Stages](#stages)
- [Design](#design)
  - [Mockup](#mockup)
  - [Tools](#tools)
- [Development](#development)
  - [Pseudocode](#pseudocode)
  - [Data Exploration](#data-exploration)
  - [Data Cleaning](#data-cleaning)
  - [Transform the Data](#transform-the-data)
  - [Create the SQL View](#create-the-sql-view)
- [Testing](#testing)
  - [Data Quality Tests](#data-quality-tests)
- [Visualization](#visualization)
  - [Results](#results)
  - [DAX Measures](#dax-measures)
- [Analysis](#analysis)
  - [Findings](#findings)
  - [Validation](#validation)
  - [Discovery](#discovery)
- [Recommendations](#recommendations)
  - [Potential ROI](#potential-roi)
  - [Potential Courses of Action](#potential-courses-of-action)
- [Conclusion](#conclusion)

# Objective

- What is the primary goal?

The Head of Marketing wants to identify the top UK YouTubers in 2024 to decide which influencers to partner with for marketing campaigns throughout the year.

- What is the desired solution?

Create a dashboard that provides insights into the top UK YouTubers in 2024, including their:
- Subscriber count
- Total views
- Total videos
- Engagement metrics

This will enable the marketing team to make informed decisions about which YouTubers to collaborate with for their campaigns.

## User Story

As the Head of Marketing, I want to use a dashboard that analyzes YouTube channel data in the UK.

This dashboard should allow me to identify the top-performing channels based on metrics such as subscriber base and average views. With this information, I can make better decisions about which YouTubers to collaborate with, thus maximizing the effectiveness of each marketing campaign.

# Data Source

- What data is required to achieve the objective?

We need data on the top UK YouTubers in 2024, including:
- Channel names
- Total subscribers
- Total views
- Total videos uploaded

- Where is the data sourced from?
The data is sourced from Kaggle, available as an Excel extract. You can find it [here](https://www.kaggle.com/datasets/bhavyadhingra00020/top-100-social-media-influencers-2024-countrywise?resource=download).

# Stages

- Design
- Development
- Testing
- Analysis

# Design

## Dashboard Components Required

- What should the dashboard contain based on the requirements?

To determine the necessary components, we need to identify the key questions the dashboard should answer:

1. Who are the top 10 YouTubers with the most subscribers?
2. Which 3 channels have uploaded the most videos?
3. Which 3 channels have the most views?
4. Which 3 channels have the highest average views per video?
5. Which 3 channels have the highest views per subscriber ratio?
6. Which 3 channels have the highest subscriber engagement rate per video uploaded?

These questions may evolve as we progress with the analysis.

## Dashboard Mockup

- What should it look like?

Potential data visuals include:
1. Table
2. Treemap
3. Scorecards
4. Horizontal bar chart

![Dashboard-Mockup](assets/images/dashboard_mockup.png)

## Tools

| Tool | Purpose |
| --- | --- |
| Excel | Exploring the data |
| SQL Server | Cleaning, testing, and analyzing the data |
| Power BI | Visualizing the data via interactive dashboards |
| GitHub | Hosting project documentation and version control |
| Mokkup AI | Designing the wireframe/mockup of the dashboard |

# Development

## Pseudocode

- What is the general approach for creating this solution?

1. Get the data
2. Explore the data in Excel
3. Load the data into SQL Server
4. Clean the data with SQL
5. Test the data with SQL
6. Visualize the data in Power BI
7. Generate findings based on insights
8. Write documentation and commentary
9. Publish the data to GitHub Pages

## Data Exploration Notes

This stage involves examining the data for errors, inconsistencies, and any unusual or corrupted characters.

- Initial observations:

1. The dataset contains at least 4 relevant columns, indicating we have all necessary data.
2. The first column contains channel IDs with an @ symbol; we need to extract channel names from this.
3. Some cells and headers are in a different language; we need to verify if these columns are necessary.
4. We have more data than needed; some columns will need to be removed.

## Data Cleaning

- What should the cleaned data look like?

The cleaned data should be structured and ready for analysis. It should meet the following criteria:

- Retain only relevant columns.
- Ensure appropriate data types for each column.
- Ensure no column contains null values.

Expected schema for the cleaned data:

| Column Name | Data Type | Nullable |
| --- | --- | --- |
| channel_name | VARCHAR | NO |
| total_subscribers | INTEGER | NO |
| total_views | INTEGER | NO |
| total_videos | INTEGER | NO |

- Steps to clean and shape the data:

1. Remove unnecessary columns.
2. Extract YouTube channel names from the first column.
3. Rename columns using aliases.

### Transform the Data

```sql
/*
# 1. Select the required columns
# 2. Extract the channel name from the 'NOMBRE' column
*/

-- 1.
SELECT
    SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS channel_name,  -- 2.
    total_subscribers,
    total_views,
    total_videos
FROM
    top_uk_youtubers_2024
```

### Create the SQL View

```sql
/*
# 1. Create a view to store the transformed data
# 2. Cast the extracted channel name as VARCHAR(100)
# 3. Select the required columns from the top_uk_youtubers_2024 SQL table
*/

-- 1.
CREATE VIEW view_uk_youtubers_2024 AS

-- 2.
SELECT
    CAST(SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS VARCHAR(100)) AS channel_name, -- 2.
    total_subscribers,
    total_views,
    total_videos
-- 3.
FROM
    top_uk_youtubers_2024
```

# Testing

- What data quality and validation checks will be performed?

Data quality tests conducted:

## Row Count Check

```sql
/*
# Count the total number of records (or rows) in the SQL view
*/

SELECT
    COUNT(*) AS no_of_rows
FROM
    view_uk_youtubers_2024;
```

![Row count check](assets/images/1_row_count_check.png)

## Column Count Check

### SQL Query

```sql
/*
# Count the total number of columns (or fields) in the SQL view
*/

SELECT
    COUNT(*) AS column_count
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_uk_youtubers_2024';
```

### Output

![Column count check](assets/images/2_column_count_check.png)

## Data Type Check

### SQL Query

```sql
/*
# Check the data types of each column in the view by checking the INFORMATION_SCHEMA view
*/

SELECT
    COLUMN_NAME,
    DATA_TYPE
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_uk_youtubers_2024';
```

### Output

![Data type check](assets/images/3_data_type_check.png)

## Duplicate Count Check

### SQL Query

```sql
/*
# 1. Check for duplicate rows in the view
# 2. Group by the channel name
# 3. Filter for groups with more than one row
*/

SELECT
    channel_name,
    COUNT(*) AS duplicate_count
FROM
    view_uk_youtubers_2024
GROUP BY
    channel_name
HAVING
    COUNT(*) > 1;
```

### Output

![Duplicate count check](assets/images/4_duplicate_records_check.png)

# Visualization

## Results

- What does the dashboard look like?

![GIF of Power BI Dashboard](assets/images/top_uk_youtubers_2024.gif)

This shows the top UK YouTubers in 2024 so far.

## DAX Measures

### 1. Total Subscribers (M)

```sql
Total Subscribers (M) = 
VAR million = 1000000
VAR sumOfSubscribers = SUM(view_uk_youtubers_2024[total_subscribers])
VAR totalSubscribers = DIVIDE(sumOfSubscribers, million)

RETURN totalSubscribers
```

### 2. Total Views (B)

```sql
Total Views (B) = 
VAR billion = 1000000000
VAR sumOfTotalViews = SUM(view_uk_youtubers_2024[total_views])
VAR totalViews = ROUND(sumOfTotalViews / billion, 2)

RETURN totalViews
```

### 3. Total Videos

```sql
Total Videos = 
VAR totalVideos = SUM(view_uk_youtubers_2024[total_videos])

RETURN totalVideos
```

### 4. Average Views Per Video (M

)

```sql
Average Views Per Video (M) = 
VAR million = 1000000
VAR totalViews = SUM(view_uk_youtubers_2024[total_views])
VAR totalVideos = SUM(view_uk_youtubers_2024[total_videos])
VAR averageViewsPerVideo = DIVIDE(totalViews, totalVideos)
VAR avgViewsPerVid = ROUND(averageViewsPerVideo / million, 2)

RETURN avgViewsPerVid
```

### 5. Average Views Per Subscriber (M)

```sql
Average Views Per Subscriber (M) = 
VAR million = 1000000
VAR totalViews = SUM(view_uk_youtubers_2024[total_views])
VAR totalSubscribers = SUM(view_uk_youtubers_2024[total_subscribers])
VAR avgViewsPerSubscriber = DIVIDE(totalViews, totalSubscribers)
VAR avgViewsPerSub = ROUND(avgViewsPerSubscriber / million, 2)

RETURN avgViewsPerSub
```

### 6. Subscriber Engagement Rate (%)

```sql
Subscriber Engagement Rate (%) = 
VAR totalVideos = SUM(view_uk_youtubers_2024[total_videos])
VAR totalSubscribers = SUM(view_uk_youtubers_2024[total_subscribers])
VAR subscriberEngagementRate = DIVIDE(totalSubscribers, totalVideos)
VAR subscriberEngRate = ROUND(subscriberEngagementRate, 2)

RETURN subscriberEngRate
```

# Analysis

## Findings

- What insights have we discovered?

1. The top 10 YouTubers have millions of subscribers, indicating significant influence and reach.
2. Some channels have a high number of videos uploaded, suggesting consistent content creation.
3. Certain channels boast the highest view counts, highlighting their popularity.
4. A few channels have the highest average views per video, indicating highly engaging content.
5. Some channels have a high views-per-subscriber ratio, showcasing strong audience engagement.
6. Certain channels exhibit high subscriber engagement rates per video, indicating dedicated followers.

## Validation

- How do we validate the findings?

1. Cross-reference data with external sources or YouTube analytics for accuracy.
2. Confirm the consistency of engagement metrics across various platforms.
3. Ensure that the calculated measures align with industry benchmarks.

## Discovery

- What additional insights or unexpected patterns have we uncovered?

1. A few channels with lower subscriber counts but high engagement rates suggest a niche but loyal audience.
2. The correlation between total views and total subscribers varies significantly across channels, indicating differing content strategies.

# Recommendations

## Potential ROI

- How can these insights be monetized?

1. Partnering with top YouTubers can drive significant brand exposure and engagement.
2. Focusing on channels with high engagement rates can yield better ROI for marketing campaigns.

## Potential Courses of Action

- What actions should be taken based on the insights?

1. Prioritize collaborations with YouTubers who have high engagement rates and average views per video.
2. Allocate more resources to channels that consistently produce engaging content.
3. Consider niche channels with loyal audiences for targeted marketing campaigns.

# Conclusion

The dashboard provides valuable insights into the top UK YouTubers in 2024. By leveraging these insights, the marketing team can make data-driven decisions about which YouTubers to partner with, maximizing the effectiveness of their campaigns and ultimately driving better ROI.
