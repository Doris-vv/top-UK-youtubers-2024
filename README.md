# Top UK YouTubers Project - Excel & SQL & Power BI 
The raw data for this project is only one file. This data represents the information of UK Youtubers. The Head of Marketing wants to find out who the top YouTubers are in 2024 to decide on which YouTubers would be best to run marketing campaigns throughout the rest of the year.

The project is done in Microsoft SQL server, presented in Power BI and analysed in Excel.

## Data source
You can download the data from https://www.kaggle.com/datasets/bhavyadhingra00020/top-100-social-media-influencers-2024-countrywise?resource=download.

The file is also included in this repository.

## Objective
The Head of Marketing wants to discover the top performing UK Youtubers to form marketing collaborations with throughout the year 2024. 

## Tools
|Tool        |Purpose                                                                        |
|------------|-------------------------------------------------------------------------------|
|SQL Server  |Cleaning, testing and analyzing the data                                       |
|Mokkup AI   |Designing the wireframe/mockup of the dashboard                                |
|Power BI    |Visualizing the data via interactive dashboards                                |
|Excel       |Calculate, explore the data, generate the findings and write the recommendation|
|GitHub      |Hosting the project documentation and version control                          |

## Data cleaning
The dataset to produce the information we need should include the following fields
- Channel name (string)
- Total subscribers (integer)
- Total videos (integer)
- Total views (integer)

### Transform the data
```
/*
# 1. Select the required columns
# 2. Extract the channel name from the 'NOMBRE' column
*/

SELECT
    SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS channel_name,  
    total_subscribers,
    total_views,
    total_videos
FROM
    top_uk_youtubers_2024
```

### Create the SQL view
```
/*
# 1. Create a view to store the transformed data
# 2. Cast the extracted channel name as VARCHAR(100)
# 3. Select the required columns from the top_uk_youtubers_2024 SQL table 
*/

CREATE VIEW view_uk_youtubers_2024 AS
SELECT
    CAST(SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS VARCHAR(100)) AS channel_name, -- 2. 
    total_subscribers,
    total_views,
    total_videos
FROM
    top_uk_youtubers_2024
```
## Data Testing
### Row Count Check
```
/*
# Count the total number of records (or rows) are in the SQL view
*/

SELECT
    COUNT(*) AS no_of_rows
FROM
    view_uk_youtubers_2024;
```
### Column and Data Type Check
```
/*
# Check the column and data types of each column from the view by checking the INFORMATION SCHEMA view
*/

SELECT
    COLUMN_NAME,
    DATA_TYPE
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_NAME = 'view_uk_youtubers_2024';
```

### Duplicate Count Check
```
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

## Visualization
Data visualization was done in Microsoft Power BI.
![Image](https://github.com/user-attachments/assets/efa0ae0f-f09d-4eed-bf90-3901d8a8dc30)

## Analysis
For this analysis, we'll prioritize analysing the metrics that are important in generating the expected ROI for our marketing client, which are the YouTube channels wuth the most subscribers, total views and total videos.
1. Youtubers with the most subscribers
```
/* 
# 1. Define variables 
# 2. Create a CTE that rounds the average views per video 
# 3. Select the column you need and create calculated columns from existing ones 
# 4. Filter results by Youtube channels
# 5. Sort results by net profits (from highest to lowest)
*/

DECLARE @conversionRate FLOAT = 0.02;		-- The conversion rate @ 2%
DECLARE @productCost FLOAT = 5.0;			-- The product cost @ $5
DECLARE @campaignCost FLOAT = 50000.0;		-- The campaign cost @ $50,000	

WITH ChannelData AS (
    SELECT 
        channel_name,
        total_views,
        total_videos,
        ROUND((CAST(total_views AS FLOAT) / total_videos), -4) AS rounded_avg_views_per_video
    FROM 
        youtube_db.dbo.view_uk_youtubers_2024
)

SELECT 
    channel_name,
    rounded_avg_views_per_video,
    (rounded_avg_views_per_video * @conversionRate) AS potential_units_sold_per_video,
    (rounded_avg_views_per_video * @conversionRate * @productCost) AS potential_revenue_per_video,
    ((rounded_avg_views_per_video * @conversionRate * @productCost) - @campaignCost) AS net_profit
FROM 
    ChannelData
WHERE 
    channel_name in ('NoCopyrightSounds', 'DanTDM', 'Dan Rhodes')     
ORDER BY
	net_profit DESC
```
result by excel
|Channel Name	   |Avg Views per Vid 	|Potential Product Sales per Video 	|Potential Revenue per Video |Net Profit|
|------------------|--------------------|-----------------------------------|----------------------------|----------|
|NoCopyrightSounds |6,920,000 	 	    |138,400 		                    |692,000 		             |642,000   |
|DanTDM	           |5,340,000 	 	    |106,800 	 	                    |534,000 	 	             |484,000   |
|Dan Rhodes	       |11,150,000 	 	    |223,000 		                    |1,115,000 	 	             |1,065,000 |



2. Youtubers with the most videos
```
DECLARE @conversionRate FLOAT = 0.02;		-- The conversion rate @ 2%
DECLARE @productCost FLOAT = 5.0;			-- The product cost @ $5
DECLARE @campaignCost FLOAT = 55000.0;		-- The campaign cost @ $55,000	

WITH ChannelData AS (
    SELECT
        channel_name,
        total_views,
        total_videos,
        ROUND((CAST(total_views AS FLOAT) / total_videos), -4) AS rounded_avg_views_per_video
    FROM
        youtube_db.dbo.view_uk_youtubers_2024
)

SELECT
    channel_name,
    rounded_avg_views_per_video,
    (rounded_avg_views_per_video * @conversionRate) AS potential_units_sold_per_video,
    (rounded_avg_views_per_video * @conversionRate * @productCost) AS potential_revenue_per_video,
    ((rounded_avg_views_per_video * @conversionRate * @productCost) - @campaignCost) AS net_profit
FROM
    ChannelData
WHERE
    channel_name IN ('24 News HD', 'Sky News', 'BBC News ???? ')
ORDER BY
    net_profit DESC;
```
result by excel
|Channel Name	   |Avg Views per Vid 	|Potential Product Sales per Video 	|Potential Revenue per Video |Net Profit|
|------------------|--------------------|-----------------------------------|----------------------------|----------|
|24 News HD        |20,000 	 	        |400 		                        |2,000 		                 |-53,000   |
|Sky News	       |90,000 	 	        |1800 	 	                        |9,000 	 	                 |-46,000   |
|BBC News ???? 	   |120,000 	 	    |2400 		                        |12,000 	 	             |-43,000   |

3. Youtubers with the most views
```
DECLARE @conversionRate FLOAT = 0.02;        -- The conversion rate @ 2%
DECLARE @productCost MONEY = 5.0;            -- The product cost @ $5
DECLARE @campaignCost MONEY = 130000.0;      -- The campaign cost @ $130,000

WITH ChannelData AS (
    SELECT
        channel_name,
        total_views,
        total_videos,
        ROUND(CAST(total_views AS FLOAT) / total_videos, -4) AS avg_views_per_video
    FROM
        youtube_db.dbo.view_uk_youtubers_2024
)

SELECT
    channel_name,
    avg_views_per_video,
    (avg_views_per_video * @conversionRate) AS potential_units_sold_per_video,
    (avg_views_per_video * @conversionRate * @productCost) AS potential_revenue_per_video,
    (avg_views_per_video * @conversionRate * @productCost) - @campaignCost AS net_profit
FROM
    ChannelData
WHERE
    channel_name IN ('Mister Max', 'DanTDM', 'Dan Rhodes')
ORDER BY
    net_profit DESC;
```
result by excel
|Channel Name	   |Avg Views per Vid 	|Potential Product Sales per Video 	|Potential Revenue per Video |Net Profit|
|------------------|--------------------|-----------------------------------|----------------------------|----------|
|DanTDM            |5,340,000           |106,800                            |534,0000                    |404,000   |
|Dan Rhodes        |11,150,000          |223,000                            |1,115,000                   |985,000   |
|Mixter Max        |14,060,000          |281,200                            |1,406,000                   |1,276,000 |

## Recommendations
1.Dan Rhodes is the best YouTube channel to collaborate with if we want to maximize visbility because this channel has the most YouTube subscribers in the UK.

2.Although 24 News HD, Sky News and BBC News ???? are regular publishers on YouTube, it may be worth considering whether collaborating with them with the current budget caps are worth the effort, as the potential return on investments is negative.

3.Mister Max is the best YouTuber to collaborate with if we're interested in maximizing reach, but collaborating with DanTDM and Dan Rhodes is also good long-term options considering the fact that they both have large subscriber bases and are averaging significantly high number of views.

4.The top 3 channels to form collaborations with are NoCopyrightSounds, DanTDM and Dan Rhodes based on this analysis, because they attract the most engagement on their channels consistently.

## Action plan
Based on our analysis, we believe the best channel to advance a long-term partnership deal with to promote the client's products is the Dan Rhodes channel.

We'll have conversations with the marketing client to forecast what they also expect from this collaboration. Once we observe we're hitting the expected milestones, we'll advance with potential partnerships with DanTDM, Mister Max and NoCopyrightSounds channels in the future.
