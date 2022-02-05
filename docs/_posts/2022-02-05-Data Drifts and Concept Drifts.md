---
layout: post
title:  "Data Drifts and Concept drifts"
categories: updates
---


No Model lasts forever , Even if the data quality is fine , the model performance may start degrading in production , indicating that something went wrong ,
Throughout this article , I am going to describe two usual suspects : data drift and concept drift.

## Data Drifts

When we create a machine learning model , we use a set of examples to train it ,and there is always an implicit assumption that the data the model will receive in production will be sufficiently similar to the data that was used in training , but as we can imagine , in reality there is no rules , and in reality the data might change in different ways and for a variety of reasons.


Data Drift means that **the statistical distribution of the input data has been changed**, As a result, the trained model is not relevant for this new data.

It may still perform well on the data that is similar to the "old" one, but it became dramatically less useful when dealing with the new data regions.

<!---excerpt-break-->

### Let's Take a clear example to describe data drifts.

Suppose that you are building a speech recognition system , a system that takes a speech from a user and converts it to a transcript .

After training , testing and pushing the model to production , you noticed that the model performance started to decay , as the audio that is received by the system changed it's properties .

This change in the input data ('the audio') maybe because the language itself changes overtime , or maybe because the users started to use new abbreviations , or maybe because users of new nationalities started to use the application , therefore new accents and new language expressions were introduced.

It's so important to monitor and recognize the difference between the statistical distribution of the features on production and the training data , and if it's proved that the distribution of the input data is sufficiently changed , then model updating should take place.

## Concept Drifts

Concept Drift means that **the relation between the input of the model (X) and the output (Y) has been changed** , so that even if the input data is the same , the output is no longer valid. <br>

Concept drift comes in different flavors.

### Gradual Concept Drift

The relation between X and Y changes incrementally over time. <br>

![Gradual Concept Drift](/docs/mages/data_drifts_and_concept_drifts){:class="img-responsive"}




#### As an example of gradual concept drift


A regression model that predicts the price of houses based on their size and location , after some time of pushing the model to production , the predictions will gradually be no longer valid , because it is logical that houses prices increase over time , so the exact same house (same input) will have a different price (different output ) in 2019 and in 2021 for example.


### Sudden Concept Drift

The relation between X and Y is suddenly changed , and this usually happens because of the global events.

![image tooltip here](/_posts/1.png)


#### As an example of sudden concept drift

When the global pandemic COVID-19 hit , a lot of credit card fraud systems stopped working , because the purchase patterns of the individuals increased massively , as a lot of people started to use much more online shopping , so despite the sudden increase in the purchase pattern of a user, this is no longer an indicator of a 'stolen credit card' , alternatively it can be mapped as 'normal' behavior during the pandemic .


## Let's Sum Up

**Let's admit** , All models degrade. Sometimes, the performance drop is due to low data quality, broken pipelines, or other technical bugs. <br>

If not, you have two more bets:

**1. Data drift**: change in data distributions , so that the model performs worse on unknown data regions.


**2. Concept drift**: change in relationships , the relation between X and Y that the model was trained on is no longer valid.


What really matters is to understand how drifts impact the model performance, if retraining is justified, and how to catch this on time.

