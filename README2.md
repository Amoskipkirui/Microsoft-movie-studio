# Phase 1 Project Description

[image](dsc-phase-1-project-v2-4/Image/awesome.gif/)
## Project Overview

For this project, you will use exploratory data analysis to generate insights for a business stakeholder.

### Business Problem

Microsoft sees all the big companies creating original video content and they want to get in on the fun. They have decided to create a new movie studio, but they don’t know anything about creating movies. You are charged with exploring what types of films are currently doing the best at the box office. You must then translate those findings into actionable insights that the head of Microsoft's new movie studio can use to help decide what type of films to create.
In the folder `zippedData` are movie datasets from:

* [Box Office Mojo](https://www.boxofficemojo.com/)
* [IMDB](https://www.imdb.com/)
* [Rotten Tomatoes](https://www.rottentomatoes.com/)
* [TheMovieDB](https://www.themoviedb.org/)
* [The Numbers](https://www.the-numbers.com/)

### A. Business understanding
 1. Does a studio or production company matters much in terms of Box office and the general revenue of a film?
 2. Every film has a genre. How are different film genres contributing to a box office of a film?
 3. Films differ in terms of quality. Production budget of a film has a direct influence on the quality of the movie. I will be seeking to answer
    the question on whether the production budget affects the box office of a film. 
 4. Does the presence of stars from another field like sports, music or even from the film industry affect the box office of a film?
 5. There are top film producers. Do we have to use them in order to ensure our movies/ films travels up to the international level
    which will in turn increase the box office? Relationship between film producers and the box office of a movie.
 6. Does the rating of a movie affects the box office?
 7. How is the run-time of  film related to gross income of a film/movie?
 8. What are the top 5 movie genres that have high box office?
 9. How is the original language from which the movie/ film is drawn affects the popularity?
 10. What are the correlations between the given attributes of the datasets? Of interest is between the other attributes 
     to box office/ revenue generated

### We have to import the following libraries to aid in our code writing and visualizations.
```python
 import pandas as pd
 import numpy as np
 import seaborn as sns
 import sqlite3 
 import matplotlib.pyplot as plt
 %matplotlib inline
```

 ### We are interested with the top performing films in terms of gross income. We will therefore filter our data to the films generbom_df = pd.read_csv('bom.movie_gross.csv.gz') # load the csv file and assign it to a variable called bom_df
# filtering the dataset to only the domestic gross above $100000000
```python
bom_df = bom_df[bom_df['domestic_gross'] > 100000000] 
bom_dfating gross income from above $100,000,000 
```
  
### Remove all the characters from our values in the dataset.
 ### foreign_gross had characters in it and so to make it  a float we have to remove them using replace() method.
bom_df['foreign_gross'].replace('\W', '', regex=True, inplace= True)   
### Foreign gross is an object, we have to change it to a float now that we've removed characters from it.
### .astype() method is used to change a data type to a prefered one. Here we are changing foreign object to float from object
```python
bom_df.foreign_gross = bom_df.foreign_gross.astype(float)
``` 

### Checking if there are null values.
```python
bom_df.isna().sum()
```
 .isna() is used to check for null values. And sum() sums the null values.

### We want to know the studios that have been used most in production of films that have both domestic and foreign gross above $ 100,000,000. 
 * This will help us in identifying the qualities of those studios which makes them favorable for productions.
 #### count of the studios used in production of films with their gross above $100000000
```python
bom_df.studio.value_counts()

Histogram showing the frequency of the studios in the movie production
fig, ax = plt.subplots(figsize = (20,8))
ax.hist(bom_df.studio, bins = 80)
ax.set_xlabel('Names of studios')
ax.set_ylabel('Number of times the studios have been used.')
ax.set_title('Studio counts');
```


### Finding the relationship between studio and the box_office( sum of domestic gross and foreign gross)
 Code on creation of a new column called box office which is the sum of domestic gross and foreign gross
```python
bom_df['box_office'] =( bom_df.domestic_gross) + (bom_df.foreign_gross)
bom_df
```


### Bar graph showing the relationship between studios and box offfice.
```python
fig, ax = plt.subplots(figsize=(15,6))
plt.bar( x= bom_df.studio, height= bom_df.box_office)
plt.xlabel('Name of studios')
plt.ylabel('box office in millions')
plt.title('Relationship between Studios and total revenue from films')
plt.show()
```


### 2. For the below dataset, we are much interested in the top performing films in terms of box office. 
We will use a sample of 20 films in our analysis as this will give us full information on 
the type of films that Microsoft should adopt.

   * loading data and assigning it to rottent_df variable. 
```python
rottent_df = pd.read_csv('rotten_tomatoes_top_movies.csv', index_col = 0)
```
   * we extract the columns that are needed for our analysis
```python
rottent_df = rottent_df.loc[:,['title','year','critic_score','type','rating','genre','producer','box_office_(gross_usa)','runtime','crew']].head(20)
```
    * Arrange the rows in a descending order for easy extraction of top performing films.
rottent_df.sort_values(by='box_office_(gross_usa)', ascending= False)

### Cleaning the dataset
Check for missing values in the top 20 rows in all the columns.
```python
rottent_df.isna().sum()
```

* We will back fill the missing values in box_office_(gross_usa) column.
```python
rottent_df['box_office_(gross_usa)'].fillna(method = 'bfill', inplace= True)
rottent_df
```

* We have a missing value in rating column. We can drop the row with missing value since it will have a little impact on our dataset.
```python
rottent_df.dropna(axis = 0, inplace=True)

rottent_df.sort_values(by='box_office_(gross_usa)', ascending= False
```)

### Next, we look at the information of the dataset
```python
rottent_df.info()
```

```python
box_office_(gross_usa) is of object type instead of float. We then have to change it to float.
```
* replace('\w' ..) removes characters and replace them with empty string.
```python
rottent_df['box_office_(gross_usa)'] = rottent_df['box_office_(gross_usa)'].replace('\W', '', regex=True) 
```
* replace letter M with empty string.
```python
rottent_df['box_office_(gross_usa)'] = rottent_df['box_office_(gross_usa)'].replace('M', '', regex=True)
```
* changes the data type to float.
```python
rottent_df['box_office_(gross_usa)'] = rottent_df['box_office_(gross_usa)'].astype(float)
```


### Look at the runtime of films top performing films then visualize the data.
```python
rottent_df.runtime
```

### A bar graph showing the relationship between runtime and box office of a film. Does it affect the selling of the movie?

```python
fig, ax = plt.subplots(figsize = (20,8))
ax.bar(x = rottent_df['runtime'], height= rottent_df['box_office_(gross_usa)'])
ax.set_xlabel('runtime')
ax.set_ylabel('box office in millions')
ax.set_title('Relationship between runtime and box office(USA gross)');
```

* Look at the types of films and how they relate to box office of the top performing films.
rottent_df.type.value_counts()

### Histogram showing the leading type of movies among the top performing films.

```python
fig, ax = plt.subplots()
plt.hist(rottent_df.type)
ax.set_xlabel('Type of movie')
ax.set_ylabel('Count of the type of movies')
ax.set_title('Top films types.');

```
### Genres

### Horizontal bar graph showing the relationship between genre and the box office of a movie. Which genre is mostly used in the production of these films?
```python
fig, ax = plt.subplots(figsize=(20,8))
plt.barh(rottent_df['genre'], width= rottent_df['box_office_(gross_usa)'])
ax.set_title('How different genres perform in terms of box office')
ax.set_ylabel('Genres')
ax.set_xlabel('box office in millions');
```

* We also will have to look at the rating of the top films since it will determine a lot when settling on the type of film to recommend to Microsoft.
```python
fig, ax = plt.subplots(figsize=(20,8))
plt.barh(rottent_df.rating,width=rottent_df['box_office_(gross_usa)'])
ax.set_ylabel('rating')
ax.set_xlabel('box office in millions')
ax.set_title('How the rating of a film affect the box office');
```


* We then look at producers of these performing films. Who are the most sort after producers?
rottent_df.producer.value_counts()

### A horizontal bar graph of box office against producers. We seek to find the top producers in the production of the best performing films.

```python
fig, ax = plt.subplots(figsize = (20,8))
ax.barh( rottent_df.producer, width= rottent_df['box_office_(gross_usa)'])
ax.set_xlabel('box office in millions')
ax.set_ylabel('Names of the producers')
ax.set_title('Producers relationship with box office of films');
```

 ### 3. To discover the relationship between production budget and box office, we sourced for a dataset containing this information for our analysis.
```python
 budget_df = pd.read_csv('tn.movie_budgets.csv', index_col = 0)
budget_df.head(10)
```
**Cleaning the dataset.**
We first check for the information of this dataset using .info()

```python
budget_df.info()# gives us the information of the dataset
```
**We then check if there are duplicates in our dataset.**
```python
budget_df.duplicated().sum()# checking for duplicates and summing them up.
```
### Well, our dataset seems to have no duplicates. We proceed to examine the data types of the various column entries.
### Production budget, domestic gross, wordwide gross and box office should all be in float data type
 ```python
 budget_df['worldwide_gross'] = budget_df['worldwide_gross'].str.replace('[$,]','')# remocing of characters in the column values.

budget_df['worldwide_gross'] = budget_df['worldwide_gross'].astype(float)#  changing the dtype from object to float
 ```

 ```python
 budget_df['production_budget'] = budget_df['production_budget'].str.replace('[$,]','')

budget_df['production_budget'] = budget_df['production_budget'].astype(float)
budget_df
 ```

 ### We are interested with the top performing films and so we extract the data of those films that have a worldwide gross of above $ 100000000.

 ```python
 budget_df = budget_df[budget_df['worldwide_gross'] > 100000000]
budget_df.head(10)
 ```

 **Check on the relationship between production budget and worldwide gross**
  **Plotting a correlation matrix of production_budget and worldwide gross.**

  ```python
  correlation = budget_df.corr()
correlation. style. background_gradient (cmap = 'BrBG') 
  ```
  ### Heatmap using seaborn of production budget and wordwide gross to aid in visualization.
  ```python
  sns. heatmap (correlation, annot=True);
  ```