---
title: Predicting Credit Default Risk: A Full-Stack Data Science Project
author: Joseph Kwami Azalekor
date: 2025-05-06
tags: ["post", "featured"]
image: /assets/blog/article-4.jpg
imageAlt: Credit Default Web App
description: Predicting credit default risk is a critical task in the financial sector. This project delves into building a predictive model using the "Default of Credit Card Clients" dataset from the UCI Machine Learning Repository. 
---

<h2>Introduction</h2>
<p>Predicting credit default risk is a critical task in the financial sector. This project delves into building a predictive model using the “Default of Credit Card Clients” dataset from the UCI Machine Learning Repository. The objective is to develop an end-to-end machine learning pipeline, culminating in a deployable web application.</p>

<h2>Understanding the Data</h2>
<p>The dataset comprises 30,000 observations with 23 features, including credit limit (<code>LIMIT_BAL</code>), repayment status over the past six months (<code>PAY_0</code> to <code>PAY_6</code>), demographic details, and billing/payment amounts. The target variable, <code>default.payment.next.month</code>, indicates whether a client defaulted on their next payment.</p>

<p>Initial data exploration involved:</p>
<ul>
  <li>Checking for missing values and duplicates.</li>
  <li>Assessing data types and distributions.</li>
  <li>Understanding the balance between default and non-default classes.</li>
</ul>

<h2>Exploratory Data Analysis</h2>
<p>Key insights from the exploratory phase included:</p>
<ul>
  <li>A noticeable class imbalance, with approximately 22% defaults.</li>
  <li>Strong correlations between recent repayment behaviors (<code>PAY_1</code>, <code>PAY_2</code>) and default likelihood.</li>
  <li>Demographic factors like age and credit limit influencing default rates.</li>
</ul>

<p>Visualizations aided in identifying patterns and relationships among variables, guiding subsequent feature selection.</p>

<h2>Feature Selection & Preprocessing</h2>
<p>From the original 23 features, a subset of 10 was selected based on:</p>
<ul>
  <li>Correlation analysis.</li>
  <li>Feature importance metrics from preliminary models.</li>
  <li>Domain knowledge.</li>
</ul>

<p>Preprocessing steps included:</p>
<ul>
  <li>Normalizing numerical features using <code>StandardScaler</code>.</li>
  <li>Encoding categorical variables appropriately.</li>
  <li>Splitting the data into training and testing sets.</li>
</ul>

<h2>Modeling and Evaluation</h2>
<p>Three models were developed:</p>
<ul>
  <li>Logistic Regression (baseline).</li>
  <li>Random Forest Classifier.</li>
  <li>XGBoost Classifier.</li>
</ul>

<p>Hyperparameter tuning was conducted using <code>GridSearchCV</code>. Evaluation metrics focused on:</p>
<ul>
  <li>Accuracy.</li>
  <li>F1-Score.</li>
  <li>ROC-AUC Score.</li>
</ul>

<p>Both the tuned Random Forest and XGBoost models demonstrated superior performance, with XGBoost slightly outperforming in terms of ROC-AUC.</p>

<h2>Deployment Journey</h2>
<p>The initial deployment plan involved AWS Lambda. However, challenges arose due to:</p>
<ul>
  <li>AWS Lambda's package size limitations.</li>
  <li>The absence of a native Python environment in the free tier.</li>
  <li>The complexity of managing dependencies exceeding 250MB.</li>
</ul>

<p>Consequently, the project pivoted to deploying the application using Streamlit Cloud, leveraging GitHub for version control and dependency management via <code>requirements.txt</code>. This approach facilitated a seamless deployment process, albeit with scalability considerations.</p>

<h2>The Live Web App</h2>
<p>The deployed application is accessible at:</p>
<p><a href="https://jkazalekor-credit-default-app.streamlit.app/">https://jkazalekor-credit-default-app.streamlit.app/</a></p>

<p>Features include:</p>
<ul>
  <li>User-friendly interface for inputting client data.</li>
  <li>Real-time predictions indicating default probabilities.</li>
  <li>Display of the model used for prediction (Random Forest or XGBoost).</li>
</ul>

<p>While the application serves as a functional demonstration, it is tailored to the specific dataset and lacks features like API integration or model retraining capabilities.</p>

<h2>Reflections and Future Directions</h2>
<p>This project underscored the importance of adaptability in deployment strategies and the value of thorough exploratory analysis. Future enhancements could involve:</p>
<ul>
  <li>Exploring scalable deployment options like AWS SageMaker.</li>
  <li>Incorporating additional datasets to improve model generalizability.</li>
  <li>Implementing features for model retraining and user authentication.</li>
</ul>

<h2>Project Resources</h2>
<ul>
  <li><strong>GitHub Repository:</strong> <a href="https://github.com/jkazalekor/credit-default-app">github.com/jkazalekor/credit-default-app</a></li>
  <li><strong>Live Application:</strong> <a href="https://jkazalekor-credit-default-app.streamlit.app/">Streamlit App</a></li>
  <li><strong>Dataset:</strong> <a href="https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients">UCI Repository</a></li>
</ul>
