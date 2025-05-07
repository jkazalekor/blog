---
title: Predicting Credit Default Risk: A Full-Stack Data Science Project
author: Joseph Kwami Azalekor
date: 2025-05-06
tags: ["post", "featured"]
image: /assets/blog/article-4.jpg
imageAlt: Credit Default Web App
description: Predicting credit default risk is a critical task in the financial sector. This project delves into building a predictive model using the "Default of Credit Card Clients" dataset from the UCI Machine Learning Repository. The objective is to develop an end-to-end machine learning pipeline, culminating in a deployable web application. 
---

## Introduction

Predicting credit default risk is a critical task in the financial sector. This project delves into building a predictive model using the “Default of Credit Card Clients” dataset from the UCI Machine Learning Repository. The objective is to develop an end-to-end machine learning pipeline, culminating in a deployable web application.

## Understanding the Data

The dataset comprises 30,000 observations with 23 features, including credit limit (`LIMIT_BAL`), repayment status over the past six months (`PAY_0` to `PAY_6`), demographic details, and billing/payment amounts. The target variable, `default.payment.next.month`, indicates whether a client defaulted on their next payment.

Initial data exploration involved:

- Checking for missing values and duplicates.
- Assessing data types and distributions.
- Understanding the balance between default and non-default classes.

## Exploratory Data Analysis

Key insights from the exploratory phase included:

- A noticeable class imbalance, with approximately 22% defaults.
- Strong correlations between recent repayment behaviors (`PAY_1`, `PAY_2`) and default likelihood.
- Demographic factors like age and credit limit influencing default rates.

Visualizations aided in identifying patterns and relationships among variables, guiding subsequent feature selection.

## Feature Selection & Preprocessing

From the original 23 features, a subset of 10 was selected based on:

- Correlation analysis.
- Feature importance metrics from preliminary models.
- Domain knowledge.

Preprocessing steps included:

- Normalizing numerical features using `StandardScaler`.
- Encoding categorical variables appropriately.
- Splitting the data into training and testing sets.

## Modeling and Evaluation

Three models were developed:

- Logistic Regression (baseline).
- Random Forest Classifier.
- XGBoost Classifier.

Hyperparameter tuning was conducted using `GridSearchCV`. Evaluation metrics focused on:

- Accuracy.
- F1-Score.
- ROC-AUC Score.

Both the tuned Random Forest and XGBoost models demonstrated superior performance, with XGBoost slightly outperforming in terms of ROC-AUC.

## Deployment Journey

The initial deployment plan involved AWS Lambda. However, challenges arose due to:

- AWS Lambda's package size limitations.
- The absence of a native Python environment in the free tier.
- The complexity of managing dependencies exceeding 250MB.

Consequently, the project pivoted to deploying the application using Streamlit Cloud, leveraging GitHub for version control and dependency management via `requirements.txt`. This approach facilitated a seamless deployment process, albeit with scalability considerations.

## The Live Web App

The deployed application is accessible at:

🔗 [credit-default-app.streamlit.app](https://jkazalekor-credit-default-app.streamlit.app/)

Features include:

- User-friendly interface for inputting client data.
- Real-time predictions indicating default probabilities.
- Display of the model used for prediction (Random Forest or XGBoost).

While the application serves as a functional demonstration, it is tailored to the specific dataset and lacks features like API integration or model retraining capabilities.

## Reflections and Future Directions

This project underscored the importance of adaptability in deployment strategies and the value of thorough exploratory analysis. Future enhancements could involve:

- Exploring scalable deployment options like AWS SageMaker.
- Incorporating additional datasets to improve model generalizability.
- Implementing features for model retraining and user authentication.

## Project Resources

- **GitHub Repository**: [github.com/jkazalekor/credit-default-app](https://github.com/jkazalekor/credit-default-app)
- **Live Application**: [Streamlit App](https://jkazalekor-credit-default-app.streamlit.app/)
- **Dataset**: [UCI Repository](https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients)
