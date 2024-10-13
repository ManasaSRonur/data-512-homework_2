# Homework 2: Considering Bias in Data
## DATA512 Human Centered Data Science
## Goal
The goal of this assignment is to explore the concept of bias in data using Wikipedia articles. We will consider articles on political figures from different countries. The idea is to combine a dataset of Wikipedia articles with a dataset of country populations, and use a machine learning service called ORES to estimate the quality of each article. And then perform an analysis of how the coverage of politicians on Wikipedia and the quality of articles about politicians varies among countries. Our analysis will primarily include three aspects as mentioned below:

1. The countries with the greatest and least coverage of politicians on Wikipedia compared to their population.
2. The countries with the highest and lowest proportion of high quality articles about politicians.
3. A ranking of geographic regions by articles-per-person and proportion of high quality articles.



### License

#### Data License
The source data is obtained from the Wikimedia Pageviews API. The data is provided under the **Creative Commons Attribution-ShareAlike 3.0 Unported License (CC BY-SA 3.0)**.

**Summary of the Terms of Use**:
According to Wikimedia's terms, any data extracted from Wikipedia must attribute the source and be shared under the same or compatible license. For this project, any dataset created must acknowledge the Wikimedia Foundation and must not be used in a manner that violates the privacy of Wikipedia's contributors or users.

The full terms of use for Wikimedia Foundation's content can be found [here](https://foundation.wikimedia.org/wiki/Terms_of_use).

#### Code Attribution

Snippets of the code were taken from a code example developed by **Dr. David W. McDonald** for use in DATA 512, a course in the UW MS Data Science degree program. This code is provided under the **Creative Commons CC-BY license**.

### API Documentation/References

1. Wikimedia REST API:
    - [MediaWiki Action API](https://en.wikipedia.org/w/api.php)
    - [ORES API](https://www.mediawiki.org/wiki/ORES)

3. **Python packages/libraries**: 
    - [Requests](https://docs.python-requests.org/en/latest/)
    - [pandas](https://pandas.pydata.org/docs/reference/index.html)
    - [urllib.parse module](https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse)
    - [csv module](https://docs.python.org/3/library/csv.html)

#### Access Token

Access to the ORES API will require that you request an API access key.

To Get your access token: You will need a Wikimedia user account to get access to Lift Wing (the ML API service). You can either [create an account or login](https://api.wikimedia.org/w/index.php?title=Special:UserLogin&centralAuthAutologinTried=1&centralAuthError=Not+centrally+logged+in). If you have a Wikipedia user account - you might already have an Wikimedia account. If you are not sure try your Wikipedia username and password to check it. If you do not have a Wikimedia account you will need to create an account that you can use to get an access token.

Here is the [guide](https://api.wikimedia.org/wiki/Authentication) provides detailed steps on generating access token.

Note, when you create a Personal API token you are granted the three items - a Client ID, a Client secret, and a Access token - you shold save all three of these. When you dismiss the box they are gone. If you lose any one of the tokens you can destroy or deactivate the Personal API token from the dashboard and then create a new one.


### Data Files

#### Input Data Files:
    
1. [politicians_by_country.AUG.2024.csv]()

    This csv file contains a list of Wikipedia article pages about politicians from a wide range of countries with below fields:

    - **name**: string -  Name of the wikipedia article.
    - **url**: string - Wikipedia page URL.
    - **country**: string - country of the politician.

2. [population_by_country_AUG.2024.csv]()

    This csv file contains population data represented in millions.

    - **Geography**: string -  Representing geogrpahy such as region (distinguhed by all Caps) and country.
    - **Population**: decimal - Number representing population in millions.

#### Intermediary Data Files:
1. [articles_page_info.csv]() - File containing page info of the artciles extracted through MediaWiki Action API. It has below fields:
- **pageid** - 
- **ns** - 
- **title**
- **contentmodel**
- **pagelanguage**
- **pagelanguagehtmlcode**
- **pagelanguagedir**
- **touched**
- **lastrevid**
- **length**
- **talkid**
- **fullurl**
- **editurl**
- **canonicalurl**
- **watchers**
- **missing**
- **redirect**
- **new**

2. [articles_ores_scores.csv]() -  File containing article quality predictions from ORES API for each of the articles using revision_id from page info.


#### Output Data Files:

1. [wp_politicians_by_country.csv]() - A csv file containing following columns of data.
- **country**
- **region**
- **population**
- **article_title**
- **revision_id**
- **article_quality**

2. [wp_countries-no_match.txt]()  - A text file with all the countries for which there are no matches between the the population dataset and Wikipedia data.

### Data Processing workflow

Step 1: Retrieval of Page info Data

The script iterates through a dataset containing article titles and for each article, it retrieves page info data from the Wikimedia API and saves it to internediary file for later use.

Step 2: Quality Score Retrieval

For each article, the script reads the corresponding last revision id and uses it to make a request to the ORES API to obtain the quality score for that article. If the quality score is successfully retrieved, it is saved into the intermediary csv file for later use.
If for any reason, ORES requests fails for an article or if the last revision id is not available for an article, then details of such articles are recorded.

Step 3: Output file generation

After retrieving and including the ORES data for each article, the Wikipedia data and population data are merged together and resulting data is stored in the csv file in the format as described earlier. The countries that were not matched during the merge are recorded and stored as text file.

Step 4: Analysis

The analysis includes generating the below tabular insights from the output files generated.

- Top 10 countries by coverage: The 10 countries with the highest total articles per capita (in descending order).
- Bottom 10 countries by coverage: The 10 countries with the lowest total articles per capita (in ascending order).
- Top 10 countries by high quality: The 10 countries with the highest high quality articles per capita (in descending order).
- Bottom 10 countries by high quality: The 10 countries with the lowest high quality articles per capita (in ascending order).
- Geographic regions by total coverage: A rank ordered list of geographic regions (in descending order) by total articles per capita.
- Geographic regions by high quality coverage: Rank ordered list of geographic regions (in descending order) by high quality articles per capita.


### Research Implications.



