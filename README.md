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

<img width="672" alt="dashboard_mockup" src="https://github.com/Michaelaalo/end_to_end_uk_youtube_Project/assets/93910710/481b2282-6fd7-4aab-8fa4-22dc98c9f151">

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

![1_row_count_check](https://github.com/Michaelaalo/end_to_end_uk_youtube_Project/assets/93910710/a80a168a-4429-44b8-9ed7-1faf8245414c)


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

![2_column_count_check](https://github.com/Michaelaalo/end_to_end_uk_youtube_Project/assets/93910710/6cf7617f-757e-4634-8482-d2139c2dfcf5)

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

![3_data_type_check](https://github.com/Michaelaalo/end_to_end_uk_youtube_Project/assets/93910710/88895ad2-c521-4ab0-9ce5-1c1a5a991845)


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

![4_duplicate_records_check](https://github.com/Michaelaalo/end_to_end_uk_youtube_Project/assets/93910710/87d31b6a-919d-4a0b-ae9f-97940476fd4a)


# Visualization

## Results

- What does the dashboard look like?

![top_uk_youtubers_2024](https://github.com/Michaelaalo/end_to_end_uk_youtube_Project/assets/93910710/55ee8236-8f10-4699-807d-4f79403794f4)

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

## Findings

### Overview
In this analysis, we aimed to identify top-performing YouTube channels based on various metrics to provide insights for our marketing client. The key questions we addressed are as follows:

1. Who are the top 10 YouTubers with the most subscribers?
2. Which 3 channels have uploaded the most videos?
3. Which 3 channels have the most views?
4. Which 3 channels have the highest average views per video?
5. Which 3 channels have the highest views per subscriber ratio?
6. Which 3 channels have the highest subscriber engagement rate per video uploaded?

### Key Questions and Answers

#### 1. Top 10 YouTubers with the Most Subscribers
| Rank | Channel Name          | Subscribers (M) |
|------|-----------------------|-----------------|
| 1    | NoCopyrightSounds     | 33.60           |
| 2    | DanTDM                | 28.60           |
| 3    | Dan Rhodes            | 26.50           |
| 4    | Miss Katy             | 24.50           |
| 5    | Mister Max            | 24.40           |
| 6    | KSI                   | 24.10           |
| 7    | Jelly                 | 23.50           |
| 8    | Dua Lipa              | 23.30           |
| 9    | Sidemen               | 21.00           |
| 10   | Ali-A                 | 18.90           |

#### 2. Channels with the Most Videos Uploaded
| Rank | Channel Name  | Videos Uploaded |
|------|---------------|-----------------|
| 1    | GRM Daily     | 14,696          |
| 2    | Manchester City | 8,248          |
| 3    | Yogscast      | 6,435           |

#### 3. Channels with the Most Views
| Rank | Channel Name | Total Views (B) |
|------|--------------|------------------|
| 1    | DanTDM       | 19.78            |
| 2    | Dan Rhodes   | 18.56            |
| 3    | Mister Max   | 15.97            |

#### 4. Channels with the Highest Average Views per Video
| Channel Name | Average Views per Video (M) |
|--------------|-----------------------------|
| Mark Ronson  | 32.27                       |
| Jessie J     | 5.97                        |
| Dua Lipa     | 5.76                        |

#### 5. Channels with the Highest Views per Subscriber Ratio
| Rank | Channel Name       | Views per Subscriber |
|------|--------------------|----------------------|
| 1    | GRM Daily          | 1185.79              |
| 2    | Nickelodeon        | 1061.04              |
| 3    | Disney Junior UK   | 1031.97              |

#### 6. Channels with the Highest Subscriber Engagement Rate per Video Uploaded
| Rank | Channel Name  | Subscriber Engagement Rate |
|------|---------------|----------------------------|
| 1    | Mark Ronson   | 343,000                    |
| 2    | Jessie J      | 110,416.67                 |
| 3    | Dua Lipa      | 104,954.95                 |

### Discovery
Our analysis revealed that:

- **NoCopyrightSounds**, **Dan Rhodes**, and **DanTDM** have the most subscribers in the UK.
- **GRM Daily**, **Manchester City**, and **Yogscast** have the highest number of videos uploaded.
- **DanTDM**, **Dan Rhodes**, and **Mister Max** have the most views.
- Entertainment and music channels dominate in terms of subscriber engagement and average views per video.

### Recommendations
Based on the insights, we recommend:

1. **Dan Rhodes** as the best YouTube channel to collaborate with for maximizing visibility due to the highest subscriber count and significant average views per video.
2. While **GRM Daily**, **Manchester City**, and **Yogscast** upload the most videos, their potential ROI is lower. It may be better to allocate the budget to channels with higher engagement and view counts.
3. **Mister Max** is ideal for maximizing reach, but **DanTDM** and **Dan Rhodes** also present excellent long-term options given their large subscriber bases and high average views.

### Potential ROI
1. **Dan Rhodes**: 
   - Net profit per video: **$1,065,000**
   
2. **Mister Max**:
   - Net profit per video: **$1,276,000**

3. **DanTDM**:
   - Product placement net profit: **$484,000** per video
   - Influencer marketing net profit: **$404,000**

4. **NoCopyrightSounds**:
   - Net profit per video: **$642,000**

### Action Plan
1. **Initiate Contact**: Begin discussions with Dan Rhodes for a potential partnership.
2. **Negotiation**: Ensure contracts are within budget allocations.
3. **Campaign Kickoff**: Launch the campaigns and monitor their performance against KPIs.
4. **Review and Optimize**: Assess campaign results, gather insights, and optimize based on feedback.

By starting with Dan Rhodes, we can measure the success of the campaign and plan future collaborations with other high-performing channels like **DanTDM**, **Mister Max**, and **NoCopyrightSounds**. This approach will help us maximize ROI and achieve the marketing client's goals effectively.
