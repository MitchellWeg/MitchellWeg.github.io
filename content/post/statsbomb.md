---
title: "Analyzing Women's World Cup statistics with Statsbomb"
date: "2024-01-09"
tags: ["football", "statsbomb", "statistics"]
---


### The Code

First, lets import Statsbomb:

```
import statsbombpy as sb
```

Lets check which competition id belongs to the 2022 Womens world cup:

```
sb.competitions()

	competition_id	season_id	country_name	competition_name	competition_gender	competition_youth	competition_international	season_name	match_updated	match_updated_360	match_available_360	match_available
0	9	27	Germany	1. Bundesliga	male	False	False	2015/2016	2023-12-12T07:43:33.436182	None	None	2023-12-12T07:43:33.436182
1	16	4	Europe	Champions League	male	False	False	2018/2019	2023-03-07T12:20:48.118250	2021-06-13T16:17:31.694	None	2023-03-07T12:20:48.118250
2	16	1	Europe	Champions League	male	False	False	2017/2018	2021-08-27T11:26:39.802832	2021-06-13T16:17:31.694	None	2021-01-23T21:55:30.425330
3	16	2	Europe	Champions League	male	False	False	2016/2017	2021-08-27T11:26:39.802832	2021-06-13T16:17:31.694	None	2020-07-29T05:00
4	16	27	Europe	Champions League	male	False	False	2015/2016	2021-08-27T11:26:39.802832	2021-06-13T16:17:31.694	None	2020-07-29T05:00
...	...	...	...	...	...	...	...	...	...	...	...	...
65	55	43	Europe	UEFA Euro	male	False	True	2020	2023-02-24T21:26:47.128979	2023-04-27T22:38:34.970148	2023-04-27T22:38:34.970148	2023-02-24T21:26:47.128979
66	35	75	Europe	UEFA Europa League	male	False	False	1988/1989	2023-06-18T19:28:39.443883	2021-06-13T16:17:31.694	None	2023-06-18T19:28:39.443883
67	53	106	Europe	UEFA Women's Euro	female	False	True	2022	2023-10-24T03:36:54.066267	2023-10-24T03:37:29.085948	2023-10-24T03:37:29.085948	2023-10-24T03:36:54.066267
68	72	107	International	Women's World Cup	female	False	True	2023	2023-12-12T14:06:50.626363	2023-12-12T14:12:41.561162	2023-12-12T14:12:41.561162	2023-12-12T14:06:50.626363
69	72	30	International	Women's World Cup	female	False	True	2019	2023-07-27T10:33:48.273734	2021-06-13T16:17:31.694	None	2023-07-27T10:33:48.273734
```

The 2022 Womens World Cup has competition_id **72** and season_id **107**. We can use that to get all the matches:

```
matches = sb.matches(72, 107)
matches.head()

	match_id	match_date	kick_off	competition	season	home_team	away_team	home_score	away_score	match_status	...	last_updated_360	match_week	competition_stage	stadium	referee	home_managers	away_managers	data_version	shot_fidelity_version	xy_fidelity_version
0	3904629	2023-08-16	13:00:00.000	International - Women's World Cup	2023	Australia Women's	England Women's	1	3	available	...	2023-08-30T11:17:47.551826	6	Semi-finals	Accor Stadium	Tori Penso	Tony Gustavsson	Sarina Glotzbach-Wiegman	1.1.0	2	2
1	3906390	2023-08-20	13:00:00.000	International - Women's World Cup	2023	Spain Women's	England Women's	1	0	available	...	2023-08-22T19:38:43.965521	7	Final	Accor Stadium	Tori Penso	Jorge Vilda	Sarina Glotzbach-Wiegman	1.1.0	2	2
2	3906389	2023-08-19	11:00:00.000	International - Women's World Cup	2023	Sweden Women's	Australia Women's	2	0	available	...	2023-08-29T21:19:57.035390	7	3rd Place Final	Brisbane Stadium	Cheryl Foster	Peter Gerhardsson	Tony Gustavsson	1.1.0	2	2
3	3904628	2023-08-15	11:00:00.000	International - Women's World Cup	2023	Spain Women's	Sweden Women's	2	1	available	...	2023-08-16T11:30:31.556462	6	Semi-finals	Eden Park	Edina Alves Batista	Jorge Vilda	Peter Gerhardsson	1.1.0	2	2
4	3893806	2023-07-26	10:30:00.000	International - Women's World Cup	2023	Spain Women's	Zambia Women's	5	0	available	...	2023-08-15T02:58:57.266594	2	Group Stage	Eden Park	Hyeon-Jeong Oh	Jorge Vilda	Bruce Mwape	1.1.0	2	2
```

Within this dataframe, we're only really interested in the **match_id**. Lets put it into a list, so we can query all the events:

```
ids = list(matches['match_id'])
```
In the statsbomb python package, there exists a separate function to query the events of a particular match.
To simplify the querying process, lets create a function that creates a Pandas DataFrame of the match events:

```
def get_match_events(id):
    df = sb.events(id)
    return df
```

Now we can call the function and get that particular dataframe.

We want to analyze the crosses of all matches, so lets get all events from all matches, and put them into one big DataFrame:

```
frames = []
for id in ids:
    new_df = get_match_events(id)
    frames.append(new_df)
    
df = pd.concat(frames)
df
```

Which then yields the following (huge) DataFrame:

```
	50_50	bad_behaviour_card	ball_receipt_outcome	ball_recovery_recovery_failure	block_deflection	carry_end_location	clearance_aerial_won	clearance_body_part	clearance_head	clearance_left_foot	...	goalkeeper_success_in_play	shot_saved_off_target	goalkeeper_punched_out	block_save_block	goalkeeper_penalty_saved_to_post	dribble_no_touch	goalkeeper_success_out	shot_follows_dribble	player_off_permanent	shot_redirect
0	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
1	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
2	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
3	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
4	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
3972	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
3973	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
3974	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
3975	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
3976	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
```

Consulting the [Statsbomb events manual](https://github.com/statsbomb/open-data/blob/master/doc/Open%20Data%20Events%20v4.0.0.pdf), it states that a **pass_cross** field exists in the DataFrame. Lets use it:

```
crosses = df[df['pass_cross'] == True]
```

```
	50_50	bad_behaviour_card	ball_receipt_outcome	ball_recovery_recovery_failure	block_deflection	carry_end_location	clearance_aerial_won	clearance_body_part	clearance_head	clearance_left_foot	...	goalkeeper_success_in_play	shot_saved_off_target	goalkeeper_punched_out	block_save_block	goalkeeper_penalty_saved_to_post	dribble_no_touch	goalkeeper_success_out	shot_follows_dribble	player_off_permanent	shot_redirect
63	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
135	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
151	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
251	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
315	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
939	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
942	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
957	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
1035	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN
1103	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN	NaN

```

Lets first plot from where all these crosses are being launched:

```
crosses['location']
```

```
63       [87.3, 73.5]
135      [107.5, 2.5]
151      [93.1, 70.6]
251      [85.9, 15.5]
315     [117.2, 55.5]
            ...      
939     [116.6, 63.9]
942     [114.9, 68.1]
957      [98.6, 69.7]
1035    [116.8, 56.7]
1103    [116.6, 70.9]
Name: location, Length: 1438, dtype: object
```

This, of course, does not say much, so lets try to plot it on an actual pitch. For this we can use the *mlpsoccer* python package:

```
from mplsoccer import Pitch

def draw_pitch(data):
    pitch = Pitch(pitch_type='statsbomb')
    fig, ax = pitch.draw(figsize=(8, 4))

    for i in data:
        plt.plot(i[0], i[1], "o",
             color="blue")
```

Which then yields the following plot:

![](/images/first-pitch-plot.png)

Which isn't very pretty. A heatmap would be more appropriate here:

```
from mplsoccer import Pitch
def draw_pitch_heatmap(data):
# setup pitch
    pitch = Pitch(pitch_type='statsbomb', line_zorder=2,
                  pitch_color='#22312b', line_color='#efefef')
    # draw
    fig, ax = pitch.draw(figsize=(6.6, 4.125))
    fig.set_facecolor('#22312b')
    bin_statistic = pitch.bin_statistic([x[0] for x in data], [y[1] for y in data], statistic='count', bins=(25, 25))
    bin_statistic['statistic'] = gaussian_filter(bin_statistic['statistic'], 1)
    pcm = pitch.heatmap(bin_statistic, ax=ax, cmap='hot', edgecolors='#22312b')
    # Add the colorbar and format off-white
    cbar = fig.colorbar(pcm, ax=ax, shrink=0.6)
    cbar.outline.set_edgecolor('#efefef')
    cbar.ax.yaxis.set_tick_params(color='#efefef')
    ticks = plt.setp(plt.getp(cbar.ax.axes, 'yticklabels'), color='#efefef')
```

Which gives us a better view on where most of the crosses come from:

![](/images/pitch-heatmap.png)

Lets plot all crosses that resulted in a goal:

```
succesful_crosses = crosses[crosses['pass_goal_assist'].notna()]
draw_pitch_heatmap(succesful_crosses['location'])
```

![](/images/succesful-crosses-heatmap.png)

Here we can see that far more (successful) crosses come from the right side.

Let's talk about shots. A shot, in Statsbombs terms, is an attempt by a player to score a goal with any legal part of the body. If we take a look at the chart, we can see from where most shots are attempted:

![](/images/shots.png)

We can draw a different heatmap that shows the percentages associated with the shots taken:

```
from mplsoccer import VerticalPitch

def draw_bigger_heatmap(data):
    pitch = VerticalPitch(pitch_type='statsbomb', line_zorder=2, pitch_color='#f4edf0')
    fig, ax = pitch.draw(figsize=(4.125, 6))
    fig.set_facecolor('#f4edf0')
    bin_statistic = pitch.bin_statistic([x[0] for x in data], [y[1] for y in data], statistic='count', bins=(10, 9), normalize=True)
    pitch.heatmap(bin_statistic, ax=ax, cmap='Reds', edgecolor='#f9f9f9')
    labels = pitch.label_heatmap(bin_statistic, color='#f4edf0', fontsize=10,
                                 ax=ax, ha='center', va='center',
                                 str_format='{:.0%}')
```

![](/images/heatmap-percentages.png)

That was it! a comprehensive and first look on the open dataset provided by Statsbomb. I'm sure there is a lot more to find in this treasure trove of data.