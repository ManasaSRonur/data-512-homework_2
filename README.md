# Homework 2: Considering Bias in Data
## DATA 512 Human Centered Data Science
## Project Goal
The goal of this assignment is to explore the concept of bias in data using Wikipedia articles. We will consider articles on political figures from different countries. The idea is to combine a dataset of Wikipedia articles with a dataset of country populations and use a machine learning service called ORES to estimate the quality of each article. And then perform an analysis of how the coverage of politicians on Wikipedia and the quality of articles about politicians vary among countries. Our analysis will primarily include three aspects, as mentioned below:

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

These files are under the folder [inputfiles](https://github.com/ManasaSRonur/data-512-homework_2/tree/main/inputfiles)

1. [politicians_by_country.AUG.2024.csv](https://github.com/ManasaSRonur/data-512-homework_2/blob/main/inputfiles/politicians_by_country_AUG.2024.csv)

    This csv file contains a list of Wikipedia article pages about politicians from a wide range of countries with below fields:

    - **name**: string - Name of the wikipedia article.
    - **url**: string - Wikipedia page URL.
    - **country**: string - country of the politician.

2. [population_by_country_AUG.2024.csv](https://github.com/ManasaSRonur/data-512-homework_2/blob/main/inputfiles/population_by_country_AUG.2024.csv)

    This csv file contains population data represented in millions.

    - **Geography**: string - Representing geography such as region (distinguished by all caps) and country.
    - **Population**: decimal - Number representing population in millions.

#### Intermediary Data Files:

These files are under the folder [intermediary_files](https://github.com/ManasaSRonur/data-512-homework_2/tree/main/intermediary_files)

1. [articles_page_info.csv](https://github.com/ManasaSRonur/data-512-homework_2/blob/main/intermediary_files/articles_page_info.csv) - File containing page info of the articles extracted through MediaWiki Action API. It has below fields:
    - **pageid**: integer - Wikipedia page id
    - **title**: string - Wikipedia article title
    - **lastrevid**: integer - Last revision id of the article


2. [articles_ores_scores.csv](https://github.com/ManasaSRonur/data-512-homework_2/blob/main/intermediary_files/articles_ores_scores.csv) - File containing article quality predictions from ORES API for each of the articles using revision_id from page info. It has below fields:
    - **pageid**: integer - Wikipedia page id
    - **title**: string - Wikipedia article title
    - **revision_id**: integer - Last revision id of the article
    - **quality_prediction**: string - Predicted quality of the article



#### Output Data Files:

The below output files are under the folder [generated_files](https://github.com/ManasaSRonur/data-512-homework_2/tree/main/generated_files)

1. [wp_politicians_by_country.csv](https://github.com/ManasaSRonur/data-512-homework_2/blob/main/generated_files/wp_politicians_by_country.csv) - A csv file containing the following columns of data.
    - **country**: string -  Country Name
    - **region**: string -  country Region
    - **population**: decimal - Population of the country in millions
    - **article_title**: string - Wikipedia article title
    - **revision_id**: integer -  Article last revision id
    - **article_quality** string -  ORES quality score of the Article

2. [wp_countries-no_match.txt](https://github.com/ManasaSRonur/data-512-homework_2/blob/main/generated_files/wp_countries-no_match.txt) - A text file with all the countries for which there are no matches between the population dataset and Wikipedia data.

### Data Processing Workflow

Step 1: Retrieval of Wikipedia Page Data

The script iterates through a dataset containing article titles, removes duplicates and for each article, it retrieves page info data from the Wikimedia API and saves it to intermediary file for later use.

Step 2: Quality Score (ORES Score) Retrieval

For each article, the script reads the corresponding last revision id and uses it to make a request to the ORES API to obtain the quality score for that article. If the quality score is successfully retrieved, it is saved into the intermediary csv file for later use.
If for any reason, ORES request fails for an article or if the last revision id is not available for an article, then details of such articles are recorded.

Step 3: Output file generation with both population and wikipedia article data.

After retrieving and including the ORES data for each article, the Wikipedia page info data and ores data are merged together, so all articles without the last revision id are retained with blank values for score. This dataset is merged together with population data, and the resulting data is stored in the csv file in the format described earlier. The countries that were not matched during the final merge are recorded and stored as text file.

Step 4: Analysis

The analysis includes generating the below tabular insights from the output files generated.

- Top 10 countries by coverage: The 10 countries with the highest total articles per capita (in descending order).
- Bottom 10 countries by coverage: The 10 countries with the lowest total articles per capita (in ascending order).
- Top 10 countries by high quality: The 10 countries with the highest high quality articles per capita (in descending order).
- Bottom 10 countries by high quality: The 10 countries with the lowest high quality articles per capita (in ascending order).
- Geographic regions by total coverage: A rank ordered list of geographic regions (in descending order) by total articles per capita.
- Geographic regions by high quality coverage: A rank ordered list of geographic regions (in descending order) by high quality articles per capita.


### Research Implications.


#### Key Findings/Reflection
This analysis of Wikipedia's coverage of politicians reveals interesting patterns in both representation and article quality across different countries. I was surprised by the gap between countries with the most and least articles per person. Smaller countries like Monaco and Tuvalu have a lot of articles compared to their small populations, while larger nations like China and India have far fewer. As expected, countries with strict media control or limited access, like China, tend to be underrepresented on Wikipedia, showing how government restrictions can influence online visibility. Additionally, the top countries by article count don’t match those with the highest-quality articles, proving that more coverage doesn’t always mean better content.

I learned that smaller Western or Western-influenced countries tend to receive more coverage, while larger, more populous nations, especially in Asia, are underrepresented. This could be due to several factors, such as Wikipedia's preference for countries with higher English proficiency, better internet access, and more digital literacy. Countries with more open political systems might also have more publicly available information about their politicians.

#### What biases did you expect to find in the data (before you started working with it), and why?
I thought there would be biases related to political issues and government control over information. Countries with strict media rules would likely have fewer articles or fewer high quality articles, since certain topics might be restricted or not well covered. Similarly, countries with limited access to internet devices or developing countries would likely have fewer or lower quality articles.

#### What (potential) sources of bias did you discover in the course of your data processing and analysis?
I found major differences based on population size and articles. For example, large countries like China and India had fewer articles per person, likely due to limits on free speech and access to information. On the other hand, smaller countries might have more articles compared to their size, which could give a false impression of their political importance.

#### What might your results suggest about (English) Wikipedia as a data source?
The findings indicate that Wikipedia's content is strongly influenced by the official languages of the countries it represents. The analysis reveals that countries with the least article coverage and the least high quality articles are those where English is not an official language and where there are very few English speakers. In countries where English is not an official language, the English version of Wikipedia may have limited information, leading to an unbalanced view of their political situations. This highlights the importance of recognizing how language diversity and access affect content creation, and the difficulties non-English-speaking countries face in having their political stories accurately represented on platforms like Wikipedia.

#### Can you think of a realistic data science research situation where using these data (to train a model, perform a hypothesis-driven research, or make business decisions) might create biased or misleading results, due to the inherent gaps and limitations of the data?
A realistic example of biased results could occur if a global politics analysis model is trained on this Wikipedia data. It might overemphasize politicians from smaller Western countries while underrepresenting those from larger non-Western nations. Similarly, the model will overestimate the influence of politicians from the English-speaking countries while undermining the influence of prominent figures from the non-English-speaking countries. This could lead to a skewed understanding of global politics and inaccurate analysis of political influence or international relations.

#### Can you think of a realistic data science research situation where using these data (to train a model, perform hypothesis-driven research, or make business decisions) might still be appropriate and useful, despite its inherent limitations and biases?
Even though Wikipedia data on political figures has some limitations and biases, it can still be useful in certain research scenarios. One example is studying how online political engagement relates to digital democracy in countries. The research could explore whether countries with better Wikipedia coverage of their politicians have higher citizen participation in digital democracy efforts, such as online voting or public consultations. While the results might not apply to larger countries or those with limited Wikipedia presence, this research could help us understand the role of online platforms in boosting democratic engagement and digital political participation in small nations.

In summary, this analysis reminds us that using digital/social platforms for information is complicated. It highlights the need to carefully examine the data, assess the quality for bias, and be aware of its limitations.

### Limitations/Considerations.

There are entries in the article list, that seem to be not politicians, such as Julius Lippert (historian), Robert Rey (plastic surgeon), Kostadin Kostadinov (professor), and many more. Upon investigation of a few examples, I found that though their primary field of work is not politics, they were associated with politics at some point in their lives, making political impact. Upon the assumption that this is true for the rest of such titles, they are retained for analysis.

The ores scores API, randomly failed to process data for a few articles, mostly due to a timeout error. In my first run, I had 7 failures; during the second run, I just had one failure different from the previous failures, making this data source not very reliable.

There are few articles with missing last revision, leading to a to a missing ores score. If these articles are for countries with extremely small populations, these numbers can make a difference in the final analysis, potentially changing the ranks of those countries.

There are countries like "Monaco" and "Tuvalu" with the population value in million as 0. Technically,  the population is not 0, its less than a million, probably in a few thousands. So I am dropping these values for analysis, as we do not have an exact population value to calculate the per capita value.



