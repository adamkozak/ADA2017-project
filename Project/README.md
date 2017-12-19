# Analysis of reviews of books on Amazon

Nathalie Crevoisier, Adam Kozak, Tarmo Nurmi

**Data story:** https://adamkozak.github.io/ADA2017-project/

# Updates for milestone 3:

+ Minor text modifications to Abstract

+ Arriving at the research questions section is a modified version of Milestone 2 section in last milestone's readme. It gives a rough outline of our process of arriving at the final questions we chose, and necessary definitions.

+ Final research questions updated

+ Internal milestone list updated

+ **Contributions** section added

+ The data pipeline notebook is **AMAZON BOOKS - Project ADA 2017 .ipynb**


# Abstract
The goal of the project is to analyze the reviews of books in the [Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/). The main research questions are: _How can negativeness or positiveness of reviews and ratings influence the popularity of a book? How do the sentiments of reviews evolve over time?_ Here, the popularity of a book is defined as the total number of reviews (time-independent popularity) or the number of reviews accrued in a unit of time (time-dependent popularity). The goal is therefore to investigate the interplay between the popularity of a book and the sentiment expressed by the reviewer in his/her review and rating, aggregated according to various grouping categories (good/bad/controverisal books, books of a certain genre, etc.) and time. To find the category definitions, different ways of determining book "goodness" are considered. For sentiment analysis, a sentence-based positive/negative classifier is used. To enrich the dataset, the Amazon website is scraped for additional information on the books under consideration.


# Dataset

The dataset we will use is the [Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/), and there we will analyze the _Books_ category. Since our goal is to analyze review and rating data of books, we will use the _5-core_ subset of book review data, which contains only products which have at least 5 reviews. This is a choice to remove noise in the data and make statistics (mean rating etc.) more sensible. The _5-core_ data contains 8,898,041 reviews of 367,982 books, and each review has an associated star rating (1-5 stars). Additionally, we will use the _Books_ metadata from the same website, which includes metadata of the books in the review data set. In the review data, books are identified by their Amazon identifier number (asin), and the metadata describes e.g. the actual book title, price, and so on.

To handle the data, we use [Apache Spark™](https://spark.apache.org/). The data is in JSON format readable in Python. Since the data set is ~10 gigabytes, we can handle it by running Spark locally.

We assume that the data is from Amazon in the United States, and that the reviews are in English.

**To enrich the data set**, we wanted to also get the categories (science fiction, thriller, etc.) and keywords for each book. This was done by scraping the Amazon website "product details" section for each product. Since we cannot process huge numbers of products this way, we limited our enrichment attention to most interesting books (ones having high numbers of reviews, for example). _(See Amazon scraper.ipynb notebook.)_


# Arriving at the research questions

## Data poking

We started by poking the data by various means:

+ Mean length of reviews by month, day and book

+ Number of reviews by month and day (for all books and top 30 books according to number of reviews), and by book

+ Number of 1, 2, 3, 4, and 5-star ratings by book, and mean rating by book

The exact results and visualizations can be found in the project pipeline notebook. Importantly, we found that the rate of reviewing is very unevenly distributed, with a lot more reviews being written after approximately 2013 than before, and that the distribution of mean ratings is heavily left-skewed (meaning a lot of the distribution mass is on the high ratings of 4 and 5).

## Refined question definitions

After our data poking and progress after milestone 2, we chose our research quesions to be:

_How can negativeness or positiveness of reviews and ratings influence the popularity of a book? How do the sentiments of reviews evolve over time?_

To this end, we need to define the "popularity" of a book.
We decided on two different definitions for a popularity metric:

1. Time-independent popularity: the number of reviews at the end of the dataset time period (July 2014)
2. Time-dependent popularity: the number of reviews at the end of the dataset time period, divided by the number of months between the first review in the dataset and the end of the dataset period

The metrics are based on the idea that a "popular" book will accrue lots of reviews, and an "unpopular" one will not. therefore, there is no judgment of the quality or goodness of the book by this notion of popularity.
Metric 1 just looks at the number of reviews gathered between May 1996 and July 2014, and therefore favors older books that were pubished earlier (since they will have had more time to accrue reviews.) By contrast, metric 2 favors newer books, since the rate of commenting on books on Amazon is higher the closer we are to the present day in time (because of internet accessibility, etc.).

We also wanted to group books into categories based on the general sentiment towards them. For this, we used the Amazon star ratings (1-5 stars, a rating and a review are always given together). We labeled the books on goodness and controversiality as follows:

|  | High average rating | Neutral average rating | Low average rating |
| :---: | :---: | :---: | :---: |
| **Low rating variance** | Good, uncontroversial book | Neutral, uncontroversial book | Bad, uncontroversial book |
| **Neutral rating variance** | Good, neutral book | Neutral-neutral book | Bad, neutral book |
| **High rating variance** | Good, controversial book | Neutral, controversial book | Bad, controversial book |

Each book with at least 10 reviews was given two classifications: goodness and controversiality. For each classification, three categories were used (good/uncontroversial, neutral/neutral, bad/controversial), resulting in a total of 9 cross-section categories (good and controversial, good and uncontroversial, good and controversy-neutral, etc.).

To determine book goodness category, we needed the notion of "high" and "low" rating. Since the mean rating distribution is left-skewed, 3 does not represent the average rating in this data. Here we observe the same phenomenon as in movie or video game ratings: most products that make it to publishing aren't objectively too horrible, so mean ratings are quite high. In the context of this data set, an objectively _okay_ book can be _bad_ with respect to the other books. Therefore, we have decided to determine goodness of the mean rating by splitting the data according to percentiles: the ratings below the 25th percentile are _low_, the ratings above the 75th percentile are _high_, and the middle 50 % are _goodness-neutral_. A similar 25-50-25 split was used on the standard deviations of the ratings to classify books into _controversial_ (high standard deviation), _uncontroversial_ (low standard deviation), and _controversy-neutral_ (middle ground).

Grouping by general sentiment is not the only category grouping we considered. Another grouping could be based on the speed by which the rate of receiving new reviews becomes small. If a book is a "fad", then it will be very popular for a short while and then forgotten, whereas more consistently popular books will receive reviews for a long time. This grouping is based on our observation while data poking: when a book is published, it will receive lots of reviews, and the review rate will slow down at different pace for different books. We poked this direction but decided to ultimately use the rating sentiments as our classifications.

## Review negativity and positivity

We decided to use the [VADER sentiment analysis toolkit](https://github.com/cjhutto/vaderSentiment) to analyze the positivity and negativity of the text in reviews. VADER gives a compound score between -1 and 1 for a sentence, where -1 is as negative and 1 is as positive as possible. We found that VADER scores of reviews follow well the ratings given with the review. Therefore, the rating value can be used as a good estimate of the reviewer's sentiment towards the book.


# Final research questions

## Major questions:

+ How can negativeness or positiveness of reviews and ratings influence the popularity of a book?

+ How do the sentiments of reviews evolve over time?

+ Can we see a snowball effect in reviews and ratings?

## Intermediate questions on the way to major questions:

+ How can we classify books into good, bad, and those for which there is no clear consensus?

+ Is there a discrepancy between a rating and its associated review's sentiment?
  * Do people give good ratings even though their comment is negative?


# A list of internal milestones

## Up until milestone 2:

- [x] Get familiar with the data

- [ ] ~~Learn how to use the IC faculty cluster in order to perform basic statistics~~
  * Not fulfilled, since running Spark locally was sufficient

- [x] Learn the basics of Hadoop/Spark/the best tool for the task

- [x] Find the best frameworks for text processing/sentiment analysis
  * VADER was decided upon since it is a complete package and provides one comparable score

- [x] After getting familiar with the data, try to pick the project goal or
goals that are the most feasible and have the possibility of generating
interesting results

- [x] Create a IPython notebook for the project pipeline

- [x] Access the dataset and perform the necessary statistics tasks

- [x] Update the ReadMe description according to decisions made in the course of data analysis

- [x] Organize the work/create a roadmap for next milestones (report/data story and presentation)

## Up until milestone 3 (report):

- [x] Decide all models of grouping data into categories (negative/positive, fast/slow plateauing of review numbers, etc.)

- [x] Decide parameters and thresholds for category definitions based on the data

- [x] Enrich data by scraping Amazon for genres as extensively as possible

- [x] Create necessary intermediate files containing formatted data, to speed up analysis and computing

- [x] Find the best way to use VADER to analyze positivity and negativity of reviews

- [x] Calculate correlations between popularity metrics and positive and negative reviews and ratings
  * With respect to time
  * By aggregation by category (good, bad, controversial)
  * By aggregation by genre and author

- [x] Find the exact data story we want to tell (choose exact questions), make it compelling, and link it to social good

- [x] Decide format of report

- [x] Depending on report format, distribute tasks to individual group members

- [x] Produce good, polished visualizations that tell the story

- [x] Construct report into a coherent document

- [x] Update readme according to progress

- [x] Create a plan for presentation and poster creation

## Up until presentation:

- [ ] Create poster in required format (Everyone)

- [ ] Present poster (Nathalie)

# Contributions:

NATHALIE: 
First naïve way of classifying books into categories, analyses using good/bad/controversial classifications, analyses using Vader results, time evolutions analyses. Readme file updating, final comments on notebook, visualizations. Will present the final presentation. 

ADAM: 
Data handling using Spark, creation of many intermediate datasets to be handled without Spark, data poking and plots drawing to dig into the dataset, scraping from Amazon for book categories and all analyses using those categories, K-means for books classification, Vader calculations, influence of first ratings analysis, snowball effect analysis, visualizations. 

TARMO: 
Data story (report) planning and writing, question formulation, final book classifications into good-bad and controversial-uncontroversial classes, cross-section categories calculations, visualizations, readme file writing and updating, notebook commenting.

