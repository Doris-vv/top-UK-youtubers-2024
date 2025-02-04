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

![Image](https://github.com/user-attachments/assets/efa0ae0f-f09d-4eed-bf90-3901d8a8dc30)
