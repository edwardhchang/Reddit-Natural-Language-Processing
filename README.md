# Project 3: Web APIs & NLP

## Problem Statement

Reddit HQ has transferred over all their data from local servers to cloud servers and in the process, accidentally lost a lot of meta-data associated with past reddit posts. They lost the subreddit tags for posts belonging to r/AmItheAsshole and r/offmychest. Usually it is easy to identify the posts as r/AmItheAsshole posts generally include a question of 'Am I the asshole?'included in their text. This question is usually posed in the form of an acronym: 'AITA'. However, in moving servers they also had these posts scrubbed of the words, 'asshole' and 'AITA'. Reddit's Damage Control has reached out to see if we can build a model that can classify posts to either r/AmItheAsshole or r/offmychest in the exclusion of the words mentioned prior. Our baseline will be a 50/50 split of the subreddit posts and we will use the accuracy score as our evaluation metric. 

## Data and Data Dictionary

The data is pulled from Reddit using the Pushshift API, where it is read in as JSON-format and converted to a dataframe. We initally pull the 'id', 'title', 'author', 'created_utc', 'subreddit', and 'selftext' from the 'data'. Our final cleaned data is comprised of two features which are described below:

|Feature|Type|Dataset|Description|
|-------|-------|-------|--------|
|Subreddit|*Integer*|clean_data.csv|Target variable, 0 for r/AmItheAsshole, 1 for r/offmychest|
|Text|*Object*|clean_data.csv|Combined title and selftext|



## Executive Summary

In order to get our data, we used the Pushshift API (https://github.com/pushshift/api)  to pull from each subreddit. Since there is a limit on the number of posts we can get with a single request (100), we wrote a function to pull posts a certain number of iterations. Using this, we were able to create a dataframe with the post id, title, author, created_utc (post time), subreddit, and selftext (which refers to the actual text of the post). With this, we had 2200 posts from r/AmItheAsshole and 2100 posts from r/offmychest before we shipped it off to be cleaned and pre-processed. Altogether, there were 4300 posts, which were concatenated together.

To begin our cleaning, we dropped any unneeded columns, removed duplicate posts by sorting by duplicate titles and authors. We removed any posts with selftext set a NaN as well as any posts with selftext set as '[removed]'. We set our target (subreddit) as binary values (0 for r/AmItheAsshole, 1 for r/offmychest). By the time our data was ready to be exported and used for modeling, we had 2672 posts to use in our classification model which was split evenly between the two subreddits. In cleaning our text, we wanted to remove any non-letter characters such as "?" and removed links. We set all the text to lower case and removed numbers. We then tokenized the words and filtered out any stopwords. We then lemmatized these filtwered words, which we all returned in a new column for 'clean' text. We used a count vectorizer to find the most frequent words in each subreddit and added shared frequent terms to our list of stopwords which we will use in our model instantiations. 

Our models that we tested out using our cleaned text were:
   - Logistic Regression
   - Support Vector Classifier
   - Random Forest
   - Random Forest with Tfidf Vectorizer
   
For each of these models, we ran them through a grid search with variations in our hyperparameters to tune our models. We then made confusion matrices for our training and testing data to evaluate our models. Our random forest that was tuned ended up performing the best with an accuracy score of 87.5% on unseen data. With the training data, the accuracy score was 99.7% which indicates that our model is very overfit. The best parameters were 100 max trees being used with the minimum samples split being 16. The max feature was set to 'auto' and the max depth was set to 'none'. Our count vectorizer was limited to 1000 max features which was fed into our Random Forest model. Our random forest model was overfit, but it appeared to perform the best in terms of our problem statement. We initially believed the Random Forest model would assist with reducing variance, due to the random subselection of features but it appeared to follow the same trend we saw with a lot of our other models. 

## Conclusions and Recommendations

Our best model was the random forest model that was hypertuned with the above parameters. It performed significantly better than our baseline model with has an accuracy of 50%. Our model can classify a post based on its title and text while being 87.5% accurate. Reddit HQ can use this model to re-label the subreddits. For future recommendations, we would recommend possibly using a Multinomial Naive Bayes model as it works better when the features are 'counts' whereas random forest works well with multiple types of data. We would also recommend more time in engineering the features (word vectors) and the different ranges of words such as trigrams, or introducing a stemmer as opposed to a lemmatizer. Post comments added as a feature in our model would also benefit our model as we would have more data to draw from. 

## Sources

1. Pushshift API: https://github.com/pushshift/api
2. Reddit, r/offmychest: https://www.reddit.com/r/offmychest/
3. Reddit, r/AmItheAsshole: https://www.reddit.com/r/AmItheAsshole/