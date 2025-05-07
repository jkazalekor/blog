---
title: "Predicting Credit Default Risk, A Full-Stack Data Science Project"
author: Joseph Kwami Azalekor
date: 2025-05-06
tags: ["post", "featured"]
image: /assets/blog/credit_default_web_app/credit_default_web_app.png
imageAlt: Credit Default Web App
description: >
  This in-depth blog covers a complete machine learning pipeline built to predict credit default risk. It uses real-world financial data, advanced models (XGBoost, Random Forest), and ends with a deployed Streamlit application. The entire project is publicly available via GitHub and includes thorough evaluation and hyperparameter tuning steps.
---

<h2>Introduction</h2>

<p>Credit default prediction is central to financial risk management. In this project, I built a comprehensive machine learning pipeline to predict whether a credit card client will default in the next month. The model was trained on a dataset sourced from the <a href="https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients" target="_blank">UCI Machine Learning Repository</a>. The pipeline includes data preprocessing, feature reduction, model training (Logistic Regression, Random Forest, and XGBoost), and hyperparameter tuning. The final model was deployed using Streamlit and is live <a href="https://jkazalekor-credit-default-app.streamlit.app/">here</a>.</p>

<h2>Dataset Overview</h2>

<p>The dataset contains <strong>30,000 observations</strong> and <strong>24 features</strong> including:</p>
<ul>
  <li><code>LIMIT_BAL</code>: Credit limit for the client</li>
  <li><code>SEX</code>, <code>EDUCATION</code>, <code>MARRIAGE</code>: Demographic info</li>
  <li><code>PAY_0</code> to <code>PAY_6</code>: Repayment status over the last six months</li>
  <li><code>BILL_AMT1</code> to <code>BILL_AMT6</code>: Historical billing amounts</li>
  <li><code>PAY_AMT1</code> to <code>PAY_AMT6</code>: Repayment amounts</li>
</ul>

<p>The target variable is <code>default.payment.next.month</code>, where 1 means the client defaulted and 0 means they paid successfully.</p>

<h2>Exploratory Data Analysis</h2>

<p>Initial exploration revealed no missing values. A class imbalance was evident: only ~22% of clients defaulted. Strong predictors included repayment history (especially <code>PAY_1</code>) and bill amounts. All features were numerical, allowing seamless integration into scikit-learn pipelines.</p>

<h2>Feature Selection and Preprocessing</h2>

<p>I reduced the number of features from 24 to 10, focusing on the most predictive ones like <code>LIMIT_BAL</code>, <code>PAY_1</code>, <code>PAY_AMT1</code>, and <code>BILL_AMT1</code>. Standard scaling was applied using <code>StandardScaler</code> to normalize continuous features.</p>

<pre><code class="language-python">
scaler = StandardScaler()
X_scaled = scaler.fit_transform(df[feature_list])
</code></pre>

<h2>Modeling and Evaluation</h2>

<p>I evaluated three models: Logistic Regression (baseline), Random Forest, and XGBoost. The dataset was split using <code>train_test_split</code> with 20% for testing. Below is a snippet used to fit XGBoost:</p>

<pre><code class="language-python">
xgb = XGBClassifier(use_label_encoder=False, eval_metric="logloss")
xgb.fit(X_train, y_train)
</code></pre>

<p><strong>Evaluation metrics:</strong></p>
<ul>
  <li><strong>Accuracy</strong>: Measures overall correct predictions</li>
  <li><strong>F1-Score</strong>: Weighted average of precision and recall</li>
  <li><strong>ROC-AUC</strong>: Model discrimination ability</li>
</ul>

<p>Hyperparameter tuning was performed using both <code>GridSearchCV</code> and <code>RandomizedSearchCV</code>. XGBoost emerged as the top-performing model with the highest F1 and ROC-AUC.</p>

<h2>Deployment</h2>

<p>The deployment goal was to host the model via an API. However, AWS Lambda had package size limitations that made deploying XGBoost infeasible under the free tier. As a result, I pivoted to <strong>Streamlit Cloud</strong>, which integrates directly with GitHub and supports Python natively.</p>

<pre><code class="language-python">
joblib.dump(xgb_best, "xgb_model.pkl")
</code></pre>

<p>The final app was deployed from the following GitHub repository:  
<a href="https://github.com/jkazalekor/credit-default-app">github.com/jkazalekor/credit-default-app</a></p>

<h2>Web App Features</h2>

<ul>
  <li>User inputs financial info: credit limit, repayment history, and bill/payment amounts</li>
  <li>Option to choose between Random Forest and XGBoost model</li>
  <li>Displays prediction: <em>Will the client default?</em></li>
  <li>Shows confidence probability and selected model's performance</li>
</ul>

<p>App is live here: <a href="https://jkazalekor-credit-default-app.streamlit.app/">credit-default-app.streamlit.app</a></p>

<h2>Reflections and Learnings</h2>

<p>This project deepened my understanding of both data preprocessing and deployment challenges in machine learning. Choosing the right model and tuning it for real-world performance required both statistical and engineering tradeoffs. I learned:</p>

<ul>
  <li>Deployment constraints should be considered from the start</li>
  <li>Random Forest and XGBoost perform well on tabular financial data</li>
  <li>Feature importance scores can guide useful dimensionality reduction</li>
</ul>

<h2>Project Links</h2>

<ul>
  <li><strong>GitHub Repository:</strong> <a href="https://github.com/jkazalekor/credit-default-app">github.com/jkazalekor/credit-default-app</a></li>
  <li><strong>Live Web App:</strong> <a href="https://jkazalekor-credit-default-app.streamlit.app/">Streamlit App</a></li>
  <li><strong>Dataset:</strong> <a href="https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients">UCI Repository</a></li>
</ul>
