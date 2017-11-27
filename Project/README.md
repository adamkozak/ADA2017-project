# Analysis of reviews of books on Amazon

# Abstract
The goal of the project is to analyze the reviews of books in the [Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/).
Three exact goals are considered. The first goal idea is to analyze whether a pattern
in reviews (length of review, number of reviews) can be linked to a particular author,
author group or genre. The second goal is to analyze the tone and the emotions carried by the
comments and to see if certain authors, groups or genres attract certain emotions. The analysis
will probably be realized using external emotion recognition tools for text. The third goal
is to relate the emotion analysis to time, to see whether review tones change over time and if the
increase in popularity and accessibility of the internet has contributed to changes in commenter
behavior or polarization of comments. The story we would like to tell is to show something interesting
about Amazon comments from the 1990’s to the 2010’s, motivated by our interest in behavior of people
on the internet.




# Research questions

+ Can we find a similar pattern in the reviews of every book of the same author or the same type?

+ Can we find similar emotions in the reviews of every book of the same author or the same type?

+ Can we find an interesting progression in the reviews over time?

+ Can we see emergence or disappearance of people who write highly polarized comments during the time frame?




# Dataset

The dataset we will use is the [Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/), and as it is very big we
will only use the reviews related to the “Books” category. The dataset will still be
very big (more than 140 million reviews in total, of which a significant portion are book
reviews) so we will use [Apache™ Hadoop®](http://hadoop.apache.org/), [Apache Spark™](https://spark.apache.org/) (or similar) to handle it on the computing cluster. The data
is in JSON format so it should be easy to handle (not strict JSON but can be read with Python).
Nothing is said about which country the reviews come from. This is important information for analysis,
so in the absence of exact information we will assume that the data is from Amazon in the United
States (since it is hosted on the University of California San Diego website, the data includes
reviews from the 1990’s, and it is not stated otherwise in the data description). In any case,
for the analysis we assume that (nearly) all comments are in English.
For the first two analyses we will need to use the text of the review and the ID of the product
(book) reviewed and for the last one we will need to use the date of the review and also the ID
of the reviewer (to analyse the time evolution of the reviews for a particular user).

# Milestone 2

We have chosen our main questions (for now) to be:

_How can negativeness or positiveness of comments and ratings influence the popularity of a book? How do negative and positive comments and ratings evolve with time?_

To this end, we need to define the "popularity" of a book.
We decided on two different definitions for a popularity metric:

1. Time-independent popularity: the number of reviews at the end of the dataset time period (July 2014)
2. Time-dependent popularity: the number of reviews at the end of the dataset time period, divided by the number of months between the first review in the dataset and the end of the dataset period

The metrics are based on the idea that a "popular" book will accrue lots of reviews, and an "unpopular" one will not.
Metric 1 just looks at the number of reviews gathered between May 1996 and July 2014, and therefore favors older books that were pubished earlier (since they will hav ehad more time to accrue reviews.) By contrast, metric 2 favors newer books, since the rate of commenting on books on Amazon is higher the closer we are to the present day in time (because of internet accessibility, etc.).

We also want to group books based on the general sentiment towards them. For this, we use the Amazon star ratings (1-5, rating and review are always given together). We label the books as "good", "bad" or "controversial" as follows:

|  | High average rating | Low average rating |
| :---: | :---: | :---: |
| **Low rating variance ** | Good book | Bad book |
| **High rating variance** | Controversial book | Controversial book |

Controversial books can be further separated into positive controversial and negative controversial if necessary.





# A list of internal milestones

## Up until milestone 2:

- [x] Get familiar with the data

- [ ] Learn how to use the IC faculty cluster in order to perform basic statistics

- [x] Learn the basics of Hadoop/Spark/the best tool for the task

- [ ] Find the best frameworks for text processing/sentiment analysis

- [x] After getting familiar with the data, try to pick the project goal or
goals that are the most feasible and have the possibility of generating
interesting results

- [x] Create a IPython notebook for the project pipeline

- [ ] Access the dataset and perform the necessary statistics tasks

- [ ] Update the ReadMe description according to decisions made in the course of data analysis

- [ ] Organize the work/create a roadmap for next milestones (report/data story and presentation)

## Up until milestone 3:
- [ ] TBD


# Questions for TAs
+ What would be the best tools for handling and analyzing the Amazon data set?

+ What are the best frameworks for text processing?

+ Is it possible to use [Elasticsearch](https://www.elastic.co/products/elasticsearch) on the computing cluster or somehow connect it to the dataset on the cluster? Elasticsearch provides a lot of useful features for text processing (e.g. tokenizer, keyword/pattern/stop analyzer).
