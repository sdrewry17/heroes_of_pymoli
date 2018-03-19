
# Heroes of Pymoli

* Observed Trend 1:  There are significantly more males playing the game than females
* Observed Trend 2:  Those that report their gender as "Other / non-disclosed" spend more per purchase on average than either those that report their gender as male or female.
* Observed Trend 3:  People 20-24 spend the most and have the most transactions; however, people 8-12 have the highest average spend


```python
# dependencies
import pandas as pd
import numpy as np
```


```python
# assigning path to JSON data
jsonpath = input("What is the name / path of the JSON file that you wish to analyze?")
```


```python
#creating a data frame from purchase data
purchase_data_df = pd.read_json(jsonpath)
purchase_data_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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



* Total Number of Players


```python
#find total number of players
tot_players = purchase_data_df['SN'].nunique()
tot_players
```




    573



## Purchasing Analysis (Total)
* Number of Unique Items
* Average Purchase Price
* Total Number of Purchases
* Total Revenue


```python
# Find summary statistics about the purchases

#total unique items
tot_items = purchase_data_df['Item ID'].nunique()

#average price of items
avg_price = purchase_data_df['Price'].mean()

#total purchases
tot_purchases = len(purchase_data_df)

#total revenue
tot_revenue = purchase_data_df['Price'].sum()
```


```python
# Create a dataframe collecting the purchase analysis data
purchase_analysis_df = pd.DataFrame(data = [tot_items, avg_price, tot_purchases, tot_revenue], index = ['Total Items', 'Average Price', 'Total Purchases', 'Total Revenue'], columns = ["Data"])
purchase_analysis_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Total Items</th>
      <td>183.000000</td>
    </tr>
    <tr>
      <th>Average Price</th>
      <td>2.931192</td>
    </tr>
    <tr>
      <th>Total Purchases</th>
      <td>780.000000</td>
    </tr>
    <tr>
      <th>Total Revenue</th>
      <td>2286.330000</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics
* Percentage and Count of Male Players
* Percentage and Count of Female Players
* Percentage and Count of Other / Non-Disclosed


```python
# Create a DataFrame with a purchase count by gender
gender_df = purchase_data_df[['Item ID', "Gender"]].groupby(by = "Gender").count()
gender_df.rename(columns = {"Item ID": "Purchase Count"}, inplace = True)

# Create a separate dataframe to get the unique counts of gender
gender_unique_count = purchase_data_df.drop_duplicates(['SN'], keep = "last")
gender_df['Unique Count'] = gender_unique_count[['SN', "Gender"]].groupby(by = "Gender").count()
gender_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Unique Count</th>
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
      <td>136</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Find statistics by gender
males_count = gender_df.loc['Male'][0]
males_percent = males_count / tot_purchases
females_count = gender_df.loc['Female'][0]
females_percent = females_count / tot_purchases
other_count = gender_df.loc['Other / Non-Disclosed'][0]
other_percent = other_count / tot_purchases
```


```python
# create a dictionary into which the statistics can later be placed into a dataframe
my_dict = {"Purchase Count": {"males": males_count, "females": females_count, "others": other_count}, 
           "Percent": {"females": females_percent, "males": males_percent, "others": other_count}}
```


```python
# create a dataframe from the dictionary
gender_demographics_df = pd.DataFrame(data = my_dict)
gender_demographics_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percent</th>
      <th>Purchase Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>females</th>
      <td>0.174359</td>
      <td>136</td>
    </tr>
    <tr>
      <th>males</th>
      <td>0.811538</td>
      <td>633</td>
    </tr>
    <tr>
      <th>others</th>
      <td>11.000000</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



## The below each broken by gender
  * Purchase Count
  * Average Purchase Price
  * Total Purchase Value
  * Normalized Totals


```python
# create a DataFrame on the index of the gender data
gender_metrics = pd.DataFrame(index = gender_df.index)
```


```python
# create a new column in the DataFrame and assign the counts by age.
gender_metrics['Purchase Count'] = gender_df['Purchase Count']
```


```python
# create a new column in the DataFrame and put in average price data by gender
gender_metrics['Average Purchase Price'] = purchase_data_df.groupby(by = "Gender").mean()['Price']
```


```python
# create a new column in the DataFrame and put in the total price by gender
gender_metrics['Total Purchase Value'] = purchase_data_df.groupby(by = "Gender").sum()['Price']
```


```python
# create normalized average
gender_metrics['Normalized Average'] = gender_metrics['Total Purchase Value'] / gender_df["Unique Count"]
gender_metrics
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Average</th>
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
      <td>2.815515</td>
      <td>382.91</td>
      <td>3.829100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>2.950521</td>
      <td>1867.68</td>
      <td>4.016516</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>3.249091</td>
      <td>35.74</td>
      <td>4.467500</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics

* The below each broken into bins of 4 years (i.e. &lt;10, 10-14, 15-19, etc.) 
* Purchase Count
* Average Purchase Price
* Total Purchase Value


```python
# create bins based on the shape of the data for age
bins = np.arange(0, purchase_data_df['Age'].max(), 4)
labels = ["0-4", "4-8", "8-12", "12-16", "16-20", "20-24", "28-32", "32-36", "36-40", "40-44", "44+"]
```


```python
# insert the bins into the dataframe
purchase_data_df['Age Group'] = pd.cut(purchase_data_df['Age'], bins = bins, labels = labels)
```


```python
# group the data by the bins created by the ages in the data set & create a new dataframe 
#first containing the count of purchases by age group
age_demo_df = purchase_data_df[['Age Group', 'Price']].groupby('Age Group').count()
age_demo_df.rename(columns = {"Price": "Purchase Count"}, inplace = True)
age_demo_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0-4</th>
      <td>0</td>
    </tr>
    <tr>
      <th>4-8</th>
      <td>22</td>
    </tr>
    <tr>
      <th>8-12</th>
      <td>24</td>
    </tr>
    <tr>
      <th>12-16</th>
      <td>87</td>
    </tr>
    <tr>
      <th>16-20</th>
      <td>161</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a new column with the total purchase by age group
age_demo_df['Total Purchase Value'] = purchase_data_df[['Age Group', 'Price']].groupby('Age Group').sum()
```


```python
# Create a separate dataframe to get the unique counts of age
age_unique_count = purchase_data_df.drop_duplicates(['SN'], keep = "last")
age_demo_df['Unique Count'] = age_unique_count[['SN', "Age Group"]].groupby(by = "Age Group").count()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
      <th>Unique Count</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0-4</th>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4-8</th>
      <td>22</td>
      <td>61.34</td>
      <td>2.788182</td>
      <td>13</td>
    </tr>
    <tr>
      <th>8-12</th>
      <td>24</td>
      <td>81.25</td>
      <td>3.385417</td>
      <td>18</td>
    </tr>
    <tr>
      <th>12-16</th>
      <td>87</td>
      <td>238.89</td>
      <td>2.745862</td>
      <td>64</td>
    </tr>
    <tr>
      <th>16-20</th>
      <td>161</td>
      <td>468.03</td>
      <td>2.907019</td>
      <td>120</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>238</td>
      <td>696.09</td>
      <td>2.924748</td>
      <td>186</td>
    </tr>
    <tr>
      <th>28-32</th>
      <td>104</td>
      <td>309.37</td>
      <td>2.974712</td>
      <td>75</td>
    </tr>
    <tr>
      <th>32-36</th>
      <td>66</td>
      <td>202.09</td>
      <td>3.061970</td>
      <td>44</td>
    </tr>
    <tr>
      <th>36-40</th>
      <td>38</td>
      <td>113.28</td>
      <td>2.981053</td>
      <td>28</td>
    </tr>
    <tr>
      <th>40-44</th>
      <td>37</td>
      <td>107.35</td>
      <td>2.901351</td>
      <td>22</td>
    </tr>
    <tr>
      <th>44+</th>
      <td>2</td>
      <td>5.92</td>
      <td>2.960000</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a new column with the average purchase price by age group
age_demo_df['Average Purchase Price'] = age_demo_df['Total Purchase Value'] / age_demo_df['Purchase Count']
```


```python
age_demo_df['Normalized Average'] = age_demo_df['Total Purchase Value'] / age_demo_df['Unique Count']
age_demo_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
      <th>Unique Count</th>
      <th>Normalized Average</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0-4</th>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4-8</th>
      <td>22</td>
      <td>61.34</td>
      <td>2.788182</td>
      <td>13</td>
      <td>4.718462</td>
    </tr>
    <tr>
      <th>8-12</th>
      <td>24</td>
      <td>81.25</td>
      <td>3.385417</td>
      <td>18</td>
      <td>4.513889</td>
    </tr>
    <tr>
      <th>12-16</th>
      <td>87</td>
      <td>238.89</td>
      <td>2.745862</td>
      <td>64</td>
      <td>3.732656</td>
    </tr>
    <tr>
      <th>16-20</th>
      <td>161</td>
      <td>468.03</td>
      <td>2.907019</td>
      <td>120</td>
      <td>3.900250</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>238</td>
      <td>696.09</td>
      <td>2.924748</td>
      <td>186</td>
      <td>3.742419</td>
    </tr>
    <tr>
      <th>28-32</th>
      <td>104</td>
      <td>309.37</td>
      <td>2.974712</td>
      <td>75</td>
      <td>4.124933</td>
    </tr>
    <tr>
      <th>32-36</th>
      <td>66</td>
      <td>202.09</td>
      <td>3.061970</td>
      <td>44</td>
      <td>4.592955</td>
    </tr>
    <tr>
      <th>36-40</th>
      <td>38</td>
      <td>113.28</td>
      <td>2.981053</td>
      <td>28</td>
      <td>4.045714</td>
    </tr>
    <tr>
      <th>40-44</th>
      <td>37</td>
      <td>107.35</td>
      <td>2.901351</td>
      <td>22</td>
      <td>4.879545</td>
    </tr>
    <tr>
      <th>44+</th>
      <td>2</td>
      <td>5.92</td>
      <td>2.960000</td>
      <td>2</td>
      <td>2.960000</td>
    </tr>
  </tbody>
</table>
</div>



**Top Spenders**

* Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
  * SN
  * Purchase Count
  * Average Purchase Price
  * Total Purchase Value


```python
#Create a dataframe with the purchase count by the players
players_df = purchase_data_df[['SN', "Price"]].groupby('SN').count()
players_df.rename(columns = {"Price": "Purchase Count"}, inplace = True)
players_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Adairialis76</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Aduephos78</th>
      <td>3</td>
    </tr>
    <tr>
      <th>Aeduera68</th>
      <td>3</td>
    </tr>
    <tr>
      <th>Aela49</th>
      <td>1</td>
    </tr>
    <tr>
      <th>Aela59</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#  Add in teh total purchase value for each player
players_df['Total Purchase Value'] = purchase_data_df[['SN', 'Price']].groupby('SN').sum()
players_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Adairialis76</th>
      <td>1</td>
      <td>2.46</td>
    </tr>
    <tr>
      <th>Aduephos78</th>
      <td>3</td>
      <td>6.70</td>
    </tr>
    <tr>
      <th>Aeduera68</th>
      <td>3</td>
      <td>5.80</td>
    </tr>
    <tr>
      <th>Aela49</th>
      <td>1</td>
      <td>2.46</td>
    </tr>
    <tr>
      <th>Aela59</th>
      <td>1</td>
      <td>1.27</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Find the average purchase price
players_df['Average Purchase Price'] = players_df['Total Purchase Value'] / players_df['Purchase Count']
```


```python
#Find the top five players based on total purchase value
players_df.sort_values(by = "Total Purchase Value", ascending = False, inplace = True)
top_5_players = players_df.iloc[0:5 , :]
top_5_players
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
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
      <td>17.06</td>
      <td>3.412000</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>13.56</td>
      <td>3.390000</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>12.74</td>
      <td>3.185000</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>12.73</td>
      <td>4.243333</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>11.58</td>
      <td>3.860000</td>
    </tr>
  </tbody>
</table>
</div>



**Most Popular Items**

* Identify the 5 most popular items by purchase count, then list (in a table):
  * Item ID
  * Item Name
  * Purchase Count
  * Item Price
  * Total Purchase Value


```python
#create a dataframe that contains the item information and initially creates a purchase count for all items
items_df = purchase_data_df[['Item ID', "Item Name", "Price"]].groupby(['Item ID', 'Item Name']).count()
items_df.rename(columns = {"Price": "Purchase Count"}, inplace = True)
items_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <th>Splinter</th>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <th>Crucifer</th>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <th>Verdict</th>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <th>Phantomlight</th>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <th>Bloodlord's Fetish</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Add the total purchase value for each item to the dataframe
items_df["Purchase Value"] = purchase_data_df[['Item ID', "Item Name", "Price"]].groupby(['Item ID', 'Item Name']).sum()
```


```python
# Find the average purchase value of the items and assign to a column into the dataframe
items_df["Average Purchase Value"] = items_df['Purchase Value'] / items_df['Purchase Count']
```


```python
#Sort the data frame according to the Purchase Value column and find the most popular items by value
items_df.sort_values(by = "Purchase Value", inplace = True, ascending = False)
most_popular_items_by_value = items_df.iloc[ :5, :]
most_popular_items_by_value
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Purchase Value</th>
      <th>Average Purchase Value</th>
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
      <td>37.26</td>
      <td>4.14</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>29.75</td>
      <td>4.25</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>29.70</td>
      <td>4.95</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>29.22</td>
      <td>4.87</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>28.88</td>
      <td>3.61</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Sort the data frame according to the Purchase Count column and find the most popular items by value
items_df.sort_values(by = "Purchase Count", inplace = True, ascending = False)
most_popular_items_by_count = items_df.iloc[ :5, :]
most_popular_items_by_count
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Purchase Value</th>
      <th>Average Purchase Value</th>
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
      <td>25.85</td>
      <td>2.35</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>24.53</td>
      <td>2.23</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>37.26</td>
      <td>4.14</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>13.41</td>
      <td>1.49</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>18.63</td>
      <td>2.07</td>
    </tr>
  </tbody>
</table>
</div>


