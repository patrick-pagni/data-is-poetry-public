# Executive Summary

## Goals

This project had three goals: using NLP can I build 3 separate models to predict a poet's sex, country  of origin and dates.

The models to predict the sex and country of origin for a poet are classification models. The model predicting a poet's dates is a regression model, specifically a random forest regressor.

-----
-----

## Metrics

I measured the success of the classification models by looking at the accuracy score of each model and the AUC-ROC curve. I also look at the precision and recall scores for these models.

For the regression model, I have assessed the model by looking at the r-squared score. This will tell me the percentage of the variance that is  explainable by the model. I have also created a quantile plot to assess the model.

-----
-----

## Findings

### Sex

For the model designed to predict the sex of the poet, I found I was able to build a model that predicted the sex of the poet approximately 3% more accurately than by simply predicting the largest group every time: male. Unsurprisingly, this yielded a model that was very good at correctly classifying male poets, but was less effective at identifying female poets with the majority of misclassifications being poems written by female poets that were classified as having been written by male poets.

The following condition will undermine the models predictive power of the sex of a poet:

- The sex for many poets was determined using a proabalistic analysis of their first names, assigning sex based on whether their first name more likely belongs ot male or female
  - This is not a foolproof way of corrcetly assigning the sex to a poet, firstly there are many names which are unisex, such as Alex. This method of categorisation may have mislabeled some poets.
  - Some female poets deliberately choose a male pen name, especially historically for commercial reasons. An example of this is George Eliot. My algorithm would have assigned male to her, even though she is known to be a female poet.
  - To mitigate this, I scraped the wikipedia pages for all poets with a wiki and used the pronouns used to assign a sex to them. Then for those without a wiki, I used the probablistic analysis.

Verifying the cleanliness of the data will allow us to vouchsafe the results of the model, and ensure the outputs we are receiving aren't warped by an imperfect dataset.

Nevertheless, this model still predicts more accurately than the baseline.

-----

### Continent

The accuracy scores for this model show it is better at predicting where a poem originates than just predicting the largest class every time.

This model is very good at classifying poems from `north_america`, and is good at classifying poems from `europe`. It is, however, very poor at classifying poems from the rest of the world. This is unsurprising given the class imbalance within the dataset: only 6% of the poems within the dataset are from the rest of the world. The dominance of the other two classes makes it very unlikely the model will select a poem from the rest of the world.

The following condition is likely to undermine the model's ability to predict the continent of origin accurately (also listed in the executive summary):

- Translated poems may cloud the data set.
  - A poem originating from Germany for example will be said to have originated in Europe. If it was translated by someone from North America, however, the language used is more likely to reflect that used in North America rather than Europe.
  - This may apply to poems from the rest iof the world which may be able to be absorbed into categories.

For a more accurate model, it would be good to identify translated poems and find out who the translator is to better label where the English used comes from.

Despite this caveat, the model still is a better predictor than simply predicting the baseline prediction every time.

-----

### Dates

The cross-validation score, and test score are both approximately 0.56. This means the model explain 56% of the variance within the dataset. This is considerably better than the baseline prediction.

The intercept for the model is approximately 1913. This means that when all inputs are 0, the model will still predict a meaningful value, which is not far from the mean.

The model, however, violates two assumptions for linear regression models: the assumption of homoscedasticity (that the variance in residuals for every lavel of x is constant) and the assumption that the distribution of residuals is normal. To counter this, I would transform the the dates column by taking the log of it. I would then rerun the model to check if this has solved the problem.

It is important to flag the following limitation for this model as well:

- Translated poems may cloud the data set.
  - If a poem from the 1300s was translated in 1920, the poem is more likely to reflect the language usage of 1920s rather than the 1300s.

I think confirming the translation dates for poems which have been translated would also improve the model.

-----
-----

## Risks, limitations and assumptions

- The sex for many poets was determined using a proabalistic analysis of their first names, assigning sex based on whether their first name more likely belongs ot male or female
  - This is not a foolproof way of corrcetly assigning the sex to a poet, firstly there are many names which are unisex, such as Alex. This method of categorisation may have mislabeled some poets.
  - Some female poets deliberately choose a male pen name, especially historically for commercial reasons. An example of this is George Eliot. My algorithm would have assigned male to her, even though she is known to be a female poet.
  - To mitigate this, I scraped the wikipedia pages for all poets with a wiki and used the pronouns used to assign a sex to them. Then for those without a wiki, I used the probablistic analysis.

- All poets in the dataset are the only person with their name with a wikipedia page.
  - This assumes that for a given poet 'T.S. Eliot,' there is only one wikipedia page for the poet, and the url of this page is given by the base wiki url followed by the name.
  - This assumption is dangerous because we may have read data from other people into the dataset, meaning some poets may have been labeled with other people's data.

- Translated poems may cloud the data set.
  - A poem originating from Germany for example will be said to have originated in Europe. If it was translated by someone from North America, however, the language used is more likely to reflect that used in North America rather than Europe.
  - Equally, if a poem from the 1300s was translated in 1920, the poem is more likely to reflect the language usage of 1920s rather than the 1300s.
  - There may still be thematic differences that the model can pick up in the language used in different parts of the world and across different languages however, although this may be difficult to interpret within the model.
