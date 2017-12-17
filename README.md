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

+ How can we classify books into good, bad, and those for which there is no clear consensus?

The dataset we examine is the [5-core subset](https://en.wikipedia.org/wiki/Degeneracy_(graph_theory)) of [Julian McAuley's Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/), which spans reviews from May 1996 to July 2014. The data contains 8,898,041 reviews of 367,982 books, and each review has an associated star rating (1-5 stars). An example review looks like this:

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

To see what kind of sentiments people express in their reviews, we use [VADER sentiment analysis](https://github.com/cjhutto/vaderSentiment), which gives a compound score of a review on the positive-negative axis. The higher the score, the more positive the review, with -1 being as negative and +1 as positive as possible. Let's see the mean VADER score distribution:

![VADER mean](/Project/images/Distribution_mean_Vader_compound_score_book.png)

Looks pretty similar to the distribution of ratings. To really see if there is a difference between a rating and its associated review's sentiment, we scale the ratings to the same \[-1,+1\]-range as the VADER score, and calculate the difference between the rating and the sentiment:

![VADER differences](/Project/images/Distribution_difference_mean_ratings_mean_Vader_compound_books.png)

Excellent, most of the mass of the distribution is around zero, meaning there is little difference! Seems like people actually give a rating that accurately reflects the sentiment in their review. **This means that we can use the star ratings as a measure of a reviewer's sentiment towards a book.**

Now that the rating-sentiment relationship is established, we can move to classifying books as good, bad or controversial.

# Goodness and controversiality of books

Since most ratings are high in terms of numerical value, an absolute threshold for saying which books are good and which are bad is not sensible. Instead, we define bad books as the books with mean rating in the bottom 25 % of mean ratings, neutral books as the books with mean rating in the middle 50 % of mean ratings, and good books as the books with mean rating in the top 25 % of mean ratings. Therefore, goodness or badness is context-dependent, and books we say are bad or good are bad or good in this dataset's context.

Now, we ask the question: _do people agree on which books are good and which are bad?_ To answer this question, we create another classification — controversial, neutral, or uncontroversial. Here, we use the standard deviation of ratings, and classify the lowest 25 % as uncontroversial, middle 50 % as neutral and top 25 % as controversial. The idea is that the more variation there is among the ratings for a given book, the more people are in disagreement over the book's goodness.

Since mean and especially variance can vary wildly if there are only a few data points, **we classify only books with ratings from ten or more people**. This reduces noise and exposes actual trends. After classification, we find the number of books in different classes:

|             | **Uncontroversial** | **Neutral** | **Controversial** | _Total_   |
|-------------|---------------------|-------------|-------------------|-----------|
| **Good**    | 37761               | 11610       | 156               | 49527     |
| **Neutral** | 10801               | 69502       | 15415             | 95718     |
| **Bad**     | 566                 | 17142       | 33558             | 51266     |
| _Total_     | 49128               | 98254       | 49129             | 196511    |

_Out of the 367,982 books, 196,511 had at least 10 ratings, and the remaining 171,471 books had 9 or fewer and thus were not classified. The numbers of good and bad books or controversial or uncontroversial books are not exactly equal, because there are several books with mean rating or standard deviation exactly equal to the classification threshold values._

Most good books are also uncontroversial, and most bad books are controversial. Partly this is due to the skewness in mean ratings, but the difference is still quite striking.

# Popularity of books

The popularity of a book is related to the amount of interest people show towards it. We decide to define popularity as the number of reviews a book accrues: the more popular the book, the more it is reviewed, and vice versa. But first, how many reviews are given in general during the time frame of the dataset? Let's look at the number of reviews written per month:

![Reviews per month](/Project/images/reviews_per_month_crop.png)

The vast majority of the reviews are written at the end of the time frame, from around January 2011 to July 2014. This is the most interesting area, since most of the overall popularity of a book is generated there. How does this timeframe look for our book classifications?

![Reviews per month per category](/Project/images/number_of_reviews_per_month_for_each_category_crop.png)

Surprisingly, uncontroversial books gain more popularity than controversial ones in each goodness category throughout the time frame! Good books that are uncontroversial or controversy-neutral gain the most reviews, and bad books gain fewer reviews. _But, if a book is good and controversial, it will have variation in review numbers and eventually few reviews_. Answering our first research question, _How can negativeness or positiveness of reviews and ratings influence the popularity of a book?_, we can say that good books are popular, but consistently so only if they are uncontroversial or controversy-neutral. If book reviews agree with each other, a book is more popular than if there is disagreement in reviews, and this effect is stronger than the actual positivity or negativity of the reviews!

# Time evolution of high and low reviews

```(time evolution image)```

# The snowball effect

We examine whether there is a discernible snowball effect in the ratings, i.e. do the first ratings dictate the final mean rating score of a book. In order to filter out noise, we only look at the books with 100 or more reviews. The mean of all ratings for a book against the mean of the first 25 ratings made on the book paints a clear picture:

```(snowball image)```

The snowball effect is clear: the mean rating increases as a function of the mean of the first 25 ratings. Correlation does not imply causation, but this correlation tells us that if the mean of the first 25 ratings is high, the overall mean rating is high as well. For low first ratings, the effect is less pronounced and there is more variation — bad intial ratings can coexist with a good overall rating.
