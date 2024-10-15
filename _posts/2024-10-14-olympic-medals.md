---
title: Olympic medals
date: 2024-10-14 20:30:00 -0400
categories: [Sports, Olympics]
tags: [kaggle, pandas, viz, correlation]     # TAG names should always be lowercase

description: Olympic medal counts are kind of boring - they're highly correlated with team size. Medal rates tell a more interesting story.
---

***
***
## 0. Setup


```python
# Load the autoreload extension
%load_ext autoreload

# Enable autoreloading for all modules
%autoreload 2

# Import local modules
from util_core import *
import olympic_medals.util_olympic_medals as U

```

    The autoreload extension is already loaded. To reload it, use:
      %reload_ext autoreload


***
***
## 1. Data load


```python
# Base loads
all_data = U.load_base_datasets()

medal_df = all_data["medal_df"]
event_df = all_data["event_df"]
team_df = all_data["team_df"]
athlete_df = all_data["athlete_df"]

# Combined entry data
entry_df = U.build_combined_event_entries(team_df, athlete_df)
all_data["entry_df"] = entry_df

# Store
save_object(all_data, "in/olympic_medals_all_data.p")

```

    Notice: Dropped 358/1698 (21.1%) non-current team records.
    null_df: 0
    Notice: Dropped 3/11113 (0.0%) non-current team records.
    Exploded to 14978 records, one per athlete per event entered.
    Notice: Dropped 435/14978 (2.9%) invalid event records.
    Notice: Dropped 6494/14543 (44.7%) team event records.


#### 1.1 Medals


```python
# Side quest - how many medals were awarded in each event?
count_df = medal_df["event_key"].value_counts().to_frame().reset_index()

# Print summary stats
total_e = len(count_df)
gt3 = len(count_df[count_df["count"]>3])
eq3 = len(count_df[count_df["count"]==3])
lt3 = len(count_df[count_df["count"]<3])
print(f"Isolated a total of {total_e} events awarding {len(medal_df)} cummulative medals...")
print(f"{gt3} ({gt3/total_e*100:.1f}%) events awarded 4+ medals")
print(f"{eq3} ({eq3/total_e*100:.1f}%) events awarded 3 medals")
print(f"{lt3} ({lt3/total_e*100:.1f}%) events awarded <3 medals")

# Look at 57 events with 4+ medals
gt3_df = count_df[count_df["count"]>3].copy()
gt3_df["discipline"] = gt3_df["event_key"].apply(lambda x: x.split(": ")[0])
print(gt3_df["discipline"].value_counts())

# View sample
medal_df.head()

```

    Isolated a total of 329 events awarding 1044 cummulative medals...
    57 (17.3%) events awarded 4+ medals
    272 (82.7%) events awarded 3 medals
    0 (0.0%) events awarded <3 medals
    discipline
    Wrestling              18
    Judo                   15
    Boxing                 13
    Taekwondo               8
    Athletics               1
    Canoe Sprint            1
    Artistic Gymnastics     1
    Name: count, dtype: int64





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>medal_type</th>
      <th>medal_code</th>
      <th>medal_date</th>
      <th>name</th>
      <th>gender</th>
      <th>discipline</th>
      <th>event</th>
      <th>event_type</th>
      <th>url_event</th>
      <th>code</th>
      <th>country_code</th>
      <th>country</th>
      <th>country_long</th>
      <th>event_key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Gold Medal</td>
      <td>1.0</td>
      <td>2024-07-27</td>
      <td>Remco EVENEPOEL</td>
      <td>M</td>
      <td>Cycling Road</td>
      <td>Men's Individual Time Trial</td>
      <td>ATH</td>
      <td>/en/paris-2024/results/cycling-road/men-s-indi...</td>
      <td>1903136</td>
      <td>BEL</td>
      <td>Belgium</td>
      <td>Belgium</td>
      <td>Cycling Road: Men's Individual Time Trial</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Silver Medal</td>
      <td>2.0</td>
      <td>2024-07-27</td>
      <td>Filippo GANNA</td>
      <td>M</td>
      <td>Cycling Road</td>
      <td>Men's Individual Time Trial</td>
      <td>ATH</td>
      <td>/en/paris-2024/results/cycling-road/men-s-indi...</td>
      <td>1923520</td>
      <td>ITA</td>
      <td>Italy</td>
      <td>Italy</td>
      <td>Cycling Road: Men's Individual Time Trial</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bronze Medal</td>
      <td>3.0</td>
      <td>2024-07-27</td>
      <td>Wout van AERT</td>
      <td>M</td>
      <td>Cycling Road</td>
      <td>Men's Individual Time Trial</td>
      <td>ATH</td>
      <td>/en/paris-2024/results/cycling-road/men-s-indi...</td>
      <td>1903147</td>
      <td>BEL</td>
      <td>Belgium</td>
      <td>Belgium</td>
      <td>Cycling Road: Men's Individual Time Trial</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Gold Medal</td>
      <td>1.0</td>
      <td>2024-07-27</td>
      <td>Grace BROWN</td>
      <td>W</td>
      <td>Cycling Road</td>
      <td>Women's Individual Time Trial</td>
      <td>ATH</td>
      <td>/en/paris-2024/results/cycling-road/women-s-in...</td>
      <td>1940173</td>
      <td>AUS</td>
      <td>Australia</td>
      <td>Australia</td>
      <td>Cycling Road: Women's Individual Time Trial</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Silver Medal</td>
      <td>2.0</td>
      <td>2024-07-27</td>
      <td>Anna HENDERSON</td>
      <td>W</td>
      <td>Cycling Road</td>
      <td>Women's Individual Time Trial</td>
      <td>ATH</td>
      <td>/en/paris-2024/results/cycling-road/women-s-in...</td>
      <td>1912525</td>
      <td>GBR</td>
      <td>Great Britain</td>
      <td>Great Britain</td>
      <td>Cycling Road: Women's Individual Time Trial</td>
    </tr>
  </tbody>
</table>
</div>



#### 1.2 Events


```python
# Print summary stats and view sample
total_e2 = len(event_df)
assert total_e2 == total_e, f"Warning total_e: {total_e}, total_e2: {total_e2}"
print(f"Isolated a total of {total_e2} events...")
event_df.head()

```

    Isolated a total of 329 events...





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>event</th>
      <th>tag</th>
      <th>sport</th>
      <th>sport_code</th>
      <th>sport_url</th>
      <th>event_key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Men's Individual</td>
      <td>archery</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>https://olympics.com/en/paris-2024/sports/archery</td>
      <td>Archery: Men's Individual</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Women's Individual</td>
      <td>archery</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>https://olympics.com/en/paris-2024/sports/archery</td>
      <td>Archery: Women's Individual</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Men's Team</td>
      <td>archery</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>https://olympics.com/en/paris-2024/sports/archery</td>
      <td>Archery: Men's Team</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Women's Team</td>
      <td>archery</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>https://olympics.com/en/paris-2024/sports/archery</td>
      <td>Archery: Women's Team</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Mixed Team</td>
      <td>archery</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>https://olympics.com/en/paris-2024/sports/archery</td>
      <td>Archery: Mixed Team</td>
    </tr>
  </tbody>
</table>
</div>



#### 1.3 Teams


```python
# View sample
team_df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>code</th>
      <th>current</th>
      <th>team</th>
      <th>team_gender</th>
      <th>country_code</th>
      <th>country</th>
      <th>country_long</th>
      <th>discipline</th>
      <th>disciplines_code</th>
      <th>events</th>
      <th>athletes</th>
      <th>coaches</th>
      <th>athletes_codes</th>
      <th>num_athletes</th>
      <th>coaches_codes</th>
      <th>num_coaches</th>
      <th>event_key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ARCMTEAM3---CHN01</td>
      <td>True</td>
      <td>People's Republic of China</td>
      <td>M</td>
      <td>CHN</td>
      <td>China</td>
      <td>People's Republic of China</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>Men's Team</td>
      <td>['KAO Wenchao', 'LI Zhongyuan', 'WANG Yan']</td>
      <td>NaN</td>
      <td>['1913366', '1913367', '1913369']</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Archery: Men's Team</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ARCMTEAM3---COL01</td>
      <td>True</td>
      <td>Colombia</td>
      <td>M</td>
      <td>COL</td>
      <td>Colombia</td>
      <td>Colombia</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>Men's Team</td>
      <td>['ARCILA Santiago', 'ENRIQUEZ Jorge', 'HERNAND...</td>
      <td>NaN</td>
      <td>['1935642', '1543412', '1935644']</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Archery: Men's Team</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ARCMTEAM3---FRA01</td>
      <td>True</td>
      <td>France</td>
      <td>M</td>
      <td>FRA</td>
      <td>France</td>
      <td>France</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>Men's Team</td>
      <td>['ADDIS Baptiste', 'CHIRAULT Thomas', 'VALLADO...</td>
      <td>NaN</td>
      <td>['1541270', '1541272', '1541275']</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Archery: Men's Team</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ARCMTEAM3---GBR01</td>
      <td>True</td>
      <td>Great Britain</td>
      <td>M</td>
      <td>GBR</td>
      <td>Great Britain</td>
      <td>Great Britain</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>Men's Team</td>
      <td>['HALL Conor', 'HALL Tom', 'WISE Alex']</td>
      <td>NaN</td>
      <td>['1560988', '1560989', '1561003']</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Archery: Men's Team</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ARCMTEAM3---IND01</td>
      <td>True</td>
      <td>India</td>
      <td>M</td>
      <td>IND</td>
      <td>India</td>
      <td>India</td>
      <td>Archery</td>
      <td>ARC</td>
      <td>Men's Team</td>
      <td>['BOMMADEVARA Dhiraj', 'JADHAV Pravin Ramesh',...</td>
      <td>NaN</td>
      <td>['1546108', '1546112', '1546110']</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Archery: Men's Team</td>
    </tr>
  </tbody>
</table>
</div>



#### 1.4 Athletes


```python
# View sample
athlete_df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>code</th>
      <th>current</th>
      <th>name</th>
      <th>name_short</th>
      <th>name_tv</th>
      <th>gender</th>
      <th>function</th>
      <th>country_code</th>
      <th>country</th>
      <th>country_long</th>
      <th>...</th>
      <th>lang</th>
      <th>coach</th>
      <th>reason</th>
      <th>hero</th>
      <th>influence</th>
      <th>philosophy</th>
      <th>sporting_relatives</th>
      <th>ritual</th>
      <th>other_sports</th>
      <th>event_key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1532872</td>
      <td>True</td>
      <td>ALEKSANYAN Artur</td>
      <td>ALEKSANYAN A</td>
      <td>Artur ALEKSANYAN</td>
      <td>Male</td>
      <td>Athlete</td>
      <td>ARM</td>
      <td>Armenia</td>
      <td>Armenia</td>
      <td>...</td>
      <td>Armenian, English, Russian</td>
      <td>Gevorg Aleksanyan (ARM), father</td>
      <td>He followed his father and his uncle into the ...</td>
      <td>Footballer Zinedine Zidane (FRA), World Cup wi...</td>
      <td>His father, Gevorg Aleksanyan</td>
      <td>"Wrestling is my life." (mediamax.am. 18 May 2...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Wrestling: Men's Greco-Roman 97kg</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1532873</td>
      <td>True</td>
      <td>AMOYAN Malkhas</td>
      <td>AMOYAN M</td>
      <td>Malkhas AMOYAN</td>
      <td>Male</td>
      <td>Athlete</td>
      <td>ARM</td>
      <td>Armenia</td>
      <td>Armenia</td>
      <td>...</td>
      <td>Armenian</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>"To become a good athlete, you first have to b...</td>
      <td>Uncle, Roman Amoyan (wrestling), 2008 Olympic ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Wrestling: Men's Greco-Roman 77kg</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1532874</td>
      <td>True</td>
      <td>GALSTYAN Slavik</td>
      <td>GALSTYAN S</td>
      <td>Slavik GALSTYAN</td>
      <td>Male</td>
      <td>Athlete</td>
      <td>ARM</td>
      <td>Armenia</td>
      <td>Armenia</td>
      <td>...</td>
      <td>Armenian</td>
      <td>Personal: Martin Alekhanyan (ARM).&lt;br&gt;National...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Wrestling: Men's Greco-Roman 67kg</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1532944</td>
      <td>True</td>
      <td>HARUTYUNYAN Arsen</td>
      <td>HARUTYUNYAN A</td>
      <td>Arsen HARUTYUNYAN</td>
      <td>Male</td>
      <td>Athlete</td>
      <td>ARM</td>
      <td>Armenia</td>
      <td>Armenia</td>
      <td>...</td>
      <td>Armenian</td>
      <td>National: Habetnak Kurghinyan</td>
      <td>While doing karate he noticed wrestlers traini...</td>
      <td>Wrestler Armen Nazaryan (ARM, BUL), two-time O...</td>
      <td>NaN</td>
      <td>“Nothing is impossible, set goals in front of ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Wrestling: Men's Freestyle 57kg</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1532945</td>
      <td>True</td>
      <td>TEVANYAN Vazgen</td>
      <td>TEVANYAN V</td>
      <td>Vazgen TEVANYAN</td>
      <td>Male</td>
      <td>Athlete</td>
      <td>ARM</td>
      <td>Armenia</td>
      <td>Armenia</td>
      <td>...</td>
      <td>Armenian, Russian</td>
      <td>National: Habetnak Kurghinyan (ARM)</td>
      <td>“My family did not like wrestling very much. A...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Wrestling: Men's Freestyle 65kg</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 37 columns</p>
</div>



#### 1.5 Combined entries


```python
# View sample
entry_df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>event_key</th>
      <th>gender</th>
      <th>country_code</th>
      <th>country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Archery: Men's Team</td>
      <td>M</td>
      <td>CHN</td>
      <td>China</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Archery: Men's Team</td>
      <td>M</td>
      <td>COL</td>
      <td>Colombia</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Archery: Men's Team</td>
      <td>M</td>
      <td>FRA</td>
      <td>France</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Archery: Men's Team</td>
      <td>M</td>
      <td>GBR</td>
      <td>Great Britain</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Archery: Men's Team</td>
      <td>M</td>
      <td>IND</td>
      <td>India</td>
    </tr>
  </tbody>
</table>
</div>



***
***
## 2. Medal rates


```python
# Load
all_data = load_object("in/olympic_medals_all_data.p")

medal_df = all_data["medal_df"]
event_df = all_data["event_df"]
team_df = all_data["team_df"]
athlete_df = all_data["athlete_df"]
entry_df = all_data["entry_df"]

```


```python
# Now...we can count entries by country and medals by country

# Entries
country_entry_df = entry_df["country"].value_counts().to_frame().reset_index().rename(columns={"count":"entries"})

# Medals
country_medal_df = medal_df["country"].value_counts().to_frame().reset_index().rename(columns={"count":"total_medals"})
country_medal_detail_df = medal_df[["country", "medal_type"]].value_counts().to_frame().reset_index().rename(columns={"count":"medals"})
country_medal_detail_df = pd.pivot_table(country_medal_detail_df, index="country", columns="medal_type", values="medals")
country_medal_detail_df.columns = ["_".join(x.lower().split(" "))+"s" for x in country_medal_detail_df.columns]
detail_cols = ["gold_medals", "silver_medals", "bronze_medals"]
country_medal_df = pd.merge(country_medal_df, country_medal_detail_df[detail_cols].reset_index(), how="left", on="country")
country_medal_df[detail_cols] = country_medal_df[detail_cols].fillna(0).astype(int)

# Validate medal details
country_medal_df["checksum"] = country_medal_df[detail_cols].sum(axis=1)
country_medal_df["checksum_match"] = country_medal_df["checksum"] == country_medal_df["total_medals"]
assert country_medal_df["checksum_match"].sum() == len(country_medal_df)
country_medal_df.drop(columns=["checksum", "checksum_match"], inplace=True)

# Join
country_df = pd.merge(country_entry_df, country_medal_df, how="left", on="country")
country_df[["total_medals"]+detail_cols] = country_df[["total_medals"]+detail_cols].fillna(0).astype(int)

# Weighted medal count
def calc_weighted_medals(x, weights=[2, 1.5, 1], rate=False):
    gold_sum = x["gold_medals"] * weights[0]
    silver_sum = x["silver_medals"] * weights[1]
    bronze_sum = x["bronze_medals"] * weights[2]
    result = gold_sum + silver_sum + bronze_sum
    if rate: result /= x["entries"]
    return result
country_df["total_medals_weighted"] = country_df.apply(lambda x: calc_weighted_medals(x), axis=1)

# Unweighted medal rate
country_df["medal_rate_unweighted"] = country_df["total_medals"] / country_df["entries"]

# Weighted medal rate
country_df["medal_rate_weighted"] = country_df.apply(lambda x: calc_weighted_medals(x, rate=True), axis=1)

# Add ranks and sort
country_df["medal_rate_unweighted_rank"] = country_df["medal_rate_unweighted"].rank(method="min", ascending=False)
country_df["medal_rate_weighted_rank"] = country_df["medal_rate_weighted"].rank(method="min", ascending=False)
country_df = country_df.sort_values(["medal_rate_weighted_rank", "medal_rate_unweighted_rank"]).reset_index(drop=True)

# Export and view sample
country_df.to_csv(f"out/country_medal_rankings.csv", index=False)
country_df

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>entries</th>
      <th>total_medals</th>
      <th>gold_medals</th>
      <th>silver_medals</th>
      <th>bronze_medals</th>
      <th>total_medals_weighted</th>
      <th>medal_rate_unweighted</th>
      <th>medal_rate_weighted</th>
      <th>medal_rate_unweighted_rank</th>
      <th>medal_rate_weighted_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Saint Lucia</td>
      <td>5</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>3.5</td>
      <td>0.400000</td>
      <td>0.700000</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Dominica</td>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>2.0</td>
      <td>0.250000</td>
      <td>0.500000</td>
      <td>9.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>DPR Korea</td>
      <td>15</td>
      <td>6</td>
      <td>0</td>
      <td>2</td>
      <td>4</td>
      <td>7.0</td>
      <td>0.400000</td>
      <td>0.466667</td>
      <td>1.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>IR Iran</td>
      <td>41</td>
      <td>12</td>
      <td>3</td>
      <td>6</td>
      <td>3</td>
      <td>18.0</td>
      <td>0.292683</td>
      <td>0.439024</td>
      <td>5.0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bahrain</td>
      <td>15</td>
      <td>4</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>6.5</td>
      <td>0.266667</td>
      <td>0.433333</td>
      <td>6.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>201</th>
      <td>Solomon Islands</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>93.0</td>
      <td>93.0</td>
    </tr>
    <tr>
      <th>202</th>
      <td>Liechtenstein</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>93.0</td>
      <td>93.0</td>
    </tr>
    <tr>
      <th>203</th>
      <td>Somalia</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>93.0</td>
      <td>93.0</td>
    </tr>
    <tr>
      <th>204</th>
      <td>Belize</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>93.0</td>
      <td>93.0</td>
    </tr>
    <tr>
      <th>205</th>
      <td>Nauru</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>93.0</td>
      <td>93.0</td>
    </tr>
  </tbody>
</table>
<p>206 rows × 11 columns</p>
</div>



***
***
## 3. Plots

#### 3.1 Medal Wins vs Chances


```python
# Setup
config = {
    # Data
    "name_col": "country",
    "size_col": "entries",
    "color_col": "entries",
    "x_axis_col": "entries",
    "y_axis_col": "total_medals",
    # Labels
    "title": "Medal Wins vs Chances",
    "x_axis_title": "Medal Chances",
    "y_axis_title": "Medal Wins",
    # Formatting
    "min_point_size": 5,
    "max_point_size": 30,
    "x_axis_dtick": 20,
    "y_axis_dtick": 10,
    "width": 800,
    "height": 800
}

# Plot and save html version
fig = plotly_scatter_v1(country_df, config)
fig.write_html("md/olympic_medals/medal_wins_vs_chances.html")

```

{% include olympic_medals/medal_wins_vs_chances.html %}

#### 3.2 Weighted Medal Wins vs Chances


```python
# Setup
config = {
    # Data
    "name_col": "country",
    "size_col": "entries",
    "color_col": "entries",
    "x_axis_col": "entries",
    "y_axis_col": "total_medals_weighted",
    # Labels
    "title": "Weighted Medal Wins vs Chances",
    "x_axis_title": "Medal Chances",
    "y_axis_title": "Weighted Medal Wins (Gold = 2, Silver = 1.5, Bronze = 1)",
    # Formatting
    "min_point_size": 5,
    "max_point_size": 30,
    "x_axis_dtick": 20,
    "y_axis_dtick": 10,
    "width": 800,
    "height": 800
}

# Plot and save html version
fig = plotly_scatter_v1(country_df, config)
fig.write_html("md/olympic_medals/weighted_medal_wins_vs_chances.html")

```

{% include olympic_medals/weighted_medal_wins_vs_chances.html %}

#### 3.3 Medal Rate vs Chances


```python
# Setup
config = {
    # Data
    "name_col": "country",
    "size_col": "entries",
    "color_col": "entries",
    "x_axis_col": "entries",
    "y_axis_col": "medal_rate_unweighted",
    # Labels
    "title": "Medal Rate vs Chances",
    "x_axis_title": "Medal Chances",
    "y_axis_title": "Medal Rate",
    # Formatting
    "min_point_size": 5,
    "max_point_size": 30,
    "x_axis_dtick": 20,
    "y_axis_dtick": 0.1,
    "width": 800,
    "height": 800
}

# Plot and save html version
fig = plotly_scatter_v1(country_df, config)
fig.write_html("md/olympic_medals/medal_rate_vs_chances.html")

```

{% include olympic_medals/medal_rate_vs_chances.html %}

#### 3.4 Weighted Medal Rate vs Chances


```python
# Setup
config = {
    # Data
    "name_col": "country",
    "size_col": "entries",
    "color_col": "entries",
    "x_axis_col": "entries",
    "y_axis_col": "medal_rate_weighted",
    # Labels
    "title": "Weighted Medal Rate vs Chances",
    "x_axis_title": "Medal Chances",
    "y_axis_title": "Weighted Medal Rate (Gold = 2, Silver = 1.5, Bronze = 1)",
    # Formatting
    "min_point_size": 5,
    "max_point_size": 30,
    "x_axis_dtick": 20,
    "y_axis_dtick": 0.1,
    "width": 800,
    "height": 800
}

# Plot and save html version
fig = plotly_scatter_v1(country_df, config)
fig.write_html("md/olympic_medals/weighted_medal_rate_vs_chances.html")

```

{% include olympic_medals/weighted_medal_rate_vs_chances.html %}


```python

```

***
***
## 3. Other analysis


```python
country_df[["entries", "total_medals"]].corr()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>entries</th>
      <th>total_medals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>entries</th>
      <td>1.00000</td>
      <td>0.89764</td>
    </tr>
    <tr>
      <th>total_medals</th>
      <td>0.89764</td>
      <td>1.00000</td>
    </tr>
  </tbody>
</table>
</div>




```python
country_df[["entries", "medal_rate_unweighted", "medal_rate_weighted"]].corr()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>entries</th>
      <th>medal_rate_unweighted</th>
      <th>medal_rate_weighted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>entries</th>
      <td>1.000000</td>
      <td>0.372375</td>
      <td>0.392037</td>
    </tr>
    <tr>
      <th>medal_rate_unweighted</th>
      <td>0.372375</td>
      <td>1.000000</td>
      <td>0.976465</td>
    </tr>
    <tr>
      <th>medal_rate_weighted</th>
      <td>0.392037</td>
      <td>0.976465</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
