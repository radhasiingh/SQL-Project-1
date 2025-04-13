# Netflix Movies & TV Shows Data Analysis using SQL

![Netflix logo](BrandAssets_Logos_01-Wordmark.jpg)

## Overview
This project involves a comprehensive analysis of Netflix’s movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project’s objectives, business problems, solutions, findings, and conclusions.

## Objectives
1. Analyze the distribution of content types (movies vs TV shows).
2. Identify the most common ratings for movies and TV shows.
3. List and analyze content based on release years, countries, and durations.
4. Explore and categorize content based on specific criteria and keywords.

## Dataset
The data for this project is sourced from the Kaggle dataset
Link: [Netflix Shows Dataset on Kaggle](https://www.kaggle.com/datasets/shivamb/netflix-shows)

## Schema
## Schema

```sql
CREATE TABLE netflix (
  show_id VARCHAR(10),
  type VARCHAR(10),
  title VARCHAR(150),
  director VARCHAR(208),
  casts VARCHAR(1000),
  country VARCHAR(150),
  date_added VARCHAR(50),
  release_year INT,
  rating VARCHAR(50),
  duration VARCHAR(50),
  listed_in VARCHAR(100),
  description VARCHAR(300)
);
```

## Business Problems & Solutions

1. Count no. of Movies vs TV Shows.
```sql   
SELECT type, COUNT(*) AS type_of_content
FROM netflix
GROUP BY type;
```

2. Show how often each rating appears by type.
```sql 
SELECT type, rating, COUNT(*) as total_rating
FROM netflix
GROUP BY type, rating
ORDER BY 
	CASE
		WHEN type = 'Movie' THEN 1
		WHEN type = 'TV Show' THEN 2
	END,
	total_rating DESC;
```
3. List all the movies released in a specific year (e.g. 2020)	
```sql 
SELECT * 
FROM netflix
WHERE release_year = 2020 and type = 'Movie';
```

4. Find the top 5 countries with the most content on Netflix
```sql 
SELECT
	UNNEST(STRING_TO_ARRAY(country, ',')) as new_country,
	COUNT(show_id) as total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

5. Identify the longest movie.
```sql 
SELECT * FROM netflix
WHERE
	type = 'Movie'
	AND
	duration = (SELECT MAX(duration) FROM netflix);
```
6. Find all the movies/TV shows by director 'Rajiv Chilaka'
```sql 
SELECT*
FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
```
7. List all TV shows with more than 5 seasons
```sql 
SELECT
	*
FROM netflix
WHERE 
	type = 'TV Show'
	AND
	SPLIT_PART(duration, ' ',1):: numeric > 5;
```
8. Count the number of content items in each genre
```sql 
SELECT
	UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre,
	COUNT(show_id)
FROM netflix
GROUP BY 1
```
9.List all movies that are documentaries
```sql 
SELECT * FROM netflix
WHERE
	listed_in ILIKE '%documentaries%'
```
10. Find all content without a director
```sql 
SELECT * FROM netflix
WHERE
	director IS NULL
```

11. Find how many movies actor  'Salman Khan' has appeared in.
```sql 
SELECT * FROM netflix
WHERE
	casts ILIKE '%Salman Khan%'
```
12. Find the top 10 actors who have appeared in the highest number of movies  producing in Inida. 	
```sql 
SELECT
UNNEST(STRING_TO_ARRAY(casts, ',')) as actors,
COUNT(*) as total_content
FROM netflix
WHERE country ILIKE '%india%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```
13. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field. 
    Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.
```sql 
WITH new_table
AS(
SELECT *,
	CASE
		WHEN description ILIKE '%kill%' OR
			description ILIKE '%Violence%' THEN 'Bad Content'
			ELSE 'Good Content'
	END category	
FROM netflix)
SELECT
	category,
	COUNT(*) as total_content
FROM new_table
GROUP BY 1
```
