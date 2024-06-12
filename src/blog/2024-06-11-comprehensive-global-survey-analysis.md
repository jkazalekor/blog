---
title: Comprehensive Global Survey Analysis
author: Joseph Kwami Azalekor
date: 2024-06-11
tags: ["post", "featured"]
image: /assets/blog/article-4.jpg
imageAlt: Global Survey Analysis
description: This is an exploratory data analysis of a global employee survey conducted in 2021. It spans several countries in Europe, the Americas, and other parts of the world. Employees from various industries took part in the survey, making it very diverse. I hope you find insights interesting. 
---

This is an exploratory data analysis of a global employee survey conducted in 2021. It spans several countries in Europe, the Americas, and other parts of the world. Employees from various industries took part in the survey, making it very diverse. I hope you find insights interesting. Thank you for your audience.

We start by importing the necessary libraries for the analysis.

<pre><code class="language-python">
# Adding Pandas and Numpy libraries
import pandas as pd
import numpy as np

</code></pre>

Then we import the data we would be exploring. The data can be downloaded from this <a href="https://docs.google.com/spreadsheets/d/1IPS5dBSGtwYVbjsfbaMCYIWnOuRmJcbequohNxCyGVw/edit?resourcekey#gid=1625408792" target="_blank" class="link">GoogleSheet</a> page.

<pre><code class="language-python">
# Importing data for analysis
df = pd.read_excel("Ask A Manager Salary Survey 2021.xlsx")

</code></pre>

Running a basic inspection of the data shows us that the column names have to be fixed. We do that with this function that takes a dataframe and dictionary as input. The output is the original dataframe with new column names.

<pre><code class="language-python">
#Function to change multiple column names using the indices of the columns

def rename_columns(df, index_name_dict):
    for index, new_name in index_name_dict.items():
        if 0 <= index < len(df.columns):
             df.rename(columns={df.columns[index]: new_name}, inplace=True)
        else:
            raise IndexError(f"Column index {index} out of range")

</code></pre>

<pre><code class="language-python">
column_change_dict = {1: "Age", 2: "Industry", 3: "Job_Title", 4: "Job_Title_1",
                         5: "Annual_Salary", 6: "Annual_Salary_1", 
                    7: "Currency", 8: "Currency_1", 9: "Annual_Salary_2", 
                    10: "Country", 11: "US_State", 12: "City", 13: "Work_Experience",
                     14: "Work_Experience_Industry", 15: "Highest_Education", 16: "Gender", 17: "Race"}
                     
rename_columns(df, column_change_dict)

</code></pre>

With our corrected column names we the basic inspection of the dataframe is now a breeze. We see that "Timestamp", "Job_Title_1", "Currency_1", "Annual_Salary_2" columns are less relevant or have very few non-null cells so, we drop them.

<pre><code class="language-python">
# Dropping less relevant columns

df = df.drop(columns=["Timestamp", "Job_Title_1", "Currency_1", "Annual_Salary_2"])

</code></pre>

The check for duplicates catches over 150 duplicates. We go ahead and drop the duplicates and keep the first one found. Now we have no duplicates. Then we reset the index of the dataframe to keep it tidy.

<pre><code class="language-python">
# Checking for the number of duplicates

df.duplicated().sum()

</code></pre>

<pre><code class="language-python">
# Dropping duplicate after keeping the first one

df = df.drop_duplicates(keep="first")

# Resetting index

df = df.reset_index(drop=True)

</code></pre>

Now univariate analysis, let’s start with bar chart visuals of ages of employees.

<pre><code class="language-python">
# Bar chart of employee ages

plt.figure(figsize=(10, 6))
df["Age"].value_counts().plot(kind="bar")
plt.title("Chart of Ages of Employees")
plt.ylabel("Frequency")
plt.xticks(rotation=45)
plt.show()

</code></pre>

Then, the top 10 industries in the survey is plotted with a horizontal bar chart.

<pre><code class="language-python">
# Barchart of industries in the survey

plt.figure(figsize=(10, 6))
top_10_industry = df["Industry"].value_counts().head(10)
top_10_industry.sort_values(ascending=True).plot(kind="barh")
plt.title("Top 10 Industries in Survey")
plt.show()

</code></pre>

Also, the 20 most frequent job titles are visualized with a horizontal bar chart.

<pre><code class="language-python">
# Get the top 10 job titles
top_n = 20
top_job_titles = df['Job_Title'].value_counts().head(top_n)

# Plotting the top job titles with horizontal bars
plt.figure(figsize=(12, 8))
top_job_titles.sort_values(ascending=True).plot(kind='barh')
plt.title(f'Top {top_n} Job Titles')
plt.xlabel('Count')
plt.ylabel('Job Title')
plt.show()

</code></pre>

For salaries, the currencies vary since this is a global survey. By inspection, we observe that one of the unique values in the “Currency” column is “Other”. So let’s set that value aside and handle the known currencies by adjusting the currencies to the US Dollar. We do that with a dictionary mapping and a function that converts the currencies to USD. A new column “Adjusted_Salary_USD” is created in the process.

<pre><code class="language-python">
# Define conversion rates to USD
conversion_rates = {
    'USD': 1.0,
    'CAD': 0.73,
    'GBP': 1.28,
    'EUR': 1.10,
    'AUD/NZD': 0.65,
    'CHF': 1.12,
    'SEK': 0.096,
    'JPY': 0.0064,
    'ZAR': 0.053,
    'HKD': 0.13
}

# Function to convert salary to USD
def convert_to_usd(data):
    if data['Currency'] in conversion_rates:
        return data['Annual_Salary'] * conversion_rates[data['Currency']]
    return data['Annual_Salary']  # Return the original value if currency is "Other"

# Apply the conversion function to create a new column
df['Adjusted_Salary_USD'] = df.apply(convert_to_usd, axis=1)

</code></pre>

After we create a new dataframe “df_currency” for rows (or observations) with “Currency” value not equal to “Other”. Thus with the “value_counts()” method we see that “Currency” column for “df_currency” is without “Other” cell values.

<pre><code class="language-python">
# Creating new dataframe with no "Other" value in "Currency" column

df_currency = df[df["Currency"] != "Other"]
df_currency["Currency"].value_counts()

</code></pre>

A histogram or boxplot visual of “Adjusted_Salary_USD” on “df_currency” gives us an uninsightful diagram. This is because of extreme values or outliers in the dataset. Let’s fix that with this function that multiplies 1.5 by the interquartile range and creates an upper and lower bound by adding the upper quartile and lower quartile respectively.

<pre><code class="language-python">
# Histogram plot of 'Adjusted_Salary_USD' on 'df_currency'

df_currency["Adjusted_Salary_USD"].hist(bins=10)
plt.show()

</code></pre>

<pre><code class="language-python">
# Boxplot plot of 'Adjusted_Salary_USD' on 'df_currency'

df_currency["Adjusted_Salary_USD"].plot(kind="box")
plt.show()

</code></pre>

<pre><code class="language-python">
# Function to remove outliners from dataframe

def remove_outliers_iqr(df, column):
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1

    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR

    outliers = df[(df[column] < lower_bound) | (df[column] > upper_bound)]
    df_clean = df[(df[column] >= lower_bound) & (df[column] <= upper_bound)]

    return df_clean, outliers

# Calling the remove_outliers_iqr function

df_currency_adj, df_currency_outliers = remove_outliers_iqr(df_currency, "Adjusted_Salary_USD")
</code></pre>

With our new dataframe “df_currency_adj” we can create an insightful histogram and boxplot to see the distribution of employee salaries in the survey.

<pre><code class="language-python">
# Boxplot of 'Adjusted_Salary_USD' on 'df_currency_adj

df_currency_adj["Adjusted_Salary_USD"].plot(kind="box")
plt.title("Boxplot of Adjusted Salary in USD")
plt.ylabel("Adjusted Salary (USD)")
plt.show()

</code></pre>

<pre><code class="language-python">
# Histogram of 'Adjusted_Salary_USD' on 'df_currency_adj

df_currency_adj["Adjusted_Salary_USD"].plot(kind="hist", bins=20)
plt.title("Histogram of Adjusted Salary in USD")
plt.xlabel("Adjusted Salary (USD)")
plt.ylabel("Frequency")
plt.show()

</code></pre>

Can you believe that there are over 1.2k outliers in our dataset? Interesting.

<pre><code class="language-python">
# Outliers in the dataframe

df_currency_outliers

</code></pre>

The “Country” column had a lot of messy data. There was a lot of cleaning to be done there. We start by making all observations with non-null “US_State” columns have a “Country” value of “USA”. Then we do a county mapping dictionary to replace as much of the messy data in the “Country” column.

<pre><code class="language-python">
# Changing all observations with non-null 'US_State' columns to 'Country' == 'USA'

df.loc[df['US_State'].notna(), 'Country'] = 'USA'

# Country mapping to correct several messy values in 'Country' column

country_mapping = {
    'United States': 'USA',
    'US': 'USA',
    'Uk': 'UK',
    'England': 'UK',
    'United Kingdom': 'UK',
    'Scotland': 'UK',
    'canada': 'Canada',
    'United Kingdom ': 'UK',
    'United kingdom' : 'UK',
    'England ': 'UK',
    'UK ': 'UK',
    'Canada ': 'Canada',
    'Germany ': 'Germany',
    'Australia ': 'Australia',
    'U.K.': 'UK',
    'U.K. ': 'UK',
    'New Zealand ': 'New Zealand',
    'The Netherlands': 'Netherlands',
    'France ': 'France',
    'Netherlands ': 'Netherlands',
    'Spain ': 'Spain',
    'Sweden ': 'Sweden'
}

df['Country'] = df['Country'].replace(country_mapping)

</code></pre>

After we plot the “Country” data with a horizontal bar chart. Without question, the USA dominates.

<pre><code class="language-python">
# Bar chart of top 5 countries in survey

top_5_countries = df["Country"].value_counts().head()
top_5_countries.sort_values(ascending=False).plot(kind="barh")
plt.ylabel("Frequency")
plt.title("Top 5 Countries in the Survey")
plt.show()

</code></pre>

We find ourselves in a similar situation in the “City” column. And we do the same for some of the values in the “City” column. Then we display a horizontal bar chart of the top 30 cities in the survey.

<pre><code class="language-python">
# City mapping to replace messy data in 'City' column

city_mapping = {
    "New York": "New York City",
    "NYC": "New York City",
    "Washington, DC": "Washington DC",
    "Washington": "Washington DC"
}

df['City'] = df['City'].replace(city_mapping)

</code></pre>

<pre><code class="language-python">
# Bar chart of top 30 cities in the survey

top_30_cities = df["City"].value_counts().head(30)
plt.figure(figsize=(11, 7))
top_30_cities.sort_values(ascending=True).plot(kind="barh")
plt.title("Top 30 Cities in the Survey")
plt.xlabel("Count")
plt.show()

</code></pre>

After we replicate the same plot from the “City” column for the top 30 US States in the survey.

<pre><code class="language-python">
# Bar chart of the top 30 US States

plt.figure(figsize=(11, 7))
top_30_states = df["US_State"].value_counts().head(30)
top_30_states.sort_values(ascending=True).plot(kind="barh")
plt.title("Top 30 US States in the Survey")
plt.ylabel("US State")
plt.xlabel("Count")
plt.show()

</code></pre>


Moving on, a side-by-side pie chart is used to display the data in “Work_Experience” and “Work_Experience_Industry”. Some extra code is used to create matching colors for the unique values in both pie charts.

<pre><code class="language-python">
# Creating unique colors for each category of work experience

unique_values = pd.concat([df["Work_Experience"].value_counts(), df["Work_Experience_Industry"].value_counts()]).index.unique()
colors = plt.cm.tab20.colors[:len(unique_values)]  # Get a color palette

color_map = {val: colors[i] for i, val in enumerate(unique_values)}

# Function to get colors for the pie chart
def get_colors(values):
    return [color_map[val] for val in values]

fig, axs = plt.subplots(1, 2, figsize=(12, 6))

axs[0].pie(df["Work_Experience"].value_counts(), 
           labels=df["Work_Experience"].value_counts().index, autopct='%1.1f%%', startangle=140, 
           colors=get_colors(df["Work_Experience"].value_counts().index))
axs[0].set_title('Pie Chart of General Work Experience')

axs[1].pie(df["Work_Experience_Industry"].value_counts(), 
           labels=df["Work_Experience_Industry"].value_counts().index, 
           autopct='%1.1f%%', startangle=140, colors=get_colors(df["Work_Experience_Industry"].value_counts().index))
axs[1].set_title('Pie Chart of Work Experience In The Industry')

plt.tight_layout()
plt.show()

</code></pre>

Next we plot the “Highest_Education” column with a bar chart. College degrees and Master’s degrees have the most counts.

<pre><code class="language-python">
# Bar chart of highest education of employees in the survey

plt.figure(figsize=(8, 5))
df["Highest_Education"].value_counts().plot(kind="bar")
plt.xlabel("Highest Education")
plt.ylabel("Count")
plt.xticks(rotation=45)
plt.show()

</code></pre>

We do same for the “Gender” column and an overwhelming number of employee are women.

<pre><code class="language-python">
# Bar chart of "Gender" column

df["Gender"].value_counts().plot(kind="bar")
plt.title("Gender Distribution of Survey")
plt.ylabel("Count")
plt.xticks(rotation=45)
plt.show()

</code></pre>

For the last univariate plot, we bar chart the race column and most of the surveyed employees were White.

<pre><code class="language-python">
# Bar chart of the top 10 races in the survey

top_10_races = df["Race"].value_counts().head(10)
top_10_races.sort_values(ascending=True).plot(kind="barh")
plt.title("Top 10 Employee Races in Survey")
plt.xlabel("Count")
plt.show()

</code></pre>

In our first bivariate analysis, we visualize a boxplot of highest education of employees against the adjusted salary using the “df_currency_adj” dataframe. It could be observed that employees with professional degrees or PhD are those with the highest average salaries.

<pre><code class="language-python">
# Boxplot of salaries by education level

edu_order = ["High School", "Some college", "College degree", "Master's degree", "PhD", "Professional degree (MD, JD, etc.)"]
plt.figure(figsize=(10, 6))
sns.boxplot(x='Highest_Education', y='Adjusted_Salary_USD', data=df_currency_adj, order=edu_order)
plt.title('Box Plot of Adjusted Salary by Education Level')
plt.ylabel("Adjusted Salary USD")
plt.xlabel("Highest Education")
plt.xticks(rotation=45)
plt.show()

</code></pre>

Then a boxplot of “Age” against “Adjusted_Salary_USD”. We see that the average salary increases till the age of “25-34”, after it plateaus. However, the “65 or over” category has the widest interquartile range.

<pre><code class="language-python">
# Boxplot of salaries by age

age_order = ['under 18', '18-24', '25-34', '35-44', '45-54', '55-64', '65 or over']
plt.figure(figsize=(10, 6))
sns.boxplot(x="Age", y="Adjusted_Salary_USD", data=df_currency_adj, order=age_order)
plt.xticks(rotation=45)
plt.ylabel("Adjusted Salary USD")
plt.title("Boxplot of Adjusted Salary by Age")
plt.show()

</code></pre>

The boxplot of work experience and adjusted salary is similar to “Age” against “Adjusted_Salary_USD”. Mean salary rises till “8-10 years”, then stales afterwards.

<pre><code class="language-python">
# Boxplot of salaries by work experience

work_exp_order = ['2 - 4 years', '5-7 years', '8 - 10 years', '11 - 20 years', '21 - 30 years', '31 - 40 years', '41 years or more']
plt.figure(figsize=(10, 6))
sns.boxplot(x="Work_Experience", y="Adjusted_Salary_USD", data=df_currency_adj, order=work_exp_order)
plt.xticks(rotation=45)
plt.ylabel("Adjusted Salary USD")
plt.xlabel("Work Experience")
plt.title("Boxplot of Adjusted Salary by Work Experience")
plt.show()

</code></pre>

The same can be said for “Work_Experience_Industry” and “Adjusted_Salary_USD”.

<pre><code class="language-python">
# Boxplot of salaries by work experience in industry

work_exp_inds_order = ['1 year or less', '2 - 4 years', '5-7 years', '8 - 10 years', '11 - 20 years', '21 - 30 years',
                       '31 - 40 years', '41 years or more']
plt.figure(figsize=(10, 6))
sns.boxplot(x="Work_Experience_Industry", y="Adjusted_Salary_USD", data=df_currency_adj, order=work_exp_inds_order)
plt.xticks(rotation=45)
plt.ylabel("Adjusted Salary USD")
plt.xlabel("Work Experience in Industry")
plt.title("Boxplot of Adjusted Salary by Work Experience in Industry")
plt.show()

</code></pre>

In the next plot, a boxplot of “Gender” against “Adjusted_Salary_USD”, we see men having the highest average salary although the survey has significantly more women.

<pre><code class="language-python">
# Boxplot of salaries by gender

plt.figure(figsize=(10, 6))
sns.boxplot(x="Gender", y="Adjusted_Salary_USD", data=df_currency_adj)
plt.title("Boxplot of Adjusted Salary by Gender")
plt.ylabel("Adjusted Salary USD")
plt.xticks(rotation=45)
plt.show()

</code></pre>

For the boxplot of the top six (6) US States and the adjusted salary we see California has the highest mean salary and the widest interquartile range. New York, Washington and Massachusetts closely follow in average salaries.

<pre><code class="language-python">
# Filtering the top 6 US States
filtered_states = ["California", "New York", "Massachusetts", "Texas", "Illinois", "Washington"]
df_top_states_adj = df_currency_adj[df["US_State"].isin(filtered_states)]

# Boxplot of salaries by the top 6 US States

plt.figure(figsize=(10, 6))
sns.boxplot(x="US_State", y="Adjusted_Salary_USD", data=df_top_states_adj)
plt.title("Boxplot of Adjusted Salary by Top US States")
plt.xlabel("US State")
plt.ylabel("Adjusted Salary USD")
plt.xticks(rotation=45)
plt.show()

</code></pre>

The visual of the top ten (10) industries in the survey against “Adjusted_Salary_USD” reveals that “Computing or Tech” is the most rewarding industry as it has the highest average salary and the widest box range. 

<pre><code class="language-python">
# Filtering the top 10 industries in the survey

filtered_industries = ["Computing or Tech", "Education (Higher Education)", "Nonprofits", 
                       "Health care", "Government and Public Administration", "Accounting, Banking & Finance",
                       "Engineering or Manufacturing", "Marketing, Advertising & PR", "Law", "Business or Consulting"]
df_top_inds_adj = df_currency_adj[df["Industry"].isin(filtered_industries)]

# Boxplot of salaries by the top 10 industries


plt.figure(figsize=(10, 6))
sns.boxplot(y="Industry", x="Adjusted_Salary_USD", data=df_top_inds_adj)
plt.title("Boxplot of Adjusted Salary by Top Industries")
plt.ylabel("Industry")
plt.xlabel("Adjusted Salary USD")
plt.xticks(rotation=45)
plt.show()

</code></pre>

The next plot is a clustered bar chart of “Gender” and the top 10 industries. The results showed women dominating in all industries, which was expected since most of the surveyed persons were women. However, there were much more men in the “Computing or tech” industry. This may be why men make more money (on average) than women. Let’s investigate further.

<pre><code class="language-python">
# Bar chart of the top 10 industries by the gender of employees

df_top_inds = df[df["Industry"].isin(filtered_industries)]
plt.figure(figsize=(12, 6))
sns.countplot(data=df_top_inds, x='Industry', hue='Gender')
plt.title('Distribution of Genders Across Different Industries')
plt.xlabel('Industry')
plt.ylabel('Count')
plt.xticks(rotation=45)
plt.legend(title='Gender')
plt.show()

</code></pre>

Our further investigation with pivot tables which displayed the percentage of men and women in the top 10 industries showed us that men have a higher median and mean salary than women in all the considered industries. Also, the percentage of men in “Computing or tech” was the greatest with 45.35% for men and 54.65% for women.

<pre><code class="language-python">
# New dataframe of only 'Men' and 'Women' in 'Gender' column of the top 10 industries
df_women_men_inds = df_top_inds[df['Gender'].isin(['Man', 'Woman'])]

# Create a pivot table
pivot_table_inds = df_women_men_inds.pivot_table(
    index='Industry',
    columns='Gender',
    values='Adjusted_Salary_USD',
    aggfunc=['mean', 'median', 'size']
)


# Flatten the multi-level columns
pivot_table_inds.columns = ['_'.join(col).strip() for col in pivot_table_inds.columns.values]
pivot_table_inds.reset_index(inplace=True)

# Calculate the proportions
pivot_table_inds['Total'] = pivot_table_inds['size_Man'].fillna(0) + pivot_table_inds['size_Woman'].fillna(0)
pivot_table_inds['Percentage_Man'] = ((pivot_table_inds['size_Man'] / pivot_table_inds['Total']) * 100).round(2)
pivot_table_inds['Percentage_Woman'] = ((pivot_table_inds['size_Woman'] / pivot_table_inds['Total']) * 100).round(2)
pivot_table_inds
# Select and rename columns for clarity
result_df = pivot_table_inds[['Industry', 'Percentage_Man', 'mean_Man', 'median_Man', 'Percentage_Woman', 'mean_Woman', 'median_Woman']]
result_df.columns = ['Industry', 'Percentage_Man', 'Average_Salary_Man', 'Median_Salary_Man',
                     'Percentage_Woman', 'Average_Salary_Woman', 'Median_Salary_Woman']

# Display the DataFrame
result_df

</code></pre>

The last bivariate analysis plot was a bar chart of “Highest_Education” against all ages.

<pre><code class="language-python">
# Bar chart of highest education across all age categories

plt.figure(figsize=(10, 6))
sns.countplot(x="Age", hue="Highest_Education", order=age_order, data=df)
plt.ylabel("Count")
plt.title("Distribution of Highest Education Across Age Categories")
plt.show()

</code></pre>

Then we concluded by answering two basic questions with our dataset. First, the highest-paid male German employee earns $935,000 per year. Then the average salary of women between the ages of “25-34” in California is $107,372.63. 

<pre><code class="language-python">
# Highest-paid man in Germany

german_men = df[(df["Country"] == "Germany") & (df["Gender"] == "Man")]
german_men.loc[german_men["Adjusted_Salary_USD"].idxmax()]

</code></pre>

<pre><code class="language-python">
# Mean Salary of Women between the ages of 25 and 34 in California

cali_women = df[(df["US_State"] == "California") & (df["Age"] == "25-34")]
cali_women["Adjusted_Salary_USD"].mean().round(3)

</code></pre>

Thank you again for your time and I would be posting more soon, so stay tuned. You can get the dataset and code from my <a href="https://colab.research.google.com/drive/1H4lWUwTxGdPv6EEq5H-eTPlGQ77-ETPV?usp=sharing" targert="_blank" class="link">Colab</a> or my <a href="https://github.com/jkazalekor/Comprehensive-analysis-global-survey" targert="_blank" class="link">Github</a>.

