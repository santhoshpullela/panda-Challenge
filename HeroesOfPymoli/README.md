

```python
import pandas as pd
import numpy as np
import csv
```


```python
# Reading input file to dataframe
filepath = ("purchase_data.json")
df = pd.read_json(filepath)
#df.to_csv("mainjson output.csv", sep =',', encoding='utf-8') - added this step to analyse the data in excel and verify the resp outputs
#df.head()
game_df = pd.DataFrame(df)
game_df.head()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Total Players Count
players_count = len(game_df["SN"].unique())
total_players_pd = pd.DataFrame({"Total Players Count": [players_count]})
total_players_pd
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
      <th>Total Players Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis
purchases_unique = len(game_df["Item ID"].unique())
purchases_total = game_df["Price"].count()
purchases_revenue = game_df["Price"].sum()
puchases_average = round(game_df["Price"].sum()/purchases_total,2)

purc_analysis_pd = pd.DataFrame({"Number of Unique Items":purchases_unique,"Average Purchase Price":puchases_average,
                         "Total Number of Purchases":purchases_total, "Total Revenue":[purchases_revenue]}, 
                            columns =["Number of Unique Items","Average Purchase Price","Total Number of Purchases","Total Revenue"])

purc_analysis_pd["Average Purchase Price"] = purc_analysis_pd["Average Purchase Price"].map("${:.2f}".format)
purc_analysis_pd["Total Revenue"] = purc_analysis_pd["Total Revenue"].map("${:.2f}".format)
purc_analysis_pd

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
      <th>Number of Unique Items</th>
      <th>Average Purchase Price</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Gender Demographics
gender_df = game_df.drop_duplicates(subset=['SN'],keep='first')
gender_df = gender_df.groupby("Gender")
gender_count = gender_df["Age"].count()
gender_percentage = round((gender_count/players_count)*100,2)
gender_analysis = pd.DataFrame({"Players count":gender_count,"Percentage of Players":gender_percentage})
gender_analysis.sort_values(["Players count"],ascending = False)
gender_analysis["Percentage of Players"] = gender_analysis["Percentage of Players"].map("{:.2f}%".format)
gender_analysis
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
      <th>Percentage of Players</th>
      <th>Players count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>81.15%</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40%</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchasing Analysis - Gender based
purc_gender_df = game_df.groupby("Gender")
gender_purchases_count = purc_gender_df["Price"].count()
gender_purchases_average = round(purc_gender_df["Price"].mean(),2)
gender_purchases_total = purc_gender_df["Price"].sum()
gender_purchases_max = gender_purchases_total.max()
gender_purchases_min = gender_purchases_total.min()
gender_normalize_total = (gender_purchases_total - gender_purchases_min)/(gender_purchases_max - gender_purchases_min)
gender_purchase_analysis = pd.DataFrame({"Total Gender Purchases":gender_purchases_count,"Average Gender Purchases": gender_purchases_average,
                                       "Total Purchase Value": gender_purchases_total, "Normalized Totals": gender_normalize_total},
                                        columns =["Total Gender Purchases","Average Gender Purchases","Total Purchase Value","Normalized Totals"])
gender_purchase_analysis
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
      <th>Total Gender Purchases</th>
      <th>Average Gender Purchases</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>2.82</td>
      <td>382.91</td>
      <td>0.189509</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>2.95</td>
      <td>1867.68</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>3.25</td>
      <td>35.74</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Age Analysis
bins = [0, 9, 14, 19, 100]
range_names = ['< 10', '10 - 14', '15 -19', '20+']
game_new_df = game_df
game_new_df["Age Range"] = pd.cut(game_new_df["Age"], bins, labels=range_names)
age_df = game_new_df.drop_duplicates(subset=['SN'],keep='first')
age_analysis_df = age_df.groupby("Age Range")
age_players_count = age_analysis_df["Age"].count()
age_percentage_players = round((age_analysis_df["Age"].count()/players_count)*100,2)
age_analysis_pd = pd.DataFrame({"Players Count":age_players_count,
                             "Percentage of Players": age_percentage_players})
age_analysis_pd["Percentage of Players"] = age_analysis_pd["Percentage of Players"].map("{:.2f}%".format)
age_analysis_pd
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
      <th>Percentage of Players</th>
      <th>Players Count</th>
    </tr>
    <tr>
      <th>Age Range</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; 10</th>
      <td>3.32%</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10 - 14</th>
      <td>4.01%</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15 -19</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20+</th>
      <td>75.22%</td>
      <td>431</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Age Purchase Analysis
age_purc_df = game_new_df.groupby("Age Range")
age_purchases_count = age_purc_df["Price"].count()
age_purchases_average = round(age_purc_df["Price"].mean(),2)
age_purchases_total = age_purc_df["Price"].sum()
age_purchase_max = age_purchases_total.max()
age_purchase_min = age_purchases_total.min()
age_normalize_total = (age_purchases_total-age_purchase_min)/(age_purchase_max - age_purchase_min)
age_purchase_analysis_pd = pd.DataFrame({"Purchase Count":age_purchases_count,"Average Purchase Price": age_purchases_average, 
                                "Total Purchase Value": age_purchases_total, "Normalize Total": age_normalize_total},
                                columns =["Purchase Count","Average Purchase Price","Total Purchase Value","Normalize Total"])

age_purchase_analysis_pd["Average Purchase Price"] = age_purchase_analysis_pd["Average Purchase Price"].map("${:.2f}".format)
age_purchase_analysis_pd["Total Purchase Value"] = age_purchase_analysis_pd["Total Purchase Value"].map("${:.2f}".format)
age_purchase_analysis_pd

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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalize Total</th>
    </tr>
    <tr>
      <th>Age Range</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; 10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>10 - 14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>0.008246</td>
    </tr>
    <tr>
      <th>15 -19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>0.185179</td>
    </tr>
    <tr>
      <th>20+</th>
      <td>584</td>
      <td>$2.94</td>
      <td>$1719.50</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Top 5 Spenders
spenders_df = game_df.groupby("SN")
spenders_count = spenders_df["Price"].count()
spenders_average_price = spenders_df["Price"].mean()
spenders_total = spenders_df["Price"].sum()

spender_analysis_pd = pd.DataFrame({"Purchase Count":spenders_count,"Average Purchase Price": spenders_average_price, 
                                "Total Purchase Value": spenders_total},
                                columns =["Purchase Count","Average Purchase Price","Total Purchase Value"])

total_spender_analysis_pd = spender_analysis_pd.sort_values("Total Purchase Value", ascending = False)
top_spender_analysis_pd = total_spender_analysis_pd.head(5)
top_spender_analysis_pd["Average Purchase Price"] = top_spender_analysis_pd["Average Purchase Price"].map("${:.2f}".format)
top_spender_analysis_pd["Total Purchase Value"] = top_spender_analysis_pd["Total Purchase Value"].map("${:.2f}".format)

top_spender_analysis_pd

```

    C:\Users\santo\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:13: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      del sys.path[0]
    C:\Users\santo\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:14: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      
    




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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most Popular Items
item_df = game_df.groupby(["Item ID","Item Name"])
item_count = item_df["Price"].count()
item_total = item_df["Price"].sum()
item_price = item_df["Price"].unique()
item_analysis_pd = pd.DataFrame({"Purchase Count":item_count,"Purchase Price": item_price, "Total Purchase Value": item_total})
item_price_pd = item_analysis_pd["Purchase Price"].values.astype(float)
item_analysis_pd["Price"] = item_price_pd
new_item_analysis_pd = item_analysis_pd[["Purchase Count","Price","Total Purchase Value"]]
total_item_analysis_pd = new_item_analysis_pd.sort_values("Purchase Count",ascending = False)
top_item_analysis_pd = total_item_analysis_pd.head(5)

top_item_analysis_pd["Price"] = top_item_analysis_pd["Price"].map("${:.2f}".format)
top_item_analysis_pd["Total Purchase Value"] = top_item_analysis_pd["Total Purchase Value"].map("${:.2f}".format)

top_item_analysis_pd


```

    C:\Users\santo\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:13: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      del sys.path[0]
    C:\Users\santo\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:14: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      
    




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
      <th></th>
      <th>Purchase Count</th>
      <th>Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most Profitable Items
total_item_analysis_purc_pd = total_item_analysis_pd.sort_values("Total Purchase Value",ascending = False)
top_item_analysis_purc_pd = total_item_analysis_purc_pd.head(5)
top_item_analysis_purc_pd["Price"] = top_item_analysis_purc_pd["Price"].map("${:.2f}".format)
top_item_analysis_purc_pd["Total Purchase Value"] = top_item_analysis_purc_pd["Total Purchase Value"].map("${:.2f}".format)
top_item_analysis_purc_pd
```

    C:\Users\santo\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      after removing the cwd from sys.path.
    C:\Users\santo\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """
    




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
      <th></th>
      <th>Purchase Count</th>
      <th>Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>




```python


```
