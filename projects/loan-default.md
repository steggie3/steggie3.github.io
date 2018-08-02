---
title: Loan Default Prediction Machine Learning Project
permalink: /projects/loan-default.html
layout: single
classes: wide
author_profile: true
---

This is an exploratory project for me to apply different Machine Learning (ML) models and techniques and have a better understanding of how each of them work and interact with the data:
+ Feature Engineering
  + One-hot encoding for categorical features
  + Normalization/standardization
  + Imputation
  + Feature expansion
  + Feature reduction:
    + Feature hashing
    + Feature selection
    + Principal component analysis
  + Feature discretization with Decision Trees or Random Forests
+ Machine Learning Models:
  + Logistic Regression
  + Decision Trees, Random Forest, Gradient-Boosted Decision Trees
  + K-Nearest-Neighbor
  + Support Vector Machines
  + Neural Networks

The data is from a Kaggle competition [Loan Default Prediction](https://www.kaggle.com/c/loan-default-prediction).

I started with data cleaning and data preprocessing, applied each of the ML model to the data, performed hyperparameter turing to explore the potential of the models, and then analyzed the accuracy, Receiver Operating Characteristic (ROC) curves, and Precision-Recall (PR) curves to evaluate the quality of the resulting models. I also applied different feature engineering techniques and compared the model performance. The detailed experiments and reports in the form of Jupyter notebooks are available [here](https://github.com/steggie3/loan-default-prediction).

<figure>
  <img src="{{site.url}}/projects/loan-default/curves.png" alt="curves.png"/>
  <figcaption>Model statistics, ROC curve, and PR curve in one of the experiments.</figcaption>
</figure>
