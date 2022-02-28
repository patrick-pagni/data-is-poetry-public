<style>
    img {
        display: block;
        margin-left: auto;
        margin-right: auto;
    }
</style>

<img src = 'https://user-images.githubusercontent.com/69991618/111783988-c2483380-88b2-11eb-806b-6e2bbd495298.png' width = 100 alt = 'GA'>

# Data is like poetry: predicting a poet's sex, continent of origin and dates based on their poetry

## Overview

This project was completed as part of the General Assembly Data Science Immersive course.

This document outlines the problem, hypothesis, methodology, conclusion and tools used.

-----
-----

## Repository Contents

- Technical Report:
  - Executive Summary
  - Notebooks:
    - Data Collection
    - Data Cleaning
    - EDA
    - Model Evaluation
  - Models:
    - Dates Ridge regression
    - Sex Logistic Regression
    - Continent Logistic Regression
- Read Me

*n.b. datasets are not included.*

-----
-----

## Problem Statement

Everyone has their own unique way of using language. When I think, speak and write I am using English, but I am specifically using my English. But I don't exist in a vacuum -- the way I use language is influenced by my circumstances: where I live, who my friends are, the time I was born. You can learn a lot about me from the way I use language.

The aim of this model is to use data to explore this relationship and understand just how much we can tell about someone from how they use language.

-----
-----

## Goals

This project had three goals. Taking a poem as an input, use NLP and feature engineering to build 3 models to predict the following details about its author:

- Sex
- Continent of origin *(originally country_of_origin)*
- Dates

To predict the sex and continent of origin of the poet, I have built 2 classification models. To predict each poet's dates, I have built a regression model.

-----
-----

## Data Source

[Kaggle - Poetry Foundation](https://www.kaggle.com/tgdivy/poetry-foundation-poems/version/1)

The linked dataset contains 14,000 poems from Poetry Foundation.

All poems are labeled with the title, author and poetry foundation tags attached to them. This formed the spine of my final dataset.

Since the above dataset does not contain the target variables I am looking for, I used the poet column to compile a list of the poet's within the dataset. Then, using a combination of scraping wikipedia, probabilistic analysis based on first names and manual input, I labeled the rows with the target variables.

-----
-----

## Cleaning & Processing

As mentioned above, the spine of the dataset did not include the target variables I wanted to predict. Therefore data cleaning and processing requirements centred principally around the following:

- Preparing target variables using web scrapes and probabilistic analysis of first names
- Removing html tags from title and poem columns
- Dummifying tag list to get usable tag columns
- Developing NLP regex token to capture words and punctuation

-----
-----

## Exploratory Data Analysis

The EDA process lef me to draw the following conclusions from the dataset:

1. The class imbalance in the `country_of_origin` columns for this dataset has led me to aggregate the countries by their continent and use this as my target variable instead.
2. There is moderate class imbalance within the `sex` target column, with male being the largest class. This imbalance is not so drastic that I cannot build my model using this target variable.
3. The `dates` column has a negative skew, with most values falling between 1900 and 2000.
4. After investigating several outliers for the `dates` column, I can confirm they are legitimate values and will be kept in the dataset.
5. The `number_of_lines` and `number_of_stanzas` columns are both positively skewed, and are heavily dominated by lower values - this means legitimate higher values are being flagged as outliers.
6. After investigating several of these outlier I can confirm they are legitimate recordings and will remain within the dataset.



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
