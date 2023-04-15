---
title: "The Statistical Significance of the NFL Combine - Part 2"
date: "2023-04-14"
tags: ["nfl", "combine", "statistics"]
---

### Preamble
After preparing the data in the first part, we can now start exploring the data we've created.
To make it a little bit easier to run the code, I created a GitHub repository that includes a docker-compose that sets up everything you need to have a database and a Grafana dashboard.

The [code](https://github.com/MitchellWeg/draft-combine-blogpost) can be found here. The README has extensive instructions on how to run everything.

### Data exploration

#### College Stats

Lets first create a graph to see which college created the most NFL players:
`
SELECT COUNT(college) as player_count, college FROM draft GROUP BY college ORDER BY player_count DESC;
`

This yields the following graph in Grafana:

![](/images/colleges-screenshot.png)

This graph is not the prettiest, so let's condense it to make it a little bit more readable by altering the query:
```sql
SELECT COUNT(college) as player_count, college FROM draft GROUP BY college ORDER BY player_count DESC LIMIT 50;
```

This yields a way better, more readable graph:

![](/images/colleges-better-screenshot.png)

 
As you can see from the output of the query, LSU produced the most drafted players between 2000 and 2022.

Lets now check which schools produced the most first picks:

#### Player Stats

Lets see the percentage of the positions of the drafted players. If you would think that yearly more QB's are drafted, think again:

`SELECT position, COUNT(position) from draft GROUP BY draft.position;`

And if we turn this query into a pie-chart:

![](/images/pie-chart-positions.png)

Lets create a leaderboard for each individual stat:

* Fourty Yard Dash

`SELECT id, name, fourty_yard_dash, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE fourty_yard_dash != 0 ORDER BY fourty_yard_dash ASC LIMIT 5;`

```
+-------------------+------------------+------+
| name              | fourty_yard_dash | year |
+-------------------+------------------+------+
| John Ross         |             4.22 | 2017 |
| Donte' Stallworth |             4.22 | 2002 |
| Kalon Barnes      |             4.23 | 2022 |
| Chris Johnson     |             4.24 | 2008 |
| Dri Archer        |             4.26 | 2014 |
+-------------------+------------------+------+
```

* Three Cone Drill

`SELECT name, cone_drill, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE cone_drill != 0 ORDER BY cone_drill ASC LIMIT 5;`

```
+------------------+------------+------+
| name             | cone_drill | year |
+------------------+------------+------+
| David Long       |       6.45 | 2019 |
| Chris Rainey     |        6.5 | 2012 |
| Cecil Shorts     |        6.5 | 2011 |
| Avonte Maddox    |       6.51 | 2018 |
| Trindon Holliday |       6.54 | 2010 |
+------------------+------------+------+
```


* Bench
The number indicates the amount of reps performed, with a weight of 225 (102kg).

`SELECT name, bench, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE bench != 0 ORDER BY bench DESC LIMIT 5;`

```
+------------------+-------+------+
| name             | bench | year |
+------------------+-------+------+
| Stephen Paea     |    49 | 2011 |
| Leif Larsen      |    45 | 2000 |
| Dontari Poe      |    44 | 2012 |
| Jeff Owens       |    44 | 2010 |
| Brodrick Bunkley |    44 | 2006 |
+------------------+-------+------+
```

* Vertical

`SELECT name, vertical, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE vertical != 0 ORDER BY vertical DESC LIMIT 5;`

```
+-----------------------+----------+------+
| name                  | vertical | year |
+-----------------------+----------+------+
| Chris Conley          |       45 | 2015 |
| Chris Chambers        |       45 | 2001 |
| Byron Jones           |     44.5 | 2015 |
| Donovan Peoples-Jones |     44.5 | 2020 |
| Juan Thornhill        |       44 | 2019 |
+-----------------------+----------+------+
```

* Shuttle

`SELECT name, shuttle, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE shuttle != 0 ORDER BY shuttle ASC LIMIT 5`

```
+---------------+---------+------+
| name          | shuttle | year |
+---------------+---------+------+
| Kevin Kasper  |    3.73 | 2001 |
| Deion Branch  |    3.78 | 2002 |
| Brandin Cooks |    3.81 | 2014 |
| Bobby McCain  |    3.82 | 2015 |
| Troy Walters  |    3.84 | 2000 |
+---------------+---------+------+
```

* Broad Jump

`SELECT name, broad_jump, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE broad_jump != 0 ORDER BY broad_jump DESC LIMIT 5`

```
+----------------+------------+------+
| name           | broad_jump | year |
+----------------+------------+------+
| Byron Jones    |        147 | 2015 |
| Juan Thornhill |        141 | 2019 |
| Obi Melifonwu  |        141 | 2017 |
| Miles Boykin   |        140 | 2019 |
| Chris Conley   |        139 | 2015 |
+----------------+------------+------+
```


### Closing Thoughts
All these stats look nice and all, but what indicator do they have on the players success? Well, lets take for instance the best QB's in the fourty yard dash:

`SELECT id, name, fourty_yard_dash, year from draft INNER JOIN combine ON draft.id = combine.player_index WHERE fourty_yard_dash AND position = 'QB' != 0 ORDER BY fourty_yard_dash ASC LIMIT 5;`

```
+------+---------------+------------------+------+
| id   | name          | fourty_yard_dash | year |
+------+---------------+------------------+------+
|   90 | Michael Vick  |             4.33 | 2001 |
|  695 | Reggie McNeal |              4.4 | 2006 |
| 2472 | Justin Fields |             4.45 | 2021 |
| 1242 | Tyrod Taylor  |             4.47 | 2011 |
|  610 | Vince Young   |             4.48 | 2006 |
+------+---------------+------------------+------+
```

There are some notable names in there, for instance Chicago Bears' QB Justin Fields & Michael Vick. However, also Reggie McNeal is in there?
In my personal opinion, these dash excerices are somewhat of a good metric. It can indicate how well the QB can operate when the pocket collapses and the QB has to scramble. It is not however, the end all be all of the players draft stock.

There is one more player I would like to mention:

`SELECT fourty_yard_dash, vertical, broad_jump, cone_drill, shuttle FROM combine WHERE player_index = (SELECT id FROM draft WHERE name = 'Tom Brady');`

```
+------------------+----------+------------+------------+---------+
| fourty_yard_dash | vertical | broad_jump | cone_drill | shuttle |
+------------------+----------+------------+------------+---------+
|             5.28 |     24.5 |         99 |        7.2 |    4.38 |
+------------------+----------+------------+------------+---------+
```

Lets compare these stats to that years average.

` SELECT ROUND(AVG(fourty_yard_dash),2), ROUND(AVG(vertical),2), ROUND(AVG(broad_jump),2), ROUND(AVG(cone_drill),2), ROUND(AVG(shuttle),2) FROM combine INNER JOIN draft ON draft.id = combine.player_index WHERE draft.year = (SELECT year FROM draft WHERE name = 'Tom Brady');`

```
+--------------------------------+------------------------+--------------------------+--------------------------+-----------------------+
| ROUND(AVG(fourty_yard_dash),2) | ROUND(AVG(vertical),2) | ROUND(AVG(broad_jump),2) | ROUND(AVG(cone_drill),2) | ROUND(AVG(shuttle),2) |
+--------------------------------+------------------------+--------------------------+--------------------------+-----------------------+
|                           4.65 |                  22.71 |                    82.97 |                     4.35 |                  2.55 |
+--------------------------------+------------------------+--------------------------+--------------------------+-----------------------+
```

As you can see, apart from the three cones drill, his combine statistics are pretty underwhelming.
