---
title: "The Statistical Significance of the NFL Combine - Part 1"
date: "2023-04-09"
tags: ["nfl", "combine", "statistics"]
---

### Preamble
Probably everyone has at least a dream of playing a professional sport at least once in their lives. Unfortunately, this is only for the the best of the best, and it's pretty uncommon for us common folk to ever surpass the semi-pro level. 

While my own chess rating barely exceeds the 1500's, it's always fun to look up at the pro's, and with football that is no different.

The NFL combine is a way for the future stars of the NFL to impress the scouts, and possibly get an edge on their draft stock. In this blog series, we will do a deep dive into what makes the NFL combine tick, and what the statistical significance is of these try-outs.

### What is the combine?
The NFL Scouting Combine is a week long event in Indianapolis where participants perform physical and mental excersises in front of scouts, GM's and the sort. This event is invite only and participants aim to up their draft stock and salary.

The excersises include:
- 40-yard dash
- Bench pressing of 225 pounds (102 kg)
- Vertical jump
- Broad jump
- 20-yard shuttle
- 3 cone drill
- 60-yard shuttle
- Position-specific drills
- Interviews 
- Physical and injury evaluations
- Drug screening
- Cybex test
- Wonderlic test

Source: [Wiki](https://en.wikipedia.org/wiki/NFL_Scouting_Combine)

### The Dataset
For this, I will use two self made datasets, scraped from [Pro Football Reference](https://www.pro-football-reference.com/): [Dataset 1](https://www.kaggle.com/datasets/mitchellweg1/nfl-draft-dataset-2000-2022) is used to tell which players was drafted at one spot, and [Dataset 2](https://www.kaggle.com/datasets/mitchellweg1/nfl-combine-results-dataset-2000-2022) is used for their respective statistics in the combine.

#### Preparing the dataset
I made these two datasets to be modular, and I didn't have the intention to explicitly have them work together. This means that these datasets share a lot of data. For instance, both datasets have the same player names and the players position. This will add a lot of redundancy to our database when we import it into SQL. That's why we need to prepare the dataset.

Because these are two separate datasets, we'll first have to combine (pun intended) the datasets together.

To separate the player and combine datasets into their own respective dataframes, I wrote the following python function

```
def combine_datasets():
    new_draft_df = pd.DataFrame()
    new_combine_df = pd.DataFrame()
    player_idx = 0

    for year in range(START_YEAR, END_YEAR + 1):
        draft_file = f"{DRAFT_DATA_DIR}/{year}_draft.csv"
        combine_file = f"{COMBINE_DATA_DIR}/{year}_combine.csv"

        draft_df = pd.read_csv(draft_file)
        combine_df = pd.read_csv(combine_file)

        for _, row in draft_df.iterrows():
            pred = combine_df.loc[(combine_df["Player"] == row["Player"]) & (combine_df["Pos"] == row["Pos"])]

            if not pred.empty:
                new_draft_row = {
                    'draft': player_idx,
                    'name': [row["Player"]], 
                    'position': [row["Pos"]], 
                    'college': [pred["School"].to_string(index=False)],
                    'round': [row["Rnd"]],
                    'pick': [row["Pick"]],
                    'year': year,
                    'age': [row["Age"]],
                    'height': [pred["Ht"].to_string(index=False)],
                    'weight': [pred["Wt"].to_string(index=False)],
                }

                new_combine_row = {
                    'player_index': player_idx,
                    'fourty_yard_dash': [pred["40yd"].to_string(index=False)],
                    'vertical': [pred["Vertical"].to_string(index=False)],
                    'bench': [pred["Bench"].to_string(index=False)],
                    'broad_jump': [pred["Broad Jump"].to_string(index=False)],
                    'cone_drill': [pred["3Cone"].to_string(index=False)],
                    'shuttle': [pred["Shuttle"].to_string(index=False)]
                }

                _draft_df = pd.DataFrame(new_draft_row)
                _combine_df = pd.DataFrame(new_combine_row)

                new_draft_df = pd.concat([new_draft_df, _draft_df])
                new_combine_df = pd.concat([new_combine_df, _combine_df])

                player_idx += 1


    return new_draft_df, new_combine_df
```

#### Loading the dataset into a SQL database
Then we can use Pandas to create the SQL statement for us, by using the `.to_sql()` function on a DataFrame.

```python
draft.to_sql('draft', conn, index=False)
combine.to_sql('combine', conn, index=False)
```

Note: for the `conn` argument, you can use any SQLALchemy compatible database.

To go a little bit further, we can add a constraint to our tables:

```sql
ALTER TABLE draft 
ADD CONSTRAINT PK_player_index PRIMARY KEY(index);

ALTER TABLE combine ADD CONSTRAINT FOREIGN KEY (player_index) REFERENCES draft(index);
```

We can verify the results by running:

`SELECT name, position, fourty_yard_dash, year FROM draft INNER JOIN combine ON id = combine.player_index WHERE name = "Tom Brady";`


Which yields:

`Tom Brady|QB|5.28|2000`

And there you go, we now have a working database.
