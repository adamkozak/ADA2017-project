**_By: Nathalie Crevoisier, Adam Kozak, Tarmo Nurmi_**

_This data story is from the authors' course project for the [Applied Data Analysis](https://dlab.epfl.ch/teaching/fall2017/cs401/) course at the [École Polytechnique Fédérale de Lausanne](https://www.epfl.ch/), from Fall of 2017._

# Introduction

User reviews on [Amazon.com](https://www.amazon.com/) are a source of interesting insight into people's behavior on the internet. We decided to take a look into book reviews on Amazon over a period of time, to find answers to questions that interest us:

**Research questions:**

+ How can negativeness or positiveness of reviews and ratings influence the popularity of a book?

+ How do negative and positive reviews and ratings evolve with time?

+ Can we see a snowball effect in reviews and ratings?

Before answering these questions, we need to examine the relationship between a text _review_ and its associated _rating_, define the notion of 'good' and 'bad' books, and define what it means for a book to be 'popular':

**Intermediate questions:**

+ Is there a discrepancy between a rating and its associated review's sentiment?
  * Do people give good ratings even though their comment is negative?

+ What ratings indicate that a book is good or bad?
  * Relating _rating_ (stars given) to sentiment of _review_ (textual evaluation)

The dataset we examine is the [5-core subset](https://en.wikipedia.org/wiki/Degeneracy_(graph_theory)) of [Julian McAuley's Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/). The data contains 8,898,041 reviews of 367,982 books, and each review has an associated star rating (1-5 stars). An example review looks like this:

```
{ 
"reviewerID": "A2SUAM1J3GNN3B",
"asin": "0000013714",
"reviewerName": "J. McDonald",
"helpful": [2, 3],
"reviewText": "I bought this for my husband who plays the piano. 
He is having a wonderful time playing these old hymns. 
The music is at times hard to read because we think the book was 
published for singing from more than playing from. 
Great purchase though!",
"overall": 5.0,
"summary": "Heavenly Highway Hymns",
"unixReviewTime": 1252800000,
"reviewTime": "09 13, 2009" 
}
```

Immediately we notice that the identity of a book is given as the [Amazon Standard Identification Number, ASIN](https://www.amazon.com/gp/seller/asin-upc-isbn-info.html). For a book this is the same number as its ISBN. Since actual book titles are nicer than numbers, we enrich the data by scraping the Amazon website for titles associated with ASINs, as well as more information on the book (genres, categories, etc.).

# Ratings, reviews and sentiment

Now that introductions are out of the way, let's take a look at the distribution of ratings in the data:

![Mean ratings](/Project/images/mean_rating.png)

Wow, that looks pretty heavily left-skewed! Apparently, reviewers are giving significantly more high than low ratings. Does this distribution really represent the sentiment of reviewers, or are people just not inclined to give low numerical scores even though they might bash the book in the review text?

To see what kind of sentiments people express in their reviews, we use [VADER sentiment analysis](https://github.com/cjhutto/vaderSentiment), which gives a compound score of a review on the positive-negative axis. The higher the score, the more positive the review, with -1 being as negative and +1 as positive as possible.

```(Image of VADER compound scores)```

Looks pretty similar to the distribution of ratings. To really see if there is a difference between a rating and its associated review's sentiment, we scale the ratings to the same \[-1,1\]-range as the VADER score, and calculate the difference between the rating and the sentiment:

```(Image of differences)```

Excellent, most of the mass of the distribution is around zero, meaning there is little difference! Seems like people actually give a rating that accurately reflects the sentiment in their review. **This means that we can use the star ratings as a measure of a reviewer's sentiment towards a book.**

Now that the rating-sentiment relationship is established, we can move to classifying books as good, bad or controversial.

# Goodness and controversiality of books

Since most ratings are high in terms of numerical value, an absolute threshold for saying which books are good and which are bad is not sensible. Instead, we define bad books as the books with mean rating in the bottom 25 % of mean ratings, neutral books as the books with mean rating in the middle 50 % of mean ratings, and good books as the books with mean rating in the top 25 % of mean ratings. Therefore, goodness or badness is context-dependent, and books we say are bad or good are bad or good in this dataset's context.

Now, we ask the question: _do people agree on which books are good and which are bad?_ To answer this question, we create another classification — controversial, neutral, or uncontroversial. Here, we use the standard deviation of ratings, and classify the lowest 25 % as uncontroversial, middle 50 % as neutral and top 25 % as controversial. The idea is that the more variation there is among the ratings for a given book, the more people are in disagreement over the book's goodness.

Since mean and especially variance can vary wildly if there are only a few data points, **we classify only books with ratings from ten or more people**. This reduces noise and exposes actual trends. After classification, we find the number of books in different classes:

|             | **Uncontroversial** | **Neutral** | **Controversial** | Total |
|-------------|---------------------|-------------|-------------------|-----------|
| **Good**    | 37761               | 11610       | 156               | 49527     |
| **Neutral** | 10801               | 69502       | 15415             | 95718     |
| **Bad**     | 566                 | 17142       | 33558             | 51266     |
| Total   | 49128               | 98254       | 49129             | 196511    |

# Popularity of books








# Analysis of reviews of books on Amazon

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
  * Not fulfilled, since for now running Spark locally is sufficient

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

- [ ] Decide all models of grouping data into categories (negative/positive, fast/slow plateauing of review numbers, etc.)

- [ ] Decide parameters and thresholds for category definitions based on the data

- [ ] Enrich data by scraping Amazon for genres as extensively as possible

- [ ] Create necessary intermediate files containing formatted data, to speed up analysis and computing

- [ ] Find the best way to use VADER to analyze positivity and negativity of reviews

- [ ] Calculate correlations between popularity metrics and positive and negative reviews and ratings
  * With respect to time
  * By aggregation by category (good, bad, controversial)
  * By aggregation by genre and author

- [ ] If necessary, run analysis on the cluster

- [ ] Find the exact data story we want to tell (choose exact questions), make it compelling, and link it to social good

- [ ] Decide format of report

- [ ] Depending on report format, distribute tasks to individual group members

- [ ] Produce good, polished visualizations that tell the story

- [ ] Construct report into a coherent document

- [ ] Update readme according to progress

- [ ] Create a plan for presentation and poster creation
