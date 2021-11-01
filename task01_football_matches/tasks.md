# Football Matches - Tasks

Each of the questions/tasks below can be answered using a `SELECT` query. When you find a solution copy it into the code block under the question before pushing your solution to GitHub.

1) Find all the matches from 2017.

```sql
SELECT * FROM matches WHERE season = 2017;

```

2) Find all the matches featuring Barcelona.

```sql
SELECT * FROM matches WHERE hometeam = 'Barcelona' or awayteam = 'Barcelona';

```

3) What are the names of the Scottish divisions included?

```sql
SELECT name FROM divisions WHERE country = 'Scotland';

```

4) Find the division code for the Bundesliga. Use that code to find out how many matches Freiburg have played in the Bundesliga since the data started being collected.

```sql
-- Find code --
SELECT code FROM divisions WHERE name = 'Bundesliga';

-- Find matches --
SELECT * FROM matches WHERE (hometeam = 'Freiburg' or awayteam = 'Freiburg') and division_code = 'D1';

```

5) Find the unique names of the teams which include the word "City" in their name (as entered in the database)

```sql
SELECT DISTINCT(hometeam) FROM matches WHERE hometeam LIKE '%City%';
-- Or --
SELECT hometeam FROM matches WHERE hometeam LIKE '%City%' GROUP BY hometeam;
-- Can use awayteam instead of hometeam as well -- 

```

6) How many different teams have played in matches recorded in a French division?

```sql
-- Find French divsions --
 SELECT * FROM divisions WHERE country = 'France';

-- Find number of different teams -- 
 SELECT COUNT(DISTINCT hometeam) FROM matches WHERE division_code IN ('F1', 'F2');


-- SELECT hometeam FROM matches WHERE division_code = 'F1' or division_code = 'F2' GROUP BY hometeam;


```

7) Have Huddersfield played Swansea in the period covered?

```sql
-- Returns all matches --
SELECT * FROM matches WHERE (hometeam = 'Huddersfield' and awayteam = 'Swansea') or (hometeam = 'Swansea' and awayteam = 'Huddersfield');
-- If anything returns, Huddersfield have played Swansea --

-- Returns number of matches--
SELECT * FROM matches WHERE (hometeam = 'Huddersfield' and awayteam = 'Swansea') or (hometeam = 'Swansea' and awayteam = 'Huddersfield');
-- If count > 0, Huddersfield have played Swansea --

```

8) How many draws were there in the Eredivisie between 2010 and 2015?

```sql
-- Find code --
SELECT code FROM divisions WHERE name = 'Eredivisie';

-- Find number of matches --
SELECT COUNT(*) FROM matches WHERE ftr = 'D' AND season BETWEEN 2010 AND 2013;

```

9) Select the matches played in the Premier League in order of total goals scored from highest to lowest. Where there is a tie the match with more home goals should come first.

```sql
-- Find code --
SELECT code FROM divisions WHERE name = 'Premier League';

-- Find matches --
SELECT * FROM matches WHERE division_code = 'E0' ORDER BY fthg + ftag DESC, fthg DESC;

```

10) In which division and which season were the most goals scored?

```sql
-- Find match --
SELECT * FROM matches ORDER BY fthg + ftag DESC LIMIT 1;

-- Find division code and season --
SELECT division_code, season FROM matches ORDER BY fthg + ftag DESC LIMIT 1;

-- Find division name --
football=# SELECT name FROM divisions WHERE code = 'N1';

```

### Useful Resources

- [Filtering results](https://www.w3schools.com/sql/sql_where.asp)
- [Ordering results](https://www.w3schools.com/sql/sql_orderby.asp)
- [Grouping results](https://www.w3schools.com/sql/sql_groupby.asp)