# Analysis of reviews of books on Amazon

# Abstract
The goal of the project is to analyze the reviews of books in the Amazon dataset.
Three exact goals are considered. The first goal idea is to analyze whether a pattern
in reviews (length of review, number of reviews) can be linked to a particular author,
author group or genre. The second goal is to analyze the tone and the emotions carried by the
comments and to see if certain authors, groups or genres attract certain emotions. The analysis
will probably be realized using an external emotion recognition tools for text. The third goal
is to relate the emotion analysis to time, to see whether review tones change over time and if the
increase in popularity and accessibility of the internet has contributed to changes in commenter
behavior or polarization of comments. The story we would like to tell is to show something interesting
about Amazon comments from the 1990’s to the 2010’s, motivated by our interest in behavior of people
on the internet.




# Research questions

Can we find a similar pattern in the reviews of every book of the same author or the same type?
Can we find similar emotions in the reviews of every book of the same author or the same type? 
Can we find an interesting progression in the reviews over time?
Can we see emergence or disappearance of people who write highly polarized comments during the time frame?




# Dataset

The dataset we will use is the Amazon dataset, and as it is very big we
will only use the reviews related to the “Books” category. The dataset will still be
very big (more than 140 million reviews in total, of which a significant portion are book
reviews) so we will use Hadoop (or similar) to handle it on the computing cluster. The data
is in JSON format so it should be easy to handle (not strict JSON but can be read with Python). 
Nothing is said about where the reviews come from. This is important information for analysis,
so in the absence of exact information we will assume that the data is from Amazon in the United
States (since it is hosted on the University of California San Diego website, the data includes
reviews from the 1990’s, and it is not stated otherwise in the data description). In any case,
for the analysis we assume that (nearly) all comments are in English. 
For the first two analyses we will need to use the text of the review and the ID of the product
(book) reviewed and for the last one we will need to use the date of the review and also the ID
of the reviewer (to analyse the time evolution of the reviews for a particular user). 


# A list of internal milestones up until project milestone 2

Add here a sketch of your planning for the next project milestone.



# Questions for TAa

Add here some questions you have for us, in general or project-specific.
