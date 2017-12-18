# Analysis of reviews of books on Amazon

Nathalie Crevoisier, Adam Kozak, Tarmo Nurmi

# Updates for milestone 2:

+ Abstract and dataset description modified

+ Research questions and internal milestones updated

+ Milestone 2 section added, where progress is described

+ The data pipeline notebook is **Data_processing_CLEAN.ipynb**


# Abstract
The goal of the project is to analyze the reviews of books in the [Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/). The main research questions are: _How can negativeness or positiveness of reviews and ratings influence the popularity of a book? How do negative and positive reviews and ratings evolve with time?_ Here, the popularity of a book is defined as the total number of reviews (time-independent popularity) or the number of reviews accrued in a unit of time (time-dependent popularity). The goal is therefore to investigate the interplay between the popularity of a book and the sentiment expressed by the reviewer in his/her review and rating, aggregated according to various grouping categories (good/bad/controverisal books, books of a certain genre, etc.) and time. To find the category definitions, different ways of determining book "goodness" are considered. For sentiment analysis, a sentence-based positive/negative classifier is used. To enrich the dataset, the Amazon website is scraped for additional information on the books under consideration.


# Dataset

The dataset we will use is the [Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/), and there we will analyze the _Books_ category. Since our goal is to analyze review and rating data of books, we will use the _5-core_ subset of book review data, which contains only products which have at least 5 reviews. This is a choice to remove noise in the data and make statistics (mean rating etc.) more sensible. The _5-core_ data contains 8,898,041 reviews of 367,983 books, and each review has an associated star rating (1-5 stars). Additionally, we will use the _Books_ metadata from the same website, which includes metadata of the books in the review data set. In the review data, books are identified by their Amazon identifier number (asin), and the metadata describes e.g. the actual book title, price, and so on.

To handle the data, we use [Apache Spark™](https://spark.apache.org/). The data is in JSON format readable in Python. Since the data set is ~10 gigabytes, we can handle it by running Spark locally.

We assume that the data is from Amazon in the United States, and that the reviews are in English.

**To enrich the data set**, we wanted to also get the categories (science fiction, thriller, etc.) and keywords for each book. This was done by scraping the Amazon website "product details" section for each product. Since we cannot process huge numbers of products this way, we have thus far limited our enrichment attention to most interesting books (ones having high numbers of reviews, for example). _(See Amazon scraper.ipynb notebook.)_


# Milestone 2

## Data poking

We have poked the data by various means, looking at e.g.

+ Mean length of reviews by month, day and book

+ Number of reviews by month and day (for all books and top 30 books according to number of reviews), and by book

+ Number of 1, 2, 3, 4, and 5-star ratings by book, and mean rating by book

The exact results and visualizations can be found in the project pipeline notebook. Importantly, we found that the rate of reviewing is very unevenly distributed, with a lot more reviews being written after approximately 2013 than before, and that the distribution of mean ratings is heavily left-skewed (meaning a lot of the distribution mass is on the high ratings of 4 and 5).

## Refined question definition

After our data poking, we have chosen our main questions (for now) to be:

_How can negativeness or positiveness of reviews and ratings influence the popularity of a book? How do negative and positive reviews and ratings evolve with time?_

To this end, we need to define the "popularity" of a book.
We decided on two different definitions for a popularity metric:

1. Time-independent popularity: the number of reviews at the end of the dataset time period (July 2014)
2. Time-dependent popularity: the number of reviews at the end of the dataset time period, divided by the number of months between the first review in the dataset and the end of the dataset period

The metrics are based on the idea that a "popular" book will accrue lots of reviews, and an "unpopular" one will not. therefore, there is no judgment of the quality or goodness of the book by this notion of popularity.
Metric 1 just looks at the number of reviews gathered between May 1996 and July 2014, and therefore favors older books that were pubished earlier (since they will hav ehad more time to accrue reviews.) By contrast, metric 2 favors newer books, since the rate of commenting on books on Amazon is higher the closer we are to the present day in time (because of internet accessibility, etc.).

We also want to group books into categories based on the general sentiment towards them. For this, we use the Amazon star ratings (1-5 stars, a rating and a review are always given together). We label the books as "good", "bad" or "controversial" as follows:

|  | High average rating | Low average rating |
| :---: | :---: | :---: |
| **Low rating variance** | Good book | Bad book |
| **High rating variance** | Controversial book | Controversial book |

Controversial books can be further separated into positive controversial and negative controversial if necessary. Also, not all books need to go in one of these categories: if some books don't fit in any category, we can assign them to the category "neutral books".

To determine book category, we need the notion of "high" and "low" rating. Since the mean rating distribution is left-skewed, 3 does not represent the average rating in this data. Here we observe the same phenomenon as in movie or video game ratings: most products that make it to publishing aren't objectively too horrible, so mean ratings are quite high. In the context of this data set, an objectively _okay_ book can be _bad_ with respect to the other books. Therefore, we have decided to determine goodness of the mean rating by splitting the data according to percentiles: the ratings below the 25th percentile are _low_, the ratings above the 75th percentile are _high_, and the middle 50 % are _neutral_ (these percentile values are likely to change as we look more into the data).

Grouping by general sentiment is not the only category grouping we have considered. Another grouping could be based on the speed by which the rate of receiving new reviews becomes small. If a book is a "fad", then it will be very popular for a short while and then forgotten, whereas more consistently popular books will receive reviews for a long time. This grouping is based on our observation while data poking: when a book is published, it will receive lots of reviews, and the review rate will slow down at different pace for different books. We will investigate this temporal behavior to see if interesting inferences can be made.

## Review negativity and positivity

We have decided to use the [VADER sentiment analysis toolkit](https://github.com/cjhutto/vaderSentiment) to analyze the positivity and negativity of the text in reviews. VADER gives a compound score between -1 and 1 for a sentence, where -1 is as negative and 1 is as positive as possible. Our interest is to look at the distributions of the compound scores, repeat to them the analysis done on the star ratings, and to see if there are surprising results when connecting them to the ratings.


# Updated research questions

## Intermediate questions on the way to major questions:

+ What ratings indicate that a book is good or bad?
  * Relating rating to sentiment of review

+ Is there a discrepancy between a rating and its associated review's sentiment?
  * Do people give good ratings even though their comment is negative?

## Major questions:

+ How can negativeness or positiveness of reviews and ratings influence the popularity of a book?

+ How do negative and positive reviews and ratings evolve with time?

+ Can we see a snowball effect in reviews and ratings?

## Minor supporting questions:

+ Can we find similar ratings in the reviews of every book of the same author or the same type (genre)?

+ Can we find an interesting progression in the reviews and ratings over time?

+ Can we see emergence or disappearance of people who write highly polarized comments during the time frame?


# A list of internal milestones

## Up until milestone 2:

- [x] Get familiar with the data

- [ ] Learn how to use the IC faculty cluster in order to perform basic statistics
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

Contributions of all group members

ADAM 
Data handling using Spark, Creation of many datasets to be handled without Spark, Data poking and plots drawing to dig into the dataset, Scraping from Amazon for book categories and all analyses using those categories, K-means for books classification, Vader calculations, influence of first ratings analysis, snowball effect analysis. 

TARMO 
Readme file writing and updating, Final books classification into 6 categories, cross-section categories calculations, Data story writing.

NATHALIE 
First naïve way of classifying books into categories, Analyses using classifications for books into good/bad/controversial, Analyses using Vader results, time evolutions analyses. Readme file updating and final comments on notebook. Will work on the final presentation. 

