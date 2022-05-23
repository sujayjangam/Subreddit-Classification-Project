# Subreddit Classification Problem

## Overview

Lately the popularity of low carbohydrate diets has grown. There is clear evidence backing it up, and show that it promotes weight loss, such as [this article from Harvard](https://www.hsph.harvard.edu/nutritionsource/carbohydrates/low-carbohydrate-diets/#:~:text=Research%20shows%20that%20a%20moderately,selections%20come%20from%20healthy%20sources.&text=More%20evidence%20of%20the%20heart,for%20Heart%20Health%20(OmniHeart)).<br>
On a website known as Reddit, there are many subcommunities, known as subreddits, that discuss questions, advice, progress, and even frustrations around Nutrition and the many different diet styles. There are those dedicated to low/no carbohydrates as well. Within each subreddit, users can create text or image posts, and upvote or downvote posts to express approval or disapproval regarding the content of the post. The number of upvotes and downvotes are fed into a hot-ranking algorithm to determine a score for the post, with higher scoring posts rising to the top of the subreddit.

---

## Problem Statement

Given the rise in popularity of low/no carb diets, clients at MyNutrition Clinic have been requesting to be put on either a [Ketogenic](https://www.healthline.com/nutrition/ketogenic-diet-101) or a [Zerocarb (carnivore)](https://www.healthline.com/nutrition/carnivore-diet) diet. *(Some resources describing what these diets are have been linked)*. <br>
<p>This is based on the client's "I want to lose weight quickly" mentality, as well as their own additional research.
However we are concerned that this may result in "yo-yo dieting", where the client rebounds from the diet, either by quitting the diet because its too difficult, or ending the diet because they reached their short term goal, or by losing control and binge eating.
    
This rebound ends our clients up in a worse shape than when they started. Our team wants to build a classification model to classify our clients into each group based on subreddit data, and minimize false positives especially when it comes to the `zerocarb` group.
    
For this problem statement, we will be focusing only on subreddit post title, and post text for modeling.

---

### Datasets

#### Data used

There were two datasets used, both were scraped from reddit using the PushShift API.
A total of 10_000 posts were scraped from each subreddit.

* [`keto_subreddit.csv`](datasets/keto_subreddit.csv): Scraped Dataset from `r/keto` subreddit
* [`keto_cleaned.csv`](datasets/keto_cleaned.csv): Cleaned data for the scraped `r/keto` subreddit
* [`zerocarb_subreddit.csv`](datasets/zerocarb_subreddit.csv): Scraped Dataset from `r/zerocarb` subreddit   
* [`zerocarb_cleaned.csv`](datasets/zerocarb_cleaned.csv): Cleaned data for the scraped `r/zerocarb` subreddit 
* [`custom_stop_words.csv`](datasets/custom_stop_words.csv): Custom stop words created, to be shared across notebooks
* [`merged_for_modeling.csv`](datasets/merged_for_modeling.csv): Merged dataset to use for modeling
* [`model_scores.csv`](datasets/model_scores.csv): Saved model scores for further analysis.

---

### Brief Summary of Analysis

#### Data Scraping:
1. I created a custom class to help pull the data from the subreddit. I noticed that I used to get errors like connection errors and some others, which caused all progress to be lost. This avoided with the use of a class.
2. The class checked for null values, removed, deleted posts, as well as whether the post fit my criteria of not having any media.
    
    
#### Cleaning:
1. There were no null values, or missing values due to the use of the class mentioned prevoiusly. 
2. We wrote custom functions to carry out preprocessing on the text columns to ensure special characters, urls etc were removed.
3. Lastly I converted the `created_utc` column to a `pandas datetime` column.


#### EDA on Subreddit Data
1. Once we had preprocessed our data, we carried out some EDA. 
2. We saw that although `r/keto` had 30 times as many subscribers as `r/zerocarb`, the subcribers of `r/zerocarb` were engaging more with each post. Later on we found that this was due to the post volume for each subreddit.
3. Additionally we found that the moderators for `r/keto` were much more active than those for `r/zerocarb`.
4. By looking at the top trigrams, we found that on the `r/keto` subreddit, the users ask a lot more questions, and they are trying to find out more information about the ketogenic diet. On the `r/zerocar` subreddit however, it seems that users are talking about specific foods, and even cooking methods. This indicates to me that most users on `r/zerocarb` already know what they are doing in terms of their diet, however on `r/keto`, there may be many more newbies. 

#### Modeling:
1. We picked 3 models for our modeling process: Logistic Regression, Multinomial Naive Bayes, Random Forest Trees.
2. Our main metric we wanted to optimize for was `precision` and `accuracy`. After many iterations of models, we found that the `LogisticRegression` model with `TfidfVectorizer` worked the best and gave us the best scores. 

---

### Model Limitations, Improvements and Conclusions:
    
#### Model Limitations
1. Our model is able to grasp mentions of 'bone broth', 'meat', 'ground beef' and other similar words. Mentions of improved health, e.d. reduced blood pressure also end up classified as `zerocarb`.
2. However our model is thrown off by mentions of animal organs and the word bone specifically. The context of the words can be different, and they might be relevant to both `keto` and `zerocarb`, however as long as the post contains these words, it will most likely be classfied as `zerocarb`. 
3. Even though both `r/keto` and `r/zerocarb` are both very similar in the sense that they are both proponents of high protein, high fat, and low/no carb diets and lifestyle, the `r/zerocarb` definitely has a much larger focus on meat(especially beef), animal organs, and bones (bone broth).

#### Model Improvements:
It is in our interest to reduce False Positives as much as possible, as such, we would prefer to maximize the `precision` score of the model as much as possible.<br>
1. One way we can do this is test other models such as support vector machines, and perhaps deep learning models as well.
2. Another way we can do this is to adjust the threshold for the classification of `zerocarb`, making this an imbalanced classification. This will require further investigation as we continue to improve the model. <br>
3. Lastly, we can include other features in our model, other than just text, sentiment analysis for example, could possible improve the classification score.

    
#### Recommendations: 
1. Our current model performs 2% better than our baseline model on accuracy. As such we would reccommend using this model for classification. 
2. For those model probabilities where the classification is boderline, e.g. 51% vs 49%, we would recommend classifiying those as `keto`, as it is easier to make the jump from `keto` to `zerocarb` if the client still wants to do so.
3. Lastly, we would also recommend reviewing those clients within one month of being put on the `zerocarb` diet. If we review them within one month and find that the diet is not working, it might not be too late to move them to `keto` diets and prevent a rebound from happening.
