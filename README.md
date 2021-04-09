# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, I build and optimized an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.</br>

In this project, I had the opportunity to create and optimize an ML pipeline. Provided a **custom-coded model—a standard Scikit-learn Logistic Regression**—the hyperparameters of which **optimized using HyperDrive**. I also used **AutoML to build and optimize a model** on the same dataset, so that I can **compare the results of the two methods**. </br>
![You can see the main steps that I took in the diagram below:](https://github.com/AnshuTrivedi/Project-1-Optimizing-ML-Pipeline-in-Azure/blob/main/Images/project_overview.png)


## Summary

**Classification using a Bank Marketing Dataset**</br>
We use the UCI Bank Marketing dataset to predict if client will subscribe term deposit with the bank.</br>
The classification goal is to predict if the client will subscribe to a term deposit with the bank.

**In 1-2 sentences, explain the solution: e.g. "The best performing model was a ..."**</br>
The best performing model was with ID AutoML_9765636a-58a8-4a56-9043-e56ab8003e08_12 and of 'accuracy': 0.9158725341426404. 	
Accuracy of best model from HyperDrive was lower given as'Accuracy': 0.9083515416363195  with 'runId': 'HD_c47e63b0-5bc1-4af4-92de-979246af385a_1' .

## Scikit-learn Pipeline
**Explain the pipeline architecture, including data, hyperparameter tuning, and classification algorithm.**</br>
Parameter sampler

```ps = RandomParameterSampling(
    {
        '--C' : choice(0.001,0.01,0.1,1,10,20,50,100,200,500,1000),
        '--max_iter': choice(50,100,200,300)
    } )
```

I chose discrete values with choice for both parameters, C and max_iter.
C is the Regularization while max_iter is the maximum number of iterations.

**What are the benefits of the parameter sampler you chose?**
Random sampling supports discrete and continuous hyperparameters. It supports early termination of low-performance runs. Some users do an initial search with random sampling and then refine the search space to improve results.</br>

Grid sampling supports discrete hyperparameters. Use grid sampling if you can budget to exhaustively search over the search space.</br>
Bayesian sampling is recommended if you have enough budget to explore the hyperparameter space. Bayesian sampling does not support early termination.

**What are the benefits of the early stopping policy you chose?**
For more aggressive savings, use Bandit Policy with a smaller allowable slack 

An early stopping policy is used to automatically terminate poorly performing runs thus improving computational efficiency. I chose the BanditPolicy which I specified as follows:

```policy = BanditPolicy(evaluation_interval=2, slack_factor=0.1)```
evaluation_interval: This is optional and represents the frequency for applying the policy. Each time the training script logs the primary metric counts as one interval.

slack_factor: The amount of slack allowed with respect to the best performing training run. This factor specifies the slack as a ratio.

Any run that doesn't fall within the slack factor or slack amount of the evaluation metric with respect to the best performing run will be terminated. This means that with this policy, the best performing runs will execute until they finish.

## AutoML
**In 1-2 sentences, describe the model and hyperparameters generated by AutoML.**
Model Configuration
```
automl_config = AutoMLConfig(
    compute_target = compute_target,
    experiment_timeout_minutes=15,
    task='classification',
    primary_metric= 'accuracy',
    training_data= train_data,
    label_column_name= label,
    n_cross_validations= 2)
```


## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**
|Hyperdrive model|   |
|----------------|---|
|id| HD_c47e63b0-5bc1-4af4-92de-979246af385a_1 |
|accuracy| 0.9083515416363195 |

|AutoML model|  |
|------------|--|
|id          | AutoML_9765636a-58a8-4a56-9043-e56ab8003e08_12 |
|accuracy| 0.9158725341426404 |


## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**
**Data exploration**
I observed data set class was highly imbalanced, so data exploration can play major role before training model.
Class imbalance can be reduced by adding more samples of low sampled class or resampling population data. There are other methods also.
**Hyper parameter tuning**
Tuning hyperparameters such as n_cross_validations in more reliable way or benchmarked values of hyper parameter can improve model metrics.

## References :
[classification-bank-marketing-all-features](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) </br>
[How i decided early termination policy and sampling space](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-tune-hyperparameters#specify-an-early-termination-policy)</br>
[Udacity student hub](https://study-hall.udacity.com/)
