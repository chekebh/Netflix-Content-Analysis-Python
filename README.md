# Netflix Content Analysis - Python
![](https://github.com/chekebh/Netflix-Content-Analysis-Python/blob/main/intro_image.jpg)
# Introduction
The aim of this project is to analyse Netflix's content library using the following [dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows) and provide the client with a better understanding of the media on their platform. This project places more emphasis on data cleaning as the dataset has a lot of missing values and the cleaned data will be used to provide visual representations that enhance the client's understanding of their platform's content. This is an overview of the project and relevant files are attached for your viewing.

# Relevant tools/libraries
Python - pandas, numpy, seaborn and matplotlib

# Problem
1) What's the distribution of movies and tv shows?
2) Which rating is the most frequently assigned to our content?
3) How are movies and TV shows distributed among the top 10 countries?
4) In which year did we release the highest volume of content?

# Data Analysis
## Data Exploration
In this section, we aim to get an understanding of the dataset by checking the data for any incorrect datatypes, null values and duplicates.

Upon observation, the dataset doesn't have the right format for the **date_added** column.

Now,  we'll check for null values and the following code will give a heatmap to help visualise any potential missing values.

```python
sns.heatmap(df.isnull(), cmap = 'viridis')
```
![](https://github.com/chekebh/Netflix-Content-Analysis-Python/blob/main/heatmap.png)

We can see that the **director**, **cast** and **country** columns have a significant amount of missing data.

Now we check for how many missing values each columns have.

```python
df.isnull().sum()
```

|column|count|
|:---|---|
|director |2634|
|cast|825|
|country|825|
|date_added|10|
|rating|4|
|duration|3|

We now check for any duplicate rows.
```python
df.duplicated().sum()
```
This cell returned 0 hence we just need to worry about the missing data.

## Data Cleaning
First we will reformat the **date_added** column.

```python
df['date_added'] = pd.to_datetime(df['date_added'])
```

During the data exploration, we found that the **date_added**, **rating** and **duration** columns had a minimal amount of missing data hence we'll remove the rows with these missing values.

```python
df.dropna(subset = ['date_added','rating','duration'],inplace = True)
```

Given the substantial amount of missing data, our approach will be to replace these missing values with 'Not Given' rather than deleting them.

```python
df['director'].replace(np.nan, 'Not Given', inplace = True)
df['cast'].replace(np.nan , 'Not Given', inplace = True)
df['country'].replace(np.nan, 'Not Given', inplace = True)
```

# Data Visualisation

Now that we have the cleaned data, we will generate visualisations to provide the client with a more comprehensive view of their content distribution. 

The following plot will give an idea of the client's content distribution on its platform.

```python
sns.countplot(x = 'type', data = df)
plt.title('No. of Movies vs TV Shows')
plt.show()
```

![](https://github.com/chekebh/Netflix-Content-Analysis-Python/blob/main/movies_vs_tv.png)

Then to find the most frequent rating the following will be used.

```python
sns.countplot(x = 'rating', data = df)
plt.title('Ratings of Content')
plt.xticks(rotation = 45)
plt.show()
```

![](https://github.com/chekebh/Netflix-Content-Analysis-Python/blob/main/rating.png)

From this we can conclude that **TV-MA** is the most frequent rating.

The distribution of content amongst the top 10 countries will be explored with the following plot.

```python
filt = df['country'] != 'Not Given' #Just to avoid the Not Given rows
top_10 = df[filt]['country'].value_counts()[:10].index #The index here are countries
countries = df.loc[df['country'].isin(top_10)]
result = countries.groupby(['type','country']).size().reset_index(name = 'count')
stacked_df = result.pivot_table(index='country', columns='type', values='count').sort_values(by = 'Movie', ascending = False) #To separate the type column into TV Show and Movie columns

stacked_df.plot(kind = 'bar', stacked = 'True')
plt.xlabel('Country')
plt.ylabel('Count')
plt.title('Distribution of Content Types by Top 10 Countries')
plt.show()
```
![](https://github.com/chekebh/Netflix-Content-Analysis-Python/blob/main/top10.png)

The following plot will show the annual content release.

```python
movie_count = df[df['type'] == 'Movie']['date_added'].dt.year.value_counts().sort_index() # Extracts the year and counts the frequency
tv_count = df[df['type'] == 'TV Show']['date_added'].dt.year.value_counts().sort_index()

plt.plot(movie_count.index, movie_count.values, label='Movie')
plt.plot(tv_count.index, tv_count.values, label='TV Show')
plt.xlabel('Release Year')
plt.ylabel('Number of Movies/Shows')
plt.title('Number of Movies/Shows Released per Year')
plt.legend()
plt.show()
```

![](https://github.com/chekebh/Netflix-Content-Analysis-Python/blob/main/content_release.png)

We can see that **2019** was the year with the most amount of content added.

# Conclusion

The essence of this project was to analyse Netflix's content, making use of a dataset riddled with missing values. My initial step was dedicated to data cleaning, aiming to rectify these gaps in the dataset. Through this experience, I acquired valuable skills in filling missing data and began to appreciate the pivotal role of data cleaning in the data analysis process.
