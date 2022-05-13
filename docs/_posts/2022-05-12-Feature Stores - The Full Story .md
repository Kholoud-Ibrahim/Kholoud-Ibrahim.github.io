The whole story started when The feature store concept was introduced by Uber in 2017 with the launch of their [Michelangelo platform](https://eng.uber.com/michelangelo-machine-learning-platform/) ,  its essentially a purpose-built data platform for machine learning features.

fast forward , and in less than two years Hopsworks and Feast, two open-source feature store projects were launched. then Tech giants such as Google (Vertex AI), Amazon (SageMaker), and Databricks were also quick to propose their own fully-managed feature store platforms.
<img src="/docs/images/feature_stores/timeline.png"  width="800" />


## What is a feature store 

As a starting point ,to be able to answer this question , we need to primarily know **what is a feature ?!**

**A Feature** is any measurable input that can be used in a predictive model — it could be the color of an object or the sound of someone’s voice or a height of a person ...etc .

For example if we are building a predictive model for credit card fraud detection , features can be the total number of transactions , the total amount of the transactions , the time elapsed between each two transactions , and these features will feed the model to be able to predict whether a credit card is fraudlent or not.

**A Feature Store**  is a platform where all features are stored, centralized, accessible to everyone for the explicit purpose of being used to either train models or make predictions .
<img src="/docs/images/feature_stores/overview.png"  width="800" />

## Why do we need a feature store ?

**1.Reuseability** : That's the key word and the main purpose of a feature store , its simply the capability of reusing features across different machine learning models within the organization. <br>  

If you don’t reuse features across different models, you will end up with a new feature pipeline for each and every new model you put in production ! <br>   
Feature store prevents reinventing the wheel and developing a new data pipeline for each machine learning model , by enabling  the reuseability of the already existing features withing the store.
This way it massively reduces the number of the data pipelines and limits the duplication of work.

**2.Search and Discovery** : In the building phase of a machine learning model , its not expected to have a pre-defined list of the features that model will leverage to generate predictions . instead, there is a considerable amount of time and effort spent to **discover** the features available and the metadata of each. 

Most of current commerical the feature stores provide a visual user interface for Browsing the feature catalog and this allows teams to understand features better and determine if a feature is useful for a particular model or not.

<span style="color:red"> ***Important Note : Features Discovery and reusability are at the heart of the feature store , and the two capabiltiies are usually used together , so that the data scientist discover the available features within the store and then decides to reuse them.*** </span>

**3.Decoupling ML Models from Data infrastructure** :  ML systems built on traditional data infrastructure are often strongly coupled to databases, object stores, streams, and files. A result of this coupling, however, is that any change in data infrastructure may break dependent ML systems. feature store decouples ML models from the data infrastructure by providing a single data access layer to access any needed feature for model serving or model training.

**4.Avoid Training serving Skew** : Training-serving skew is a difference between performance during training and performance during serving. This skew can be caused by many reasons but the most common reason is dual implementations of data retrieval for training and serving can lead to inconsistencies in data. <br>  

As models are trained on engineered datasets, it’s imperative to apply the same transformations to data sent for prediction. This often means rewriting feature engineering code, sometimes in a different language, integrating it in your prediction workflow, and running it at prediction time. This whole process is not only time-consuming, it can also introduce inconsistencies, as even the tiniest variation in a data transform can have a large impact on predictions.

Feature Store is a good solution for the training-serving skew issue as The definitions of features used to train a model must exactly match the features provided in online serving .

the feature store let you feed both training and inference with the same transformed feature values, ensuring consistency to drive more accurate predictions. 

**5.Avoid Data Leakage** :  [Data Leakage](https://en.wikipedia.org/wiki/Leakage_(machine_learning) is accidentally using data in model training that wouldn’t be available at the time of prediction.

Models with leakage perform unrealistically well in development, but they deliver poor model accuracy in production without the benefit of future data.

Data Leakage usually happen with time-dependent features ( A featue value that got changed at time after the time of the generating the training label) . data scientists must ensure that the feature values are built only using data that could be known before the target was observed. For example, if a label was generated at time t1, any feature value should be generated beyond t1 ,and this concept is called **Point in time features**


Let's use the example and diagram from [AWS Feature Store Blog](https://aws.amazon.com/blogs/machine-learning/build-accurate-ml-training-datasets-using-point-in-time-queries-with-amazon-sagemaker-feature-store-and-apache-spark/#:~:text=Feature%20Store%20lets%20you%20define,time%20correct%20datasets%20for%20training) to explain how feature store solves the data leakage issue.

Imagine we’re training a fraud detection model on a set of historical transactions.and the features are mainly related to the consumer, merchant, and credit card. and in this case the feature values change over time.
To avoid leaking future feature values, a point-in-time query retrieves the latest state of each feature that was available at each transaction time, and no later. For example, the transaction at time t2 can only use features available before time t2, and the transaction at t1 can’t use features from timestamps greater than t1.


<img src="/docs/images/feature_stores/data_leakage.jpg"  width="800" />



## Feature Store Main Components

Most of the commerical feature stores are composed of Five main components : Registry , Storage , Transformation , Monitoring and user facing sdk.

### Registry Component

Centralized registry of feature definitions and metadata. The registry acts as a single source of truth for information about a feature in an organization. 
It stores information like feature names , features description and data types and to which entities are these features related to.

### Storage Component

Feature stores persist feature data to support retrieval. They typically contain two layers of storage: <br>

**Online Storage Layer** : used to persist feature values for low-latency lookup during inference. They typically only store the latest feature values for each entity .They are usually implemented with key-value stores like DynamoDB, Redis, or Cassandra.

**Offline Storage Layer** : used to store months’ or years’ worth of feature data for training purposes. Offline feature store data is often stored in data warehouses or data lakes like S3, BigQuery, Snowflake, Redshift.



### Transformation Component

Feature stores both manage and orchestrate data transformations that produce the feature values. Transformations managed by feature stores are configured by definitions in the feature registry.

### Monitoring Component

When something goes wrong in an ML system, it’s usually a data problem. Feature stores are positioned to detect data issues. They can calculate metrics on the features they store and serve that describe correctness and quality. 
Feature data can be validated based on user defined schemas or other structural criteria. Data quality is tracked by monitoring for data drift and training-serving skew.

### User Facing SDK

The feature store represents a black box for the end user. and the only way to access it is through a single inteface (SDK) that contains methods to :<br>
**Manage feature definitions and metadata.** <br>
**Ingest new data to the feature store.** <br>
**Retrieve training datasets.** <br>
**Retrieve online features.** <br>


## How features are organized inside the feature store ?

To understand how features are organized inside the feature store , let's first define some of feature store important terms :

**Entity** : An entity is any domain object that can be modeled and about which information can be stored. such as persons, places, things, or events.

**Feature** : A Feature is any measurable property that can be used in a predictive model.

<span style="color:red"> ***Features are the properties that describe the entities , for example number of transactions can be a feature for the entity credit card.***</span>

**Feature Table** : The logical grouping and schema of entities and related features.

*One of the popular designs is to create one feature table for each entity , which contains all the features that descibe this particular entity.*

Feature Store of an organization contains serveral ML Projects , and each ML project contains one or more feature tables and each feature table contains one or more feature that descibes a certain entity or set of entities. 


## Summary

Feature stores if not already ubiquitous should be the next must-have step for every organisation that aims to build the best AI products without having to lose their bandwidth on operational purposes.
If you find yourself continually repeating effort to code up feature transformations or copying and pasting feature-engineering code from project to project, a feature store could greatly simplify your life.

For more information about the feature store platforms [Feature Stores for ML Forum](https://www.featurestore.org/) created a curated list for all the existing feature stores and very detailed comparsion between them.
