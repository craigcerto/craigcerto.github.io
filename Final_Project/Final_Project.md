<h2><center>Brazilian Birdwatching and Brazil's Amazon Deforestation</center></h2>

<h3><center>Craig Certo</center></h3>

<img src = "Amazon_Rainforest_birds.jpg">

### Introduction

#### The Amazon Rainforest

The Amazon Rainforest is the world's largest rainforest and river basin on the planet. The Amazon spans 670 million hectares, stores 90-140 billion metric tons of carbon, and supports 34 million people. It has been estimated that the Amazon contains 10% of known species on Earth, over 1300 of which species are birds. The birds in the Amazon account for one third of all bird species in the world. Since birds primarily reside in the treetops, they are highly threatened by deforestation. Birds may discover that the rainforest they flew to last year is seriously damaged or no longer exists. 

Learn more at: https://www.britannica.com/place/Amazon-Rainforest


#### Deforestation in the Amazon

Unfortunately, the Amazon is facing a threat due to unsustainable economic development and over 20% of the Amazon biome has been lost. The Amazon is the biggest deforestation front in the world and the WWF estimates that 27% of the Amazon will be without trees by 2030 if the current rate of deforestation continues. 

If you would like to learn more about deforestation in the Amazon, please pursue information on the WWF webpage: (https://wwf.panda.org/discover/our_focus/forests_practice/deforestation_fronts2/deforestation_in_the_amazon/)

#### Bird Watchers in Brazil

As the Brazilian Amazon hosts an incredible variety of bird species, there are thousands of people who have a passion for these birds and record their observations to share them with the bird watching community. Wikiaves.com is a website and forum for bird watchers in Brazil to post their pictures and observations of birds to have them identified and seen by others. Wikiaves has become a large repository for bird watchers and currently has 36,549 observers, 3,420,606 records, and has identified 1891 species. 

Please visit their website to see fascinating recent images and descriptions of unique birds: (https://www.wikiaves.com).


#### Data Science to Recognize Trends

In this tutorial, I will be utilizing the Data Science lifecycle in an effort to determine if there is an association between the deforestation in the Brazilian Amazon Rainforest and bird watchers in Brazil. Using the activity and records from the Wikaves repository, as well as information about yearly deforestation in the Brazilian Amazon, I will utilize python's useful libraries and data science techniques to discover trends in the data, present interesting patterns, and possibly discover correlations between these two events. In the process, I will go through each step of the data science lifecycle to reach my final conclusions.

### Data Collection

The first step in the Data Science lifecycle is data collection. This is where we will take our data from external sources and import it into our project, either by scraping it from a website or by downloading the dataset onto our personal machine.

For the Wikiaves repository, I am using a Kaggle dataset named "Brazilian Bird Observation Metadata from Wikiaves" which is a dataset scraped from Wikiaves kindly made available by Danilo Lessa Bernardineli. This dataset contains about 3 million observations and 17 attributes, of which I will be using only a few. It is available for download here: (https://www.kaggle.com/danlessa/brazilian-bird-observation-metadata-from-wikiaves)

Data regarding Amazon deforestation will be scraped from Mongabay.com, a webpage dedicated to rainforests and useful information for the public regarding the Amazon. The data is updated monthly, contains and is supplied by Rhett A. Butler. To learn more about deforestation figures I will be using in this tutorial, the data is available here: (https://rainforests.mongabay.com/amazon/deforestation_calculations.html) 

#### Scraping Data

I first must download and import the HTMLTableParser, which will help me in scraping the data from Mongabay.com


```python
pip install html-table-parser-python3
```

    Requirement already satisfied: html-table-parser-python3 in /opt/conda/lib/python3.8/site-packages (0.1.5)
    Note: you may need to restart the kernel to use updated packages.



```python
import urllib.request
from html_table_parser import HTMLTableParser
import pandas as pd
```



Using the HTMLTable Parser, I read the contents of the webpage and import it into a Pandas Dataframe called "forest". I print the first 5 rows of the table to better understand the columns and attributes for each observation.


```python
# Data Collection
def url_get_contents(url): 
  
    # Send a request to the webpage
    req = urllib.request.Request(url=url,  headers={'User-Agent': 'Mozilla/5.0'}) 
    web = urllib.request.urlopen(req) 
  
    # Read the contents of the webpage
    return web.read() 
  
# Pass our URL into the url_get_contents function 
page = url_get_contents('https://rainforests.mongabay.com/amazon/deforestation_calculations.html').decode('utf-8') 
  
# Defining the HTMLTableParser object 
html = HTMLTableParser() 
  
# Feeding the page contents into the HTMLTableParser
html.feed(page) 

# Convert the table contents into a DataFrame
forest = pd.DataFrame(html.tables[0])
forest.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td></td>
      <td>Period</td>
      <td>Estimated Natural Forest Cover</td>
      <td>Deforestation (INPE)</td>
      <td>Natural forest cover change</td>
      <td>Forest cover as % of pre-1970 cover</td>
      <td>Total forest loss since 1970</td>
    </tr>
    <tr>
      <th>1</th>
      <td></td>
      <td>pre-1970</td>
      <td>4,100,000</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td></td>
      <td>1970</td>
      <td>4,001,600</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td></td>
      <td>1977</td>
      <td>3,955,870</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td></td>
      <td>1985</td>
      <td>3,864,945</td>
      <td>21,050</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>



#### Importing Data

After scraping the deforestation data, I will now import the Wikiaves.com dataset.

As I have already downloaded the Wikiaves dataset, I will simply use Pandas read_csv feature to import the data into my datatable called "bird_observations". I will print the datatable to further understand it's attributes as well.


```python
bird_observations = pd.read_csv("bird_observations.csv")
bird_observations.head()
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
      <th>Unnamed: 0</th>
      <th>author_id</th>
      <th>registry_id</th>
      <th>comment_count</th>
      <th>registry_date</th>
      <th>is_large</th>
      <th>location_id</th>
      <th>is_flagged</th>
      <th>like_count</th>
      <th>registry_link</th>
      <th>location_name</th>
      <th>views_count</th>
      <th>home_location_id</th>
      <th>species_id</th>
      <th>scientific_species_name</th>
      <th>popular_species_name</th>
      <th>species_wiki_slug</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>1.0</td>
      <td>173.0</td>
      <td>0.0</td>
      <td>2008-02-03</td>
      <td>0.0</td>
      <td>3202801</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>https://s3.amazonaws.com/media.wikiaves.com.br...</td>
      <td>Itapemirim/ES</td>
      <td>725.0</td>
      <td>3136702</td>
      <td>10530.0</td>
      <td>Athene cunicularia</td>
      <td>coruja-buraqueira</td>
      <td>coruja-buraqueira</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1.0</td>
      <td>3033917.0</td>
      <td>1.0</td>
      <td>2008-06-14</td>
      <td>1.0</td>
      <td>3302254</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>https://s3.amazonaws.com/media.wikiaves.com.br...</td>
      <td>Itatiaia/RJ</td>
      <td>45.0</td>
      <td>3136702</td>
      <td>11629.0</td>
      <td>Zonotrichia capensis</td>
      <td>tico-tico</td>
      <td>tico-tico</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>1.0</td>
      <td>3033916.0</td>
      <td>1.0</td>
      <td>2008-06-15</td>
      <td>1.0</td>
      <td>3302254</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>https://s3.amazonaws.com/media.wikiaves.com.br...</td>
      <td>Itatiaia/RJ</td>
      <td>34.0</td>
      <td>3136702</td>
      <td>10653.0</td>
      <td>Heliodoxa rubricauda</td>
      <td>beija-flor-rubi</td>
      <td>beija-flor-rubi</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>1.0</td>
      <td>18791.0</td>
      <td>5.0</td>
      <td>2008-06-15</td>
      <td>1.0</td>
      <td>3302254</td>
      <td>0.0</td>
      <td>5.0</td>
      <td>https://s3.amazonaws.com/media.wikiaves.com.br...</td>
      <td>Itatiaia/RJ</td>
      <td>377.0</td>
      <td>3136702</td>
      <td>10653.0</td>
      <td>Heliodoxa rubricauda</td>
      <td>beija-flor-rubi</td>
      <td>beija-flor-rubi</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>1.0</td>
      <td>11876.0</td>
      <td>4.0</td>
      <td>2008-06-15</td>
      <td>1.0</td>
      <td>3302254</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>https://s3.amazonaws.com/media.wikiaves.com.br...</td>
      <td>Itatiaia/RJ</td>
      <td>271.0</td>
      <td>3136702</td>
      <td>10636.0</td>
      <td>Thalurania glaucopis</td>
      <td>beija-flor-de-fronte-violeta</td>
      <td>beija-flor-de-fronte-violeta</td>
    </tr>
  </tbody>
</table>
</div>



### Data Processing

#### Cleaning the Data

The Wikiaves observation table includes many variables that are unimportant for my analysis, including the popular names of species, the comment counts of posts, and other columns that can be dropped from the table. 

I am converting the registry date for each post to a DateTime variable so I can form a new column with just the year, as that is what I care about when comparing against yearly deforestation data.

In the printed output, now there are only six variables, which can be interesting to interpret but may not appear useful individually.


```python
# Drop unnecissary columns
bird_observations = bird_observations.drop(['Unnamed: 0', 'registry_id','scientific_species_name', 
                                            'is_large', 'is_flagged', 'like_count', 'registry_link', 
                                            'comment_count', 'home_location_id', 'species_wiki_slug', 
                                            'popular_species_name'], axis = 1)
# Convert registry_date to datetime
bird_observations['registry_date'] = pd.to_datetime(bird_observations['registry_date'], infer_datetime_format = True)
bird_observations['year'] = pd.DatetimeIndex(bird_observations['registry_date']).year # Create year column
bird_observations = bird_observations.drop(['registry_date'], axis = 1) # Drop registry date
bird_observations.head() # Print the first five rows of the table
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
      <th>author_id</th>
      <th>location_id</th>
      <th>location_name</th>
      <th>views_count</th>
      <th>species_id</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>3202801</td>
      <td>Itapemirim/ES</td>
      <td>725.0</td>
      <td>10530.0</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>3302254</td>
      <td>Itatiaia/RJ</td>
      <td>45.0</td>
      <td>11629.0</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>3302254</td>
      <td>Itatiaia/RJ</td>
      <td>34.0</td>
      <td>10653.0</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>3302254</td>
      <td>Itatiaia/RJ</td>
      <td>377.0</td>
      <td>10653.0</td>
      <td>2008</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>3302254</td>
      <td>Itatiaia/RJ</td>
      <td>271.0</td>
      <td>10636.0</td>
      <td>2008</td>
    </tr>
  </tbody>
</table>
</div>



For this observation data to be useful, I must aggregate the individual observations into information that can be compared to our rainforest data. As the rainforest data contains information by the year, I will create a new DataTable out of the bird observation table which contains the total amount of bird observations, species found, locations where birds were seen, number of unique authors, and the amount of views on posts per year.

This new table contains the five attributes described and can be understood better after printing the first five rows.


```python
by_year = bird_observations.groupby(bird_observations['year']) # Group table by year column

# Future column arrays
years = []
bird_count = []
species_count = []
location_count = []
views_count = []
unique_authors = []

# For every year, aggregate the data and add to respective arrays
year_iter = iter(by_year)
for i in range(1, len(by_year)):
    year, frame = next(year_iter)
    years.append(year)
    bird_count.append(len(frame))
    species_count.append(len(frame['species_id'].unique()))
    location_count.append(len(frame['location_id'].unique()))
    views_count.append(frame['views_count'].sum())
    unique_authors.append(len(frame['author_id'].unique()))

# Add filled arrays into new DataFrame called observations
observations = pd.DataFrame()
observations['year'] = years
observations['bird observations'] = bird_count
observations['species count'] = species_count
observations['unique locations'] = location_count
observations['total post views'] = views_count
observations['unique authors'] = unique_authors
observations.head()
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
      <th>year</th>
      <th>bird observations</th>
      <th>species count</th>
      <th>unique locations</th>
      <th>total post views</th>
      <th>unique authors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008</td>
      <td>24673</td>
      <td>1237</td>
      <td>1341</td>
      <td>10579665.0</td>
      <td>1510</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2009</td>
      <td>64958</td>
      <td>1384</td>
      <td>1884</td>
      <td>25459591.0</td>
      <td>2363</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2010</td>
      <td>127033</td>
      <td>1536</td>
      <td>2427</td>
      <td>47113497.0</td>
      <td>3314</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
      <td>194321</td>
      <td>1655</td>
      <td>2931</td>
      <td>57132644.0</td>
      <td>4196</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012</td>
      <td>239962</td>
      <td>1698</td>
      <td>3163</td>
      <td>59724088.0</td>
      <td>5108</td>
    </tr>
  </tbody>
</table>
</div>



Now, the observation data from Wikiaves is ready for comparison, and we can move on to our data about Brazilian Amazon deforestation.

While there is less to clean in our deforestation dataset, the data needs to be scrubbed to be read as numbers correctly. I must import the locale library to properly remove the commas from what should be integer data.


```python
import locale
import re
locale.setlocale( locale.LC_ALL, 'en_US.UTF-8');
```

We will only be looking at the years from 2008 to 2018 as these are the only years we have information from Wikiaves. 

Here I am limiting the forest dataset to these years and dropping natural forest cover change, as it is unrelated to human-driven deforestation. I am converting all of the columns to integers and forest_cover_vs_pre1970 to a float. Printing the first five rows shows the new table with useable attributes. 


```python
forest = forest[forest.index > 26] # Only using years from 2008 to 2018
forest = forest[forest.index != 38]

forest.drop([0], axis = 1, inplace = True) # Drop unnecissary columns
forest.drop([4], axis = 1, inplace = True)

# Convert columns into integers (float for forest_cover_vs_pre1970 using RegEx)
forest.rename(columns = {1: 'year', 2: 'est forest cover', 3: 'deforestation', 5: 'forest cover vs pre1970', 6: 'total loss since 1970'}, inplace=True)
forest['year'] = pd.to_numeric(forest['year'])
forest['est forest cover'] = forest['est forest cover'].apply(lambda x: locale.atoi(str(x)))
forest['deforestation'] = forest['deforestation'].apply(lambda x: locale.atoi(str(x)))
forest['total loss since 1970'] = forest['total loss since 1970'].apply(lambda x: locale.atoi(str(x)))
forest['forest cover vs pre1970'] = forest['forest cover vs pre1970'].apply(lambda x: float(re.split(r"%", str(x))[0]))

forest.head()
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
      <th>year</th>
      <th>est forest cover</th>
      <th>deforestation</th>
      <th>forest cover vs pre1970</th>
      <th>total loss since 1970</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>27</th>
      <td>2008</td>
      <td>3451565</td>
      <td>12911</td>
      <td>84.2</td>
      <td>648435</td>
    </tr>
    <tr>
      <th>28</th>
      <td>2009</td>
      <td>3426846</td>
      <td>7464</td>
      <td>83.6</td>
      <td>673154</td>
    </tr>
    <tr>
      <th>29</th>
      <td>2010</td>
      <td>3433519</td>
      <td>7000</td>
      <td>83.7</td>
      <td>666481</td>
    </tr>
    <tr>
      <th>30</th>
      <td>2011</td>
      <td>3432446</td>
      <td>6418</td>
      <td>83.7</td>
      <td>667554</td>
    </tr>
    <tr>
      <th>31</th>
      <td>2012</td>
      <td>3432170</td>
      <td>4571</td>
      <td>83.7</td>
      <td>667830</td>
    </tr>
  </tbody>
</table>
</div>



#### Merging Datasets

For easier plotting and comparison, I will merge the two datasets into one table called "merged_table". I am joining the datasets on the year columns which are the same. Plotting the first five rows shows all attributes together in one table all represented for each year from 2008-2018.


```python
merged_table = observations.merge(forest, left_on = 'year', right_on = 'year')
merged_table.head()
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
      <th>year</th>
      <th>bird observations</th>
      <th>species count</th>
      <th>unique locations</th>
      <th>total post views</th>
      <th>unique authors</th>
      <th>est forest cover</th>
      <th>deforestation</th>
      <th>forest cover vs pre1970</th>
      <th>total loss since 1970</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008</td>
      <td>24673</td>
      <td>1237</td>
      <td>1341</td>
      <td>10579665.0</td>
      <td>1510</td>
      <td>3451565</td>
      <td>12911</td>
      <td>84.2</td>
      <td>648435</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2009</td>
      <td>64958</td>
      <td>1384</td>
      <td>1884</td>
      <td>25459591.0</td>
      <td>2363</td>
      <td>3426846</td>
      <td>7464</td>
      <td>83.6</td>
      <td>673154</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2010</td>
      <td>127033</td>
      <td>1536</td>
      <td>2427</td>
      <td>47113497.0</td>
      <td>3314</td>
      <td>3433519</td>
      <td>7000</td>
      <td>83.7</td>
      <td>666481</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
      <td>194321</td>
      <td>1655</td>
      <td>2931</td>
      <td>57132644.0</td>
      <td>4196</td>
      <td>3432446</td>
      <td>6418</td>
      <td>83.7</td>
      <td>667554</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012</td>
      <td>239962</td>
      <td>1698</td>
      <td>3163</td>
      <td>59724088.0</td>
      <td>5108</td>
      <td>3432170</td>
      <td>4571</td>
      <td>83.7</td>
      <td>667830</td>
    </tr>
  </tbody>
</table>
</div>



### Data Visualization and Exploration

An important step in the data science lifecycle is Data Visualization. While observing the table can show us information, it is much easier to understand our data if we use graphs to visualize the trends over time for different variables in our table.

I will be using matplotlib and seaborn python libraries to graph interesting attributes before analysis.


```python
import seaborn as sns
import matplotlib.pyplot as plt
```

#### Visualizing Amazon Deforestation

Below I will create a matplotlib subplot with four graphs depicting each of the columns related to Amazon deforestation. I am using seaborn's regplot function to form a scatter plot with a regression line to better see the general trend of the data. While useful, it is important to remember that with only 11 years on the x axis, a regression line may not fit the true trends of the data closely if it is non-linear. With this in mind, hopefully we can discover some things we hadn't considered before.


```python
fig = plt.figure(figsize = (10, 7))

# Estimated Forest Cover per Year
plt.subplot(221)
plt.title("Estimated Forest Cover per Year")
sns.regplot(data = merged_table, x = "year", y = "est forest cover")
plt.xlabel('Year')
plt.ylabel("Estimated Forest Cover (in Million Km^2)")

# Deforestation per Year
plt.subplot(2,2,2)
plt.title('Deforestation per Year')
sns.regplot(data = merged_table, x = 'year', y = 'deforestation')
plt.xlabel('Year')
plt.ylabel("Deforestation (Km^2)")

# Forest Cover vs Pre-1970 per Year
plt.subplot(223)
plt.title('Forest Cover vs Pre-1970 per Year')
sns.regplot(data = merged_table, x = 'year', y = 'forest cover vs pre1970')
plt.xlabel('Year')
plt.ylabel("Forest Cover (%)")

# Total Loss Since 1970 per Year
plt.subplot(2,2,4)
plt.title('Total Loss Since 1970 per Year')
sns.regplot(data = merged_table, x = 'year', y = 'total loss since 1970')
plt.xlabel('Year')
plt.ylabel("Total Loss Since 1970 (Km^2)")

plt.subplots_adjust(left=.125, bottom=.1, right=.9, top=.9, wspace=.4, hspace=.5)

plt.show()

```


![png](output_26_0.png)


These plots and their regression lines now clearly display the trends of deforestation, forest cover, and loss in the Amazon.

Estimated forest cover was fairly stagnant until around 2013 where it has decreased steadily since, and the forest cover compared to pre-1970 levels mimics the same trend but displayed through percentages. While deforestation decreased until 2012, since then it has been increasing, and the total loss of the Amazon since 1970 has also increased steadily after 2012.

It is interesting to see that it is only up until recently (roughly 2012-2013) when deforestation has become more rampant, destroying the forest cover and increasing the forest loss in total. 

We can see how these graphs are related by their similar or inverse slopes. Since Forest Cover vs Pre-1970 per Year and Estimated Forest Cover per year are representing the same information, I will remove Forest Cover vs Pre-1970 per Year as a variable for our further modeling.


```python
merged_table.drop(["forest cover vs pre1970"], axis = 1, inplace = True)
```

#### Visualizing Wikiaves Birdwatching Activity

Now that we understand our data considering the Amazon, we will now explore the data of Wikiaves users and their birdwatching activity. I will plot the four attributes related to Wikaves user activity: Birds Observed, Species Observed, Location Count, and Post Views.


```python
# Birds Observed per Year
fig = plt.figure(figsize = (14, 10))
plt.subplot(231)
plt.title("Birds Observed per Year")
sns.regplot(data = merged_table, x = "year", y = "bird observations")
plt.xlabel('Year')
plt.ylabel("Birds Observed")

# Species Observed per Year
plt.subplot(2,3,2)
plt.title('Species Observed per Year')
sns.regplot(data = merged_table, x = 'year', y = 'species count')
plt.xlabel('Year')
plt.ylabel("Species Observed")

# Location Count per Year
plt.subplot(233)
plt.title('Unique Locations per Year')
sns.regplot(data = merged_table, x = 'year', y = 'unique locations')
plt.xlabel('Year')
plt.ylabel("Unique Locations")

# Post Views per Year
plt.subplot(2,3,4)
plt.title('Total Post Views per Year')
sns.regplot(data = merged_table, x = 'year', y = 'total post views')
plt.xlabel('Year')
plt.ylabel("Views")

# Unique Authors per Year
plt.subplot(235)
plt.title('Unique Authors per Year')
sns.regplot(data = merged_table, x = 'year', y = 'unique authors')
plt.xlabel('Year')
plt.ylabel("Unique Authors")

plt.subplots_adjust(left=.125, bottom=.1, right=.9, top=.7, wspace=.4, hspace=.5)

plt.show()
```


![png](output_30_0.png)


Interestingly, it seems that from 2008 to 2015-2016, Birds Observed, Species Observed, and the Location Count per year have increased, although the trend becomes less convincing in 2016.

Despite the overall increase in birds and species observed along with bird diversity, the total number of people who viewed posts on Wikiaves.com had a sharp decrease in 2013 and has continuially fallen since. Looking at the number of unique posters on the site, there have been decreasing numbers since 2012, which could contribute to the drop in total views on posts. This data suggests that in recent years, there are less overall users on Wikiaves but each user posts more on average than in the past. 

#### Adjusting Variables from Results

Considering the dynamic between an increasing number of posts per year and less unique authors per year, I will create a new variable called "Avg Birds Observed per Unique Author" by dividing total bird observations over the unique authors per year, which I will explore further in this tutorial.


```python
merged_table['avg birds observed per unique author'] = merged_table['bird observations']/merged_table['unique authors']
```

For now, plotting Average Birds Observed per Unique Author shows us the average number of posts any particular user of Wikaves has submitted per year, which can help us understand the activity of the webpage over time.


```python
# Average Birds Observed per Unique Author per Year
sns.barplot(data = merged_table, x = 'year', y = 'avg birds observed per unique author')
plt.title('Average Birds Observed per Unique Author per Year')
plt.xlabel('Year')
plt.ylabel('Avg Birds Observed per Unique Author')
plt.show()
```


![png](output_34_0.png)


It's clear now that the average user on Wikiaves has increased their number of yearly posts over time.

#### Relationship between Deforestation and Wikiaves Activity

Vizualizing our datasets by themselves has helped us understand each better, but the main objective in this tutorial is exploring the relationship between deforestation in the Amazon and birdwatching activity on Wikiaves, which means we must put the two datasets together and see how one may or may not explain the other. Coming into this tutorial, my hypothesis was that as deforestation increased, the number of birds observed would decrease. Looking at these plots above, this doesn't seem to be the case for individual attributes, but exploring a combination of variables may help us discover relationships we did not expect.

### Model Creation and Analysis

Now that we have organized and gained an understanding of our data, it is time to enter the next step of the data science lifecycle, model creation and analysis. We will build a model that utilizes multiple of our deforestation variables to predict the number of birds observed in a given year and test its accuracy. 

#### Normalizing our Variables

Before we begin, a good practice in machine learning is standardizing or normalizing our variables depending on the models we are using, meaning we transform our variables into a format so that they can be more easily and practically compared and analyzed with each other. Considering some of our variables are in the scope of millions of km^2 and others in thousands, our machine learning models might be biased in favor of different variables simply because of their format and variance. If we tranform each variable to a range within 0 and 1 while maintaining the meaning behind each, all of the variables will be on an even playing field in our analysis. I will be normalizing all variables except year and bird observations.

Luckily, sklearn has a feature normalize in preprocessing which allows us to easily normalize our variables. If you would like to learn more about standardization and normalization please read here: (https://www.analyticsvidhya.com/blog/2020/04/feature-scaling-machine-learning-normalization-standardization/)

Printing the first five rows shows the new normalized attributes, all spanning from 0 to 1.


```python
from sklearn import preprocessing

# Selecting values for normalization
x = merged_table[['species count', 'unique locations', 'total post views', 'unique authors', 'est forest cover', 
                  'deforestation', 'total loss since 1970', 'avg birds observed per unique author']].values

# Using sklearn MinMaxScaler
min_max_scaler = preprocessing.MinMaxScaler()
scaled_values = min_max_scaler.fit_transform(x)

# Creating new table that has normalized values
normalized_table = pd.DataFrame(scaled_values) 
normalized_table.rename(columns = {0: 'species count', 1: 'unique locations', 2: 'total post views', 
                                   3: 'unique authors', 4: 'est forest cover', 5: 'deforestation', 
                                   6: 'total loss since 1970', 7: 'avg birds observed per unique author'}, inplace = True)
normalized_table.insert(loc = 0, column = 'year', value = merged_table['year'])
normalized_table.insert(loc = 9, column = 'bird observations', value = merged_table['bird observations'])

normalized_table.head()
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
      <th>year</th>
      <th>species count</th>
      <th>unique locations</th>
      <th>total post views</th>
      <th>unique authors</th>
      <th>est forest cover</th>
      <th>deforestation</th>
      <th>total loss since 1970</th>
      <th>avg birds observed per unique author</th>
      <th>bird observations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.037646</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>24673</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2009</td>
      <td>0.262970</td>
      <td>0.256981</td>
      <td>0.329027</td>
      <td>0.205443</td>
      <td>0.592969</td>
      <td>0.346882</td>
      <td>0.407031</td>
      <td>0.231942</td>
      <td>64958</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2010</td>
      <td>0.534884</td>
      <td>0.513961</td>
      <td>0.753057</td>
      <td>0.434489</td>
      <td>0.702849</td>
      <td>0.291247</td>
      <td>0.297151</td>
      <td>0.457492</td>
      <td>127033</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
      <td>0.747764</td>
      <td>0.752485</td>
      <td>0.949254</td>
      <td>0.646917</td>
      <td>0.685180</td>
      <td>0.221463</td>
      <td>0.314820</td>
      <td>0.623469</td>
      <td>194321</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012</td>
      <td>0.824687</td>
      <td>0.862281</td>
      <td>1.000000</td>
      <td>0.866570</td>
      <td>0.680636</td>
      <td>0.000000</td>
      <td>0.319364</td>
      <td>0.637337</td>
      <td>239962</td>
    </tr>
  </tbody>
</table>
</div>



#### Choosing Predictor Variables

Our goal when using our Machine Learning models will be understand if Brazilian Amazon deforestation is related to total bird observations on Wikiaves.com.

The Prediction Variables (X) are:

Estimated Forest Cover,
Deforestation,
Total Loss Since 1970

The Predicted Variable (y) is:

Bird Observations

Note that the train_test_split function randomly chooses which data will be reserved for the training and testing datasets. For this reason, the results of our Machine Learning models will have varying results depending on how our data is split.


```python
# Selecting variables for prediction and to be predicted
X = normalized_table[['est forest cover', 'deforestation', 'total loss since 1970']]
y = normalized_table[['bird observations']]
```

#### Choosing our Models

Now that our data is normalized, we are ready to start the machine learning process, which begins by choosing our models. As we have numerical continuous variables, we should use models that have the ability to work with continuous data. All models with a few exceptions are applicable to both, but my personal favorites are Linear Regression, Random Forests and Gradient Descent. We can use all three and then compare the results to see which work the best and if they are meaningful.

#### Splitting our Data

To test how our model performs, we need to split our data into a training set and a testing set. We will run our model on the training set and then test our model on the testing set which the model has never seen before, and see how well the predicted values compare to the actual values.

Sklearn has a built in feature called train_test_split which we can import and use. 70% of the data will be used for training, and 30% of the data will be used to test the result.

Note that the train_test_split function randomly chooses which data will be reserved for the training and testing datasets. For this reason, the results of our Machine Learning models will have varying results depending on how our data is split.


```python
from sklearn.model_selection import train_test_split

# Creating training and testing sets with train_test_split (70% for training, 30% for testing)
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size = .3)
```

#### Using our Models

Now that our data is split into training and testing sets, we can use our three Machine Learning models which are all built into sklearn. I will be using the score function to observe the r squared value of our results from the testing set.


```python
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.ensemble import RandomForestRegressor
from sklearn.linear_model import SGDRegressor
from scipy.stats import ttest_ind
from sklearn import metrics
import warnings
warnings.filterwarnings('ignore')
```

I will be using sklearn's LinearRegression function to train and test the data followed by RandomForestRegressor and SGDRegressor. These models will fit the training data to predict bird observation values of the x test data we reserved from the total dataset. We can obtain a score from the regressor which is it's r squared value (how much variation is explained by our model). We then will perform a sample t test to understand how much our model actually fit the true data. Note that depending

#### Multiple Linear Regression

Multiple Linear Regression is an extension of Linear Regression which uses several explanatory variables. This technique estimates the relationship between multiple independent variables to reach a numerical conclusion of the dependent variable, in this case, Birds Observed.

Learn more about Multiple Linear Regression here: https://www.scribbr.com/statistics/multiple-linear-regression/


```python
# Linear Regression
lrg = LinearRegression()
lrg.fit(x_train, y_train) # Fit the training data
lrg_ypred = lrg.predict(x_test) # Predict bird observations for x_test
t, p = ttest_ind(y_test, lrg_ypred, equal_var = False) # Sklearn sample t test
print("| Linear Regression:")
print("|")
print("| R Squared: ", lrg.score(x_test, y_test))
print("| Mean Squared Error: ", metrics.mean_squared_error(y_test, lrg_ypred)) # Metrics MSE calculation
print("| T value: ", t)
print("| P Value: ", p)
```

    | Linear Regression:
    |
    | R Squared:  0.3530148224049028
    | Mean Squared Error:  12191281030.2
    | T value:  [0.40182162]
    | P Value:  [0.69899475]


Our Linear Regression model calculated an R Squared of 0.353 and a large mean squared error, meaning that our model does not explain the variance of the data well. The P Value of 0.699 indicates that we would fail to reject the null hypothesis of equal mean between our predicted and actual values.

#### Random Forest Regression

The Random Forest Regression model works by constructing many random decision trees on small samples of the overall data which individually make a prediction. Each of these decision trees are are highly specific to these samples, but the model works by taking the mean prediction to estimate the most reasonable prediction.

Learn more about Random Forest Regression here: https://medium.com/swlh/random-forest-and-its-implementation-71824ced454f


```python
# Random Forest Regression
rfr = RandomForestRegressor(n_estimators = 500) # 500 decision trees
rfr.fit(x_train, y_train.values.ravel()) # Fit the training data
rfr_ypred = rfr.predict(x_test)
t, p = ttest_ind(y_test, rfr_ypred, equal_var = False)
print("| Random Forests")
print("|")
print("| R Squared: ", rfr.score(x_test, np.ravel(y_test)))
print("| Mean Squared Error: ", metrics.mean_squared_error(y_test, rfr_ypred)) # Metrics MSE calculation
print("| T Value: ", t)
print("| P Value: ", p)
print()
```

    | Random Forests
    |
    | R Squared:  0.3324797851756117
    | Mean Squared Error:  12578227158.948227
    | T Value:  [-0.57666702]
    | P Value:  [0.58944592]
    


The Random Forest model calculated a similar R Squared value of 0.333 and has a large MSE. The P value also indicates that we would fail to reject the null hypothesis of equal mean between our predicted and actual values.

#### Stochastic Gradient Descent Regression

The Gradient Descent Regression model is an iterative algorithm which in each run attempts to reduce the error between the the actual y value and the predicted y value. It does this using a gradient (which with one variable is the slope) of our prediction equation. Stochastic Gradient Descent means this algorithm is run on random data points from our data set on each iteration to make the process more efficient.

Learn more about Stochastic Gradient Descent here: https://towardsdatascience.com/stochastic-gradient-descent-clearly-explained-53d239905d31


```python
# Stochastic Gradient Descent Regression
reg = SGDRegressor(max_iter = 7000) # Using 7000 iterations 
reg.fit(x_train, np.ravel(y_train)) # Fitting training data
reg_ypred = reg.predict(x_test) # Sklearn sample t test
t, p = ttest_ind(y_test, reg_ypred, equal_var = False) # Sklearn t test between y's
print("| Stochastic Gradient Descent: ")
print("|")
print("| R Squared: ", reg.score(x_test, np.ravel(y_test)))
print("| Mean Squared Error: ", metrics.mean_squared_error(y_test, reg_ypred)) # Metrics MSE calculation
print("| T Value: ", t)
print("| P Value: ", p)
```

    | Stochastic Gradient Descent: 
    |
    | R Squared:  0.6392131044124635
    | Mean Squared Error:  6798385169.302546
    | T Value:  [-0.18855047]
    | P Value:  [0.85643161]


The Stochastic Gradient Descent model derived an R Squared value of 0.639, meaning that our model explains approximately 63.9% of the data. While this is better than the values previously observed, the P value of 0.85 also indicates that we would fail to reject the null hypothesis of equal mean between our predicted and actual values.

### Results

We can display our outputs visually through bar graphs using seaborn's catplot function.


```python
# Creating DataFrame with results to graph
results = pd.DataFrame()
results['Percent'] = [0.353, 0.333, 0.639, .699, .589, .856]
results['Method'] = ['Linear Regression', 'Random Forests', 'Gradient Descent', 
                     'Linear Regression', 'Random Forests', 'Gradient Descent']
results['Statistic'] = ['R Squared', 'R Squared', 'R Squared', 'P', 'P', 'P']

# Using seaborn catplot to plot results
plot = sns.catplot(
    data = results, kind = "bar", x = 'Method', y = 'Percent', 
    hue = 'Statistic', palette = 'dark', alpha = .6, height = 5)
plot.despine(left = True)
plot.set_axis_labels("", "Percent (%)")
plt.title("Results of ML Methods")
plt.show()
```


![png](output_55_0.png)


#### Relatively low R Squared values paired with high P values designates an overall result to fail to reject the null hypothesis of equal mean between our predicted and actual values, and suggests little to no correlation between deforestation in the Amazon and bird observations posted on Wikiaves.com.

### Conclusion



The final step in the data science lifecycle is drawing conclusions from the analysis on our original data. As our results displayed, the machine learning analysis resulted in low R squared values and our sample T tests resulted in high P values, suggesting no meaningful correlation between Brazilian Amazon Rainforest deforestation and Wikiaves birdwatching observations. This conclusion rejects my initial hypothesis.

#### Insights

While visualizing the data, I noticed that over time, Wikiaves bird observations have generally increased. Despite this, from 2014 to 2018, the number of unique posters actually decreased. My suspicions are that the number of observations being posted on Wikiaves could be more impacted by dedicated users who tend to post often as a part of the birdwatching community, despite any deforestation activity happening in the Amazon Rainforest. If anything, it could be the case that as these bird species become more endangered, dedicated birdwatchers have even more reason to search for them while they have the chance. 

Plotting the average birds observed for each unique author against bird observations per year could give us some indication if these ideas are related.


```python
# Average Birds Observed per Unique Author vs Bird Observations
sns.regplot(data = merged_table, x = 'avg birds observed per unique author', y = 'bird observations')
plt.title('Average Birds Observed per Unique Author vs. Bird Observations')
plt.xlabel('Average Birds Observed per Unique Author')
plt.ylabel('Bird Observations')
plt.show()
```


![png](output_59_0.png)


The positive slope on this regression line suggests there is a fairly strong linear relationship between the average amounts of posts per user and the number of total bird observations, despite the overall user base decreasing in numbers over time. As the average user has posted more frequently on Wikiaves.com, the total observations have increased.
