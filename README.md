# ANALYSIS OF THE MOVIE PRODUCTION INDUSTRY

## 1. INTRODUCTION
This analysis helps movie producers understand the market better by looking at past films to see which types of movies perform well, how much money is usually spent on them, and how that affects their success, so they can plan their budgets wisely and make better decisions for future projects.

## 2. BUSINESS UNDERSTANDING
In today’s highly competitive film industry, production companies face increasing pressure to make informed decisions that minimize financial risk while maximizing returns. Before investing in our new project, it’s important to understand how different movie features — such as genre,production budget, and release timing influence audience reception.

The goal of this analysis is to help the business:
- Identify which genres tend to perfom well in terms of rating
- Determine the trends on genre popularity over time
- Calculate trends on production budget over the years
- Calculate trends of aundience engagement over the years
- Compare domestic and worldwide gross over the years

## 2.1. Objectives
1. To identify the top 10 most produced genres
2. To identify the top rated movie genres
3. To calculate trends of production budget over the years
4. Determining how audience engagement to a movie changes over the years
5. Comparing average domestic and worldwide gross of movies and the trend with time

## 3. DATA UNDERSTANDING
For this analysis, we used two Datasets, one from IMDB, which contains movie title, average ratings and start year. The other dataset was from The numbers, wich contains production budget and both domestic and worldwide gross that movies made.

## 4. DATA PREPARATION



```python
# Import all the relevant libraries
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
import sqlite3
# Import zipfile for unzipping the zipped file
import zipfile
import os
```


```python
# Create a temporary folder, import IMDB then create a comnnection
zipfile.ZipFile("im.zip", 'r').extractall("tempo_folder")
conn = sqlite3.connect("tempo_folder/im.db")
```


```python
# Query the DB using sqlite3
IMDB = pd.read_sql("""
SELECT *
FROM movie_basics
JOIN movie_ratings
USING (movie_id) ;"""
                 , conn)
```


```python
#Check the first 5 rows
IMDB.head()
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
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0063540</td>
      <td>Sunghursh</td>
      <td>Sunghursh</td>
      <td>2013</td>
      <td>175.0</td>
      <td>Action,Crime,Drama</td>
      <td>7.0</td>
      <td>77</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0066787</td>
      <td>One Day Before the Rainy Season</td>
      <td>Ashad Ka Ek Din</td>
      <td>2019</td>
      <td>114.0</td>
      <td>Biography,Drama</td>
      <td>7.2</td>
      <td>43</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0069049</td>
      <td>The Other Side of the Wind</td>
      <td>The Other Side of the Wind</td>
      <td>2018</td>
      <td>122.0</td>
      <td>Drama</td>
      <td>6.9</td>
      <td>4517</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0069204</td>
      <td>Sabse Bada Sukh</td>
      <td>Sabse Bada Sukh</td>
      <td>2018</td>
      <td>NaN</td>
      <td>Comedy,Drama</td>
      <td>6.1</td>
      <td>13</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0100275</td>
      <td>The Wandering Soap Opera</td>
      <td>La Telenovela Errante</td>
      <td>2017</td>
      <td>80.0</td>
      <td>Comedy,Drama,Fantasy</td>
      <td>6.5</td>
      <td>119</td>
    </tr>
  </tbody>
</table>
</div>




```python
IMDB.shape
```




    (73856, 8)



##### The IMDB Dataframe has 8 columns and 73,856 rows


```python
# import the numbers dataframe
the_numbers = pd.read_csv("tn.movie_budgets.csv")
the_numbers.head()
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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>$425,000,000</td>
      <td>$760,507,625</td>
      <td>$2,776,345,279</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>$410,600,000</td>
      <td>$241,063,875</td>
      <td>$1,045,663,875</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>$350,000,000</td>
      <td>$42,762,350</td>
      <td>$149,762,350</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>$330,600,000</td>
      <td>$459,005,868</td>
      <td>$1,403,013,963</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>$317,000,000</td>
      <td>$620,181,382</td>
      <td>$1,316,721,747</td>
    </tr>
  </tbody>
</table>
</div>




```python
the_numbers.shape
```




    (5782, 6)



##### The numbers dataframe above has 6 columns, and 5,782 rows

### 4.1. Data Cleaning

We begin by checking the number of missing values in both the datasets


```python
round((IMDB.isnull().sum()/ len(IMDB))*100,2)
```




    movie_id            0.00
    primary_title       0.00
    original_title      0.00
    start_year          0.00
    runtime_minutes    10.32
    genres              1.09
    averagerating       0.00
    numvotes            0.00
    dtype: float64



##### From the above output, 10.32% of data in the `runtime_minutes` column is missing, and 1.09% of data from the genres column is missing. These percentages are low and we can drop the missing values


```python
IMDB = IMDB.dropna()
```

##### If we repeat the code above to check the % of missing values, we should get 0 in all columns


```python
round((IMDB.isnull().sum()/ len(IMDB))*100,2)
```




    movie_id           0.0
    primary_title      0.0
    original_title     0.0
    start_year         0.0
    runtime_minutes    0.0
    genres             0.0
    averagerating      0.0
    numvotes           0.0
    dtype: float64



#### The Numbers Dataset cleaning


```python
round((the_numbers.isnull().sum()/ len(the_numbers))*100,2)
```




    id                   0.0
    release_date         0.0
    movie                0.0
    production_budget    0.0
    domestic_gross       0.0
    worldwide_gross      0.0
    dtype: float64



##### If we look at the aspect of missing values, our data might look clean. But we need to check if each column has the appropriate data types.


```python
the_numbers.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5782 entries, 0 to 5781
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype 
    ---  ------             --------------  ----- 
     0   id                 5782 non-null   int64 
     1   release_date       5782 non-null   object
     2   movie              5782 non-null   object
     3   production_budget  5782 non-null   object
     4   domestic_gross     5782 non-null   object
     5   worldwide_gross    5782 non-null   object
    dtypes: int64(1), object(5)
    memory usage: 271.2+ KB
    

##### Some columns are not assigned the appropriate data types. 


```python
# Convert to datetime pandas 
the_numbers['release_date'] = pd.to_datetime(the_numbers['release_date'])
```


```python
#Create a list of column names from the dataset
columns = ['domestic_gross', 'production_budget', 'worldwide_gross']

# Remove $ and commas, then convert to float
for col in columns:
    the_numbers[col] = the_numbers[col].replace(r'[\$,]', '', regex=True).astype(float)
```


```python
the_numbers.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5782 entries, 0 to 5781
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype         
    ---  ------             --------------  -----         
     0   id                 5782 non-null   int64         
     1   release_date       5782 non-null   datetime64[ns]
     2   movie              5782 non-null   object        
     3   production_budget  5782 non-null   float64       
     4   domestic_gross     5782 non-null   float64       
     5   worldwide_gross    5782 non-null   float64       
    dtypes: datetime64[ns](1), float64(3), int64(1), object(1)
    memory usage: 271.2+ KB
    

##### Our datasets are now clean and ready to use

## 5. EXPLORATORY DATA ANALYSIS

## 5.1 Genres and Movie production


```python
# Extract each individual genre from the genres column
IMDB['genres'] = IMDB['genres'].str.split(',')
IMDB= IMDB.explode('genres')
IMDB['genres'] = IMDB['genres'].str.strip()
```


```python
# Retrieve the top 10 most occuring genres
genres = IMDB['genres'].value_counts().sort_values(ascending=False).nlargest(10)
genres.head()
```




    genres
    Drama          28394
    Documentary    16423
    Comedy         15514
    Thriller        7583
    Horror          6917
    Name: count, dtype: int64




```python
# Plot them below
plt.figure(figsize=(10, 6))
genres.plot(kind='bar', color='blue', edgecolor='black')
plt.title('Top 10 Movie Genres')
plt.xlabel('Genres')
plt.ylabel('Number of Movies')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()

```


    
![png](y_files/y_35_0.png)
    


##### As from above, the most produced genre of movies is Drama, with  28,394 productions, followed by Documantaries with 16,423 productions. Comedy, Thriller and Horror also top the list. On the other side of the list, Short films, Adult films and Game show are the least produced categories of movies, with 1, 2, and 2 productions respectively

## 5.2. Genres on average rating


```python
# Group by genre and get the average rating
genre_ratings = IMDB.groupby('genres')['averagerating'].mean().sort_values(ascending=False)
#Get the first 10
top_genres = genre_ratings.head(10)
# Visualize
plt.figure(figsize=(10, 6))
top_genres.plot(kind='bar', color='cyan')
plt.title('Top Rated Movie Genres')
plt.xlabel('Genre')
plt.ylabel('Average Rating')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

```


    
![png](y_files/y_38_0.png)
    


##### As from the above visualization, we might be tricked to say that short films are the most rated genres. However, just above we saw that this genre is among the least produced, hence the reason for the high rating average. so in this case, the most rated genre of movies might be documentaries with a mean average rating of above 7 

## 5.3 Average rating per genre per year


```python
# Group our data by genre and year, then calculate the mean rating each year
genre_yearly = IMDB.groupby(['start_year', 'genres'])['averagerating'].mean().reset_index()
#Create a filter metric
top_genres = (
    IMDB.groupby('genres')['averagerating'].mean()
    .sort_values(ascending=False)
    .head(10) 
    .index
)
```


```python
# create a variable filtered and use the metric created above for filtering
filtered = genre_yearly[genre_yearly['genres'].isin(top_genres)]
```


```python
# Create a pivot for visualization
pivot = filtered.pivot(index='start_year', columns='genres', values='averagerating')
```


```python
# Visualize
pivot.plot(figsize=(15, 8), marker='o')
plt.title('Top Rated Genres Over the Years')
plt.xlabel('Year')
plt.ylabel('Average Rating')
plt.legend(title='Genre', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()
plt.show()
```


    
![png](y_files/y_44_0.png)
    


##### The ratings average at 7 in most of these genre. However there is an observable distinct behaviour wih `Reality-TV` genre. as we see significant shifts from an average rating of 5.5 in 2012 to an average rating of 8.8 in 2014 to less than 4 in 2018

## 5.4. Calculating production budget over the years


```python
# create a column year for easy grouping of data
the_numbers['year'] = the_numbers['release_date'].dt.year
# Group the data by years
avg_budget_by_year = the_numbers.groupby('year', as_index=False)['production_budget'].mean()
```


```python
# Import ticker for marking our data
import matplotlib.ticker as ticker
# Plot the visualization
plt.figure(figsize=(10, 6))
plt.plot(avg_budget_by_year['year'], avg_budget_by_year['production_budget'], label='Average Production Budget', marker='o', color='blue')
plt.gca().yaxis.set_major_formatter(ticker.ScalarFormatter(useMathText=True))
plt.ticklabel_format(style='plain', axis='y')
plt.xlabel('Year')
plt.ylabel('Average Production Budget')
plt.title('Average Production Budget Over the Years')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```


    
![png](y_files/y_48_0.png)
    


##### The production budget increases gradualy over the years

## 5.5. Audience engagement with time


```python
#Group by years and find the mean of number of votes
yearly_votes = IMDB.groupby('start_year')['numvotes'].mean().reset_index()
yearly_votes = IMDB.groupby('start_year', observed=True)['numvotes'].sum().reset_index()
# Begin to plot. we will be using seaborn and matplotlib
plt.figure(figsize=(12, 6))
sns.barplot(data=yearly_votes, x='start_year', y='numvotes', palette='inferno')
plt.title('Audience Engagement by Year')
plt.ylabel('Average Number of Votes')
plt.xlabel('Release Year')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

    C:\Users\USER\AppData\Local\Temp\ipykernel_28316\1001291074.py:6: FutureWarning: 
    
    Passing `palette` without assigning `hue` is deprecated and will be removed in v0.14.0. Assign the `x` variable to `hue` and set `legend=False` for the same effect.
    
      sns.barplot(data=yearly_votes, x='start_year', y='numvotes', palette='inferno')
    


    
![png](y_files/y_51_1.png)
    


##### Average Yearly audience engagement is decreasing with time

## 5.6. Domestic gross and worldwide gross


```python
# Group the gross by year and calculate the mean for each grouped data
gross_by_year = the_numbers.groupby('year')[['domestic_gross', 'worldwide_gross']].mean().reset_index()
```


```python
# Plot the visualization
plt.figure(figsize=(10, 6))
plt.plot(gross_by_year['year'], gross_by_year['domestic_gross'], label='Mean Domestic Gross', marker='o')
plt.plot(gross_by_year['year'], gross_by_year['worldwide_gross'], label='Mean Worldwide Gross', marker='o')
plt.gca().yaxis.set_major_formatter(ticker.ScalarFormatter(useMathText=True))
plt.ticklabel_format(style='plain', axis='y')
plt.xlabel('Year')
plt.ylabel('Mean Gross Revenue')
plt.title('Average Domestic vs Worldwide Gross Over the Years')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```


    
![png](y_files/y_55_0.png)
    


##### Both domestic and worlwide gross have had a gradual incrase from the early 1900s to the 2010's where there is a visible gradual drop in anual gross both domestic and worldwide

## 6. Recommendations
1. Concider producing more documentaries and game-show movies as they tend to be more accepted by the audience
2. Avoid producing reality-Tv shows as they tend to be very unpredictable
3. The production budget tends to increase with time, possibly because of implementation of more technology. investing highly on movies might secure a higher chance of success
4. Concider producing films for international audience as worldwide gross is high

## 7. Conclussion
This analysis provides valuable insights into the key factors that influence a movie's success, such as genre, production budget, and audience ratings. We found that certain genres consistently receive higher ratings, while others perform better financially. There is also a clear link between budgeting decisions and gross income.

By understanding these patterns, movie producers can make more informed decisions about where to invest, what types of films to focus on, and how to better position their projects in a competitive market. These findings can help guide future production strategies, reduce financial risk, and increase the chances of creating successful and well-received films.


