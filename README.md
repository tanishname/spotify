
# 🎧 Spotify SQL Project

This project explores advanced SQL queries on a Spotify dataset, covering everything from basic SELECTs to complex window functions and CTEs. It's structured into three levels: **Easy**, **Medium**, and **Hard**

---

## 📂 Dataset Structure

The project is based on a table called `spotify` with the following schema:

```sql
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

---

## ✅ Easy Level Queries

```sql
-- 1. Tracks with more than 1 billion streams
SELECT * FROM spotify WHERE stream > 1000000000;

-- 2. List all albums with their respective artists
SELECT DISTINCT album, artist FROM spotify ORDER BY 1;

-- 3. Total number of comments where licensed = TRUE
SELECT SUM(comments) AS Total_Comments FROM spotify WHERE licensed = TRUE;

-- 4. All tracks that belong to album type 'single'
SELECT track FROM spotify WHERE album_type ILIKE 'single';

-- 5. Total number of tracks by each artist
SELECT artist, COUNT(track) AS total_number_of_songs FROM spotify GROUP BY 1 ORDER BY 2 DESC;
```

---

## 🧠 Medium Level Queries

```sql
-- 6. Average danceability per album
SELECT album, AVG(danceability) AS avg_danceability FROM spotify GROUP BY 1 ORDER BY 2 DESC;

-- 7. Top 5 tracks with highest energy
SELECT track, MAX(energy) FROM spotify GROUP BY 1 ORDER BY 2 DESC LIMIT 5;

-- 8. Tracks with views and likes where official_video is TRUE
SELECT track, SUM(views) AS total_views, SUM(likes) AS total_likes FROM spotify WHERE official_video = TRUE GROUP BY 1 ORDER BY 2 DESC;

-- 9. Total views of all tracks per album
SELECT album, track, SUM(views) AS total_views FROM spotify GROUP BY 1, 2 ORDER BY 3 DESC;
```

---

## 🔥 Hard Level Queries

```sql
-- 10. Tracks streamed more on Spotify than YouTube
WITH table1 AS (
    SELECT 
        track, 
        COALESCE(SUM(CASE WHEN most_played_on ILIKE 'spotify' THEN stream END), 0) AS ms,
        COALESCE(SUM(CASE WHEN most_played_on ILIKE 'youtube' THEN stream END), 0) AS my
    FROM spotify
    GROUP BY 1
)
SELECT * FROM table1 WHERE ms > my AND my <> 0;

-- 11. Top 3 most viewed tracks for each artist
WITH ranking_artist AS (
    SELECT 
        artist, 
        track, 
        SUM(views),
        DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) AS rank
    FROM spotify
    GROUP BY 1, 2
)
SELECT * FROM ranking_artist WHERE rank <= 3;

-- 12. Tracks where liveness > average
SELECT track, artist, liveness FROM spotify WHERE liveness > (SELECT AVG(liveness) FROM spotify);

-- 13. Difference between highest and lowest energy values per album
WITH t1 AS (
    SELECT album, MAX(energy) AS highest_energy, MIN(energy) AS lowest_energy FROM spotify GROUP BY album
)
SELECT album, highest_energy - lowest_energy AS energy_diff FROM t1;

-- 14. Tracks where energy-to-liveness ratio > 1.2
SELECT track, artist, album, energy, liveness, (energy / liveness) AS energy_liveness_ratio 
FROM spotify 
WHERE liveness > 0 AND (energy / liveness) > 1.2;

-- 15. Cumulative sum of likes ordered by views
SELECT track, artist, album, views, likes,
    SUM(likes) OVER (ORDER BY views ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumulative_likes
FROM spotify;
```

---

## 🚀 How to Use

1. Load the schema into your SQL environment.
2. Paste the queries based on the level of difficulty you want to try.
3. Observe, tweak, and analyze the results.

---

## 👨‍💻 Author

Made with 💚 by a SQL enthusiast.

