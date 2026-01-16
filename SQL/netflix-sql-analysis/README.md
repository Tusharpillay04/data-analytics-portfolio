# Netflix Movies and TV Shows Data Analysis using SQL
![Netflix logo](https://github.com/Tusharpillay04/Data-Analytics-Portfolio/blob/main/SQL/netflix-sql-analysis/logo.png)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
>Analyze the distribution of content types (movies vs TV shows).
>Identify the most common ratings for movies and TV shows.
>List and analyze content based on release years, countries, and durations.
>Explore and categorize content based on specific criteria and keywords.

## Dataset
The data for this project is sourced from the Kaggle dataset:
   #### Dataset Link:[Movie Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Database Schema

```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix (
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

## Business Problems and Solutions
### 1. Count the Number of Movies vs TV Shows
```sql
select type, count(*) as total_content from Netflix group by type;
```
#### Objective : 
Determine the distribution of content types on Netflix.

### 2. Find the Most Common Rating for Movies and TV Shows
```sql
select type, rating, count(*) as total, rank() over(partition by type order by count(*) desc) as ranking from Netflix group by type, rating order by total desc ;
```
#### Objective: 
Identify the most frequently occurring rating for each type of content. 

### 3. List All Movies Released in a Specific Year (e.g., 2020)
```sql
select * from Netflix where type = 'Movie' and release_year='2020';
```
#### Objective: 
Retrieve all movies released in a specific year.

### 4.Find the top 5 countries with the most content on Netflix
```sql
select unnest(string_to_array(country,','))as new_country, count(show_id)as total_content from Netflix group by 1 order by 2 desc limit 5;
```
#### Objective: 
Identify the top 5 countries with the highest number of content items.

### 5.Identify the Longest Movie
```sql
select * from netflix where type='Movie' and duration=(select max(duration) from netflix);
```
#### Objective: 
Find the movie with the longest duration.

### 6.Find content added in the last 5 years
```sql
select count(*) from netflix where release_year in (2021,2020,2019,2018,2017);
```
#### Objective:
Retrieve content added to Netflix in the last 5 years.

### 7.Find All Movies/TV Shows by Director 'Rajiv Chilaka'
```sql
select * from netflix where director Ilike '%Rajiv Chilaka%';
```
#### Objective: 
List all content directed by 'Rajiv Chilaka'.

### 8.List all TV shows with more than 5 seasons
```sql
SELECT * FROM netflix WHERE type = 'TV Show' AND split_part(duration, ' ', 1)::INT>5;
```
#### Objective:
Identify TV shows with more than 5 seasons.

### 9.Count the number of content items in each genre
```sql
select unnest(string_to_array(listed_in,','))as genre, count(show_id)from netflix group by 1;
```
#### Objective: Count the number of content items in each genre.

### 10.Find each year and the average number of content release in India on netflix, return top 5 year with highest avg content release!
```sql
SELECT
    EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS year,
    COUNT(*) AS total_content,
    ROUND(
        COUNT(*)::NUMERIC
        / (SELECT COUNT(*) FROM netflix WHERE country = 'India')::NUMERIC
        * 100,
        2
    ) AS avg_content_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1
ORDER BY 1;
```
#### Objective: Calculate and rank years by the average number of content releases by India.

### 11. List all movies that are documentaries
```sql
select * from netflix where listed_in ilike '%documentaries%';
```
#### Objective: Retrieve all movies classified as documentaries.

### 12. Find All Content Without a Director
```sql
select * from netflix where director is null;
```
#### Objective: 
List content that does not have a director.

### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
```sql
select * from netflix where casts ilike '%salman Khan%' and release_year > extract(year from current_date)-10;
```
#### Objective: 
Count the number of movies featuring 'Salman Khan' in the last 10 years.

### 14. Find the top 10 actors who have appeared in the highest number of movies produced
```sql
select unnest(string_to_array(casts,',')) as actrors, count(*) as total_content from netflix where country ilike '%india' group by 1 order by 2 desc limit 10;
```
#### Objective: 
Identify the top 10 actors with the most appearances in Indian-produced movies.

### 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.
```sql
with new_table 
as(
select *, 
       case 
	   when
	        description ilike '%killer%' or 
			description ilike '%violence%' then 'Bad_content'
			else 'Good_content'
		end category 
from netflix 
)
select category, 
       count(*)as total_content
from new_table 
group by 1;
```
#### Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.



