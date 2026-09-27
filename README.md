# Social Media Engagement Analytics Using Python

---

# 1. Project Overview

Social media platforms generate large amounts of engagement data such as likes, comments, shares, impressions, watch time, follower count, and engagement rate.

The objective of this project is to analyze social media engagement data using Python to understand user behavior, identify engagement trends, compare content performance, and generate useful insights.

The project covers:

- Data Import and Setup
- Data Cleaning
- Data Formatting
- Exploratory Data Analysis (EDA)
- Data Wrangling
- Statistical Analysis
- Data Visualization
- Content Performance Analysis
- User Trend Analysis
- Behavioral Analysis
- Sentiment Analysis
- Final Insights

---

# 2. Dataset

**Dataset Name:** `social_media_engagement_5000.csv`

The original dataset contains:

```text
5000 rows
19 columns
```

## Dataset Columns

- `user_id`
- `age`
- `gender`
- `country`
- `post_id`
- `post_type`
- `post_category`
- `likes`
- `comments`
- `shares`
- `watch_time_sec`
- `impression_count`
- `posted_at`
- `follower_count`
- `is_verified`
- `device_type`
- `sentiment`
- `hashtags`
- `engagement_rate`

---

# 3. Libraries Used

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
```

## Explanation

- **Pandas** is used for loading, cleaning, transforming, and analyzing data.
- **Matplotlib** is used for basic data visualization.
- **Seaborn** is used for statistical data visualization.
- **Plotly Express** is used to create interactive visualizations.

---

# Task 1 – Data Import and Setup

## 1.1 Import the Dataset

```python
import pandas as pd

df = pd.read_csv("social_media_engagement_5000.csv")

df.head()
```

### Output

The first 5 rows of the dataset are displayed.

### Explanation

`pd.read_csv()` loads the CSV file into a Pandas DataFrame called `df`.

`head()` is used to display the first five records and verify that the dataset has been loaded correctly.

---

## 1.2 Check Dataset Shape

```python
df.shape
```

### Output

```text
(5000, 19)
```

### Explanation

The original dataset contains **5000 rows and 19 columns**.

---

## 1.3 Check Column Names

```python
df.columns
```

### Output

```text
Index(['user_id', 'age', 'gender', 'country', 'post_id', 'post_type',
       'post_category', 'likes', 'comments', 'shares', 'watch_time_sec',
       'impression_count', 'posted_at', 'follower_count', 'is_verified',
       'device_type', 'sentiment', 'hashtags', 'engagement_rate'],
      dtype='object')
```

### Explanation

`df.columns` displays all the column names available in the dataset.

---

## 1.4 Check Data Types

```python
df.dtypes
```

### Output

```text
user_id               int64
age                  float64
gender                object
country               object
post_id                int64
post_type             object
post_category         object
likes                float64
comments             float64
shares               float64
watch_time_sec         int64
impression_count       int64
posted_at             object
follower_count         int64
is_verified             bool
device_type           object
sentiment             object
hashtags              object
engagement_rate      float64
dtype: object
```

### Explanation

`dtypes` is used to check the data type of each column.

The `posted_at` column is initially stored as an object and needs to be converted into datetime format.

---

## 1.5 Convert Date Column to Datetime

```python
df['posted_at'] = pd.to_datetime(
    df['posted_at'],
    dayfirst=True
)

df['posted_at'].dtype
```

### Output

```text
datetime64[us]
```

### Explanation

The `posted_at` column is converted from object/string format into datetime format.

`dayfirst=True` is used because the dates in the dataset follow a day-first format.

---

# Task 2 – Data Cleaning

## 2.1 Check Missing Values

```python
df.isnull().sum()
```

### Output

The number of missing values in each column is displayed.

### Explanation

`isnull()` identifies missing values and `sum()` calculates the total number of missing values in each column.

---

## 2.2 Handle Missing Numerical Values

```python
df['age'] = df['age'].fillna(df['age'].median())
df['likes'] = df['likes'].fillna(df['likes'].median())
df['comments'] = df['comments'].fillna(df['comments'].median())
df['shares'] = df['shares'].fillna(df['shares'].median())
```

### Output

No direct output is produced because the missing values are replaced inside the DataFrame.

### Explanation

Missing numerical values are replaced using the **median** of the corresponding column.

Median is useful because it is less affected by unusually high or low values.

---

## 2.3 Handle Missing Categorical Values

```python
df['gender'] = df['gender'].fillna(
    df['gender'].mode()[0]
)

df['sentiment'] = df['sentiment'].fillna(
    df['sentiment'].mode()[0]
)
```

### Output

No direct output is produced.

### Explanation

Missing categorical values are filled using the **mode**, which represents the most frequently occurring value.

---

## 2.4 Verify Missing Values

```python
df.isnull().sum()
```

### Output

After cleaning, all columns show `0` missing values.

### Explanation

This confirms that the missing values have been handled successfully.

---

## 2.5 Identify Duplicate Records

```python
df.duplicated().sum()
```

### Output

```text
0
```

### Explanation

There are no duplicate records in the dataset.

---

## 2.6 Remove Duplicate Records

```python
df = df.drop_duplicates()

df.shape
```

### Output

```text
(5000, 19)
```

### Explanation

`drop_duplicates()` removes duplicate records if they exist.

Since the dataset contains no duplicate rows, the number of records remains 5000.

---

## 2.7 Check Gender Categories

```python
df['gender'].value_counts()
```

### Output

```text
gender
Male      1849
Other     1581
Female    1570
Name: count, dtype: int64
```

---

## 2.8 Standardize Gender Values

```python
df['gender'] = (
    df['gender']
    .str.strip()
    .str.title()
)

df['gender'].value_counts()
```

### Output

```text
gender
Male      1849
Other     1581
Female    1570
Name: count, dtype: int64
```

### Explanation

`str.strip()` removes unwanted spaces.

`str.title()` converts the values into a consistent title-case format.

---

## 2.9 Fix Incorrect Data Types

```python
df['likes'] = df['likes'].astype(int)
df['comments'] = df['comments'].astype(int)
df['shares'] = df['shares'].astype(int)

df[['likes', 'comments', 'shares']].dtypes
```

### Output

```text
likes       int64
comments    int64
shares      int64
dtype: object
```

### Explanation

Likes, comments, and shares represent count values.

Therefore, they are converted from floating-point values to integers.

---

## 2.10 Check Unrealistic Values

```python
df[['likes', 'comments', 'shares']].agg(
    ['min', 'max']
)
```

### Output

```text
       likes  comments  shares
min     10.0       0.0     0.0
max  19998.0    2999.0  1999.0
```

Check for negative values:

```python
(df[['likes', 'comments', 'shares']] < 0).sum()
```

### Output

```text
likes       0
comments    0
shares      0
dtype: int64
```

### Explanation

No negative engagement values are present in the dataset.

---

## 2.11 Create Hashtag Count

```python
df['hashtag_count'] = df['hashtags'].fillna('').apply(
    lambda x: len(x.split()) if x != '' else 0
)

df[['hashtags', 'hashtag_count']].head()
```

### Output

The first five hashtag values and their corresponding hashtag counts are displayed.

### Explanation

A new column called `hashtag_count` is created.

It represents the number of hashtags used in each post.

---

## 2.12 Check Sentiment Categories

```python
df['sentiment'].value_counts()
```

### Output

```text
sentiment
positive    2513
neutral     1527
negative     960
Name: count, dtype: int64
```

---

## 2.13 Standardize Sentiment Labels

```python
df['sentiment'] = (
    df['sentiment']
    .str.strip()
    .str.lower()
)

df['sentiment'].value_counts()
```

### Output

```text
sentiment
positive    2513
neutral     1527
negative     960
Name: count, dtype: int64
```

### Explanation

Sentiment labels are converted into lowercase and unnecessary spaces are removed to maintain a consistent format.

---

## 2.14 Final Missing Value Check

```python
df.isnull().sum()
```

### Output

All columns contain `0` missing values after data cleaning.

---

# Task 3 – Exploratory Data Analysis

## 3.1 Display First Five Rows

```python
df.head()
```

### Output

Displays the first five records of the cleaned dataset.

---

## 3.2 Display Last Five Rows

```python
df.tail()
```

### Output

Displays the last five records of the cleaned dataset.

---

## 3.3 Check Dataset Shape

```python
df.shape
```

### Output

```text
(5000, 20)
```

### Explanation

The number of columns increased from 19 to 20 because the new `hashtag_count` column was created.

---

## 3.4 Display Column Names

```python
df.columns
```

### Output

Displays all current columns, including `hashtag_count`.

---

## 3.5 Dataset Information

```python
df.info()
```

### Output

The output displays:

- Column names
- Number of non-null values
- Data types
- Memory usage

### Explanation

`info()` provides a quick structural summary of the DataFrame.

---

## 3.6 Check Data Types

```python
df.dtypes
```

### Output

Displays the current data type of every column.

---

## 3.7 Descriptive Statistics

```python
df.describe()
```

### Output

The output includes:

- Count
- Mean
- Standard deviation
- Minimum
- 25th percentile
- Median
- 75th percentile
- Maximum

### Explanation

`describe()` provides a statistical summary of the numerical columns.

---

## 3.8 Post Type Frequency

```python
df['post_type'].value_counts()
```

### Output

Displays the number of posts belonging to each post type.

---

## 3.9 Unique Post Types

```python
df['post_type'].unique()
```

### Output

Displays all unique post types available in the dataset.

---

## 3.10 Number of Unique Post Types

```python
df['post_type'].nunique()
```

### Output

Displays the total number of unique post types.

---

## 3.11 Correlation Analysis

```python
numeric_df = df.select_dtypes(include='number')

correlation_matrix = numeric_df.corr()

correlation_matrix
```

### Output

A correlation matrix containing all numerical variables is displayed.

### Explanation

Correlation measures the linear relationship between numerical variables.

- Values closer to `1` indicate a strong positive relationship.
- Values closer to `-1` indicate a strong negative relationship.
- Values close to `0` indicate a weak linear relationship.

---

## 3.12 Average Likes by Post Type

```python
df.groupby('post_type')['likes'].mean()
```

### Output

Displays the average number of likes for each post type.

### Explanation

`groupby()` groups the records according to post type, while `mean()` calculates the average number of likes.

---

## 3.13 Average Impressions by Country

```python
df.groupby('country')['impression_count'].mean()
```

### Output

Displays the average impression count for each country.

---

# Task 4 – Data Wrangling

## 4.1 Create Engagement Score

```python
df['engagement_score'] = (
    df['likes']
    + df['comments']
    + df['shares']
)

df[
    ['likes', 'comments', 'shares', 'engagement_score']
].head()
```

### Output

The first five rows containing likes, comments, shares, and engagement score are displayed.

### Explanation

A new feature called `engagement_score` is created by adding:

```text
Likes + Comments + Shares
```

This provides a simple combined measure of user interactions.

---

## 4.2 Check Updated Columns

```python
df.columns
```

### Output

The DataFrame now contains **21 columns**, including:

```text
hashtag_count
engagement_score
```

---

## 4.3 Average Engagement Score by Post Type

```python
df.groupby(
    'post_type'
)['engagement_score'].mean()
```

### Output

Displays the average engagement score for each post type.

---

## 4.4 Average Engagement Rate by Country

```python
df.groupby(
    'country'
)['engagement_rate'].mean()
```

### Output

Displays the average engagement rate for each country.

---

## 4.5 Average Engagement Score by Sentiment

```python
df.groupby(
    'sentiment'
)['engagement_score'].mean()
```

### Output

Displays the average engagement score for positive, neutral, and negative posts.

---

## 4.6 Split the Dataset

```python
df_part1 = df.iloc[:2500]
df_part2 = df.iloc[2500:]

print(df_part1.shape)
print(df_part2.shape)
```

### Output

```text
(2500, 21)
(2500, 21)
```

### Explanation

The dataset is divided into two equal parts to demonstrate DataFrame combination.

---

## 4.7 Combine DataFrames Using Concat

```python
combined_df = pd.concat(
    [df_part1, df_part2]
)

combined_df.shape
```

### Output

```text
(5000, 21)
```

### Explanation

`pd.concat()` combines the two DataFrames back into one DataFrame.

---

# Task 5 – Statistical Analysis

## 5.1 Select Numerical Columns

```python
stats_columns = [
    'likes',
    'comments',
    'shares',
    'watch_time_sec',
    'engagement_rate',
    'follower_count'
]

stats_df = df[stats_columns]

stats_df.head()
```

### Output

Displays the first five rows of the selected numerical variables.

---

## 5.2 Mean

```python
stats_df.mean()
```

### Output

Displays the arithmetic mean of each selected numerical variable.

### Explanation

Mean represents the average value.

---

## 5.3 Median

```python
stats_df.median()
```

### Output

Displays the median of each selected numerical variable.

### Explanation

Median represents the middle value after arranging the observations in order.

---

## 5.4 Mode

```python
stats_df.mode().head(1)
```

### Output

Displays the most frequently occurring value for each selected variable.

### Explanation

Mode represents the value that appears most frequently.

---

## 5.5 Standard Deviation

```python
stats_df.std()
```

### Output

Displays the standard deviation of each selected variable.

### Explanation

Standard deviation measures how much the observations vary from their average value.

---

## 5.6 Variance

```python
stats_df.var()
```

### Output

Displays the variance of each selected numerical variable.

### Explanation

Variance measures the spread of values around their mean.

---

## 5.7 Percentiles

```python
stats_df.quantile(
    [0.25, 0.50, 0.75]
)
```

### Output

Displays:

```text
25th percentile
50th percentile
75th percentile
```

### Explanation

Percentiles help understand how the values are distributed across the dataset.

---

## 5.8 Skewness

```python
stats_df.skew()
```

### Output

Displays the skewness of each selected variable.

### Explanation

Skewness indicates whether the distribution is symmetric or tilted towards one side.

---

## 5.9 Kurtosis

```python
stats_df.kurt()
```

### Output

Displays the kurtosis of each selected variable.

### Explanation

Kurtosis provides information about the shape and tails of a distribution.

---

# Task 6 – Data Visualization

The project contains more than the required minimum of **8 visualizations**.

The visualizations are created using:

- Matplotlib
- Seaborn
- Plotly

---

## 6.1 Matplotlib – Scatter Plot: Likes vs Impressions

```python
plt.scatter(
    df['likes'],
    df['impression_count']
)

plt.xlabel('Likes')
plt.ylabel('Impressions')
plt.title('Likes vs Impressions')

plt.show()
```

### Output

A scatter plot showing the relationship between likes and impressions is displayed.

### Explanation

Each point represents a post.

The plot helps identify whether posts with more likes also tend to receive more impressions.

---

## 6.2 Matplotlib – Line Chart: Daily Engagement Trend

```python
daily_engagement = df.groupby(
    df['posted_at'].dt.date
)['engagement_rate'].mean()

plt.plot(
    daily_engagement.index,
    daily_engagement.values
)

plt.xlabel('Date')
plt.ylabel('Average Engagement Rate')
plt.title('Daily Engagement Trend')
plt.xticks(rotation=45)

plt.show()
```

### Output

A line chart showing the daily average engagement rate is displayed.

### Explanation

The line chart helps identify how engagement changes across different dates.

---

## 6.3 Matplotlib – Bar Chart: Posts by Category

```python
category_counts = (
    df['post_category'].value_counts()
)

category_counts.plot(kind='bar')

plt.xlabel('Post Category')
plt.ylabel('Number of Posts')
plt.title('Posts by Category')
plt.xticks(rotation=45)

plt.show()
```

### Output

A bar chart showing the number of posts in each content category is displayed.

### Explanation

The chart makes it easy to compare the frequency of different content categories.

---

## 6.4 Matplotlib – Pie Chart: Gender Distribution

```python
gender_counts = df['gender'].value_counts()

plt.pie(
    gender_counts,
    labels=gender_counts.index,
    autopct='%1.1f%%'
)

plt.title('Gender Distribution')

plt.show()
```

### Output

A pie chart showing the percentage distribution of users by gender is displayed.

### Explanation

The pie chart represents each gender category as a percentage of the total dataset.

---

## 6.5 Matplotlib – Histogram: Age

```python
plt.hist(
    df['age'],
    bins=10,
    edgecolor='black'
)

plt.xlabel('Age')
plt.ylabel('Frequency')
plt.title('Age Distribution')

plt.show()
```

### Output

A histogram showing the distribution of user ages is displayed.

### Explanation

The histogram groups age values into ranges and shows how frequently users fall into each range.

---

## 6.6 Matplotlib – Box Plot: Engagement Rate

```python
plt.boxplot(
    df['engagement_rate']
)

plt.ylabel('Engagement Rate')
plt.title('Engagement Rate Distribution')

plt.show()
```

### Output

A box plot showing the distribution of engagement rate is displayed.

### Explanation

The box plot helps identify:

- Median
- Data spread
- Quartiles
- Possible outliers

---

## 6.7 Seaborn – Count Plot: Post Type

```python
sns.countplot(
    x='post_type',
    data=df
)

plt.xlabel('Post Type')
plt.ylabel('Count')
plt.title('Post Type Distribution')
plt.xticks(rotation=45)

plt.show()
```

### Output

A count plot showing the number of posts belonging to each post type is displayed.

---

## 6.8 Seaborn – Bar Plot: Average Likes by Category

```python
sns.barplot(
    x='post_category',
    y='likes',
    data=df
)

plt.xlabel('Post Category')
plt.ylabel('Average Likes')
plt.title('Average Likes by Content Category')
plt.xticks(rotation=45)

plt.show()
```

### Output

A bar plot comparing average likes across content categories is displayed.

### Explanation

The plot helps compare which content categories receive more likes on average.

---

## 6.9 Seaborn – Violin Plot: Followers vs Sentiment

```python
sns.violinplot(
    x='sentiment',
    y='follower_count',
    data=df
)

plt.xlabel('Sentiment')
plt.ylabel('Follower Count')
plt.title('Followers vs Sentiment')

plt.show()
```

### Output

A violin plot showing follower-count distribution across sentiment categories is displayed.

### Explanation

The plot compares the distribution and density of follower counts for positive, neutral, and negative sentiment posts.

---

## 6.10 Seaborn – Pair Plot: Numeric Features

```python
pair_data = df[
    [
        'likes',
        'comments',
        'shares',
        'engagement_rate'
    ]
]

sns.pairplot(pair_data)

plt.show()
```

### Output

A pair plot showing relationships between the selected numerical features is displayed.

### Explanation

The pair plot allows multiple numerical variables to be compared at the same time.

It shows both individual distributions and relationships between variables.

---

## 6.11 Seaborn – Correlation Heatmap

```python
plt.figure(figsize=(10, 6))

sns.heatmap(
    correlation_matrix,
    cmap='coolwarm'
)

plt.title('Correlation Matrix')

plt.show()
```

### Output

A heatmap showing correlations between numerical variables is displayed.

### Explanation

The heatmap visually represents the strength and direction of relationships between numerical variables.

---

## 6.12 Seaborn – Swarm Plot: Engagement vs Device

```python
sample_df = df.sample(
    500,
    random_state=1
)

sns.swarmplot(
    x='device_type',
    y='engagement_rate',
    data=sample_df
)

plt.xlabel('Device Type')
plt.ylabel('Engagement Rate')
plt.title('Engagement Rate vs Device Type')

plt.show()
```

### Output

A swarm plot showing engagement rate across different device types is displayed.

### Explanation

A sample of 500 records is used because plotting all 5000 records could make the swarm plot overcrowded.

The visualization helps compare engagement-rate distributions across different devices.

---

## 6.13 Plotly – Interactive Scatter Plot

```python
fig = px.scatter(
    df,
    x='impression_count',
    y='likes',
    color='post_type',
    title='Interactive Likes vs Impressions'
)

fig.show()
```

### Output

An interactive scatter plot is displayed.

The visualization allows users to:

- Hover over individual points
- Zoom in and out
- Pan across the chart
- Compare different post types

### Explanation

Plotly provides interactive visualizations that make it easier to explore individual observations in the dataset.

---

# Task 7 – Final Insights

The final analysis focuses on four main areas:

1. Content Performance
2. User Trends
3. Behavioral Insights
4. Sentiment Analysis

---

# 7.1 Content Performance

## Which Post Types Have the Highest Engagement?

```python
post_engagement = (
    df.groupby('post_type')['engagement_rate']
    .mean()
    .sort_values(ascending=False)
)

post_engagement
```

### Output

```text
post_type
video    1.122365
text     1.064549
image    0.895646
reel     0.783047
Name: engagement_rate, dtype: float64
```

### Insight

Video posts have the **highest average engagement rate of 1.122365**.

The results are:

- Video – **1.122365**
- Text – **1.064549**
- Image – **0.895646**
- Reel – **0.783047**

Therefore, **video content has the highest average engagement rate in this dataset**.

Text posts are the second-highest performing post type, while reels have the lowest average engagement rate.

---

## Best-Performing Content Category

```python
category_engagement = (
    df.groupby(
        'post_category'
    )['engagement_rate']
    .mean()
    .sort_values(ascending=False)
)

category_engagement
```

### Output

```text
post_category
food         1.358628
tech         1.160475
lifestyle    1.091019
music        0.888803
fitness      0.865628
education    0.844222
fashion      0.807109
travel       0.702223
Name: engagement_rate, dtype: float64
```

### Insight

The **Food** category has the highest average engagement rate at **1.358628**.

The ranking is:

- Food – **1.358628**
- Tech – **1.160475**
- Lifestyle – **1.091019**
- Music – **0.888803**
- Fitness – **0.865628**
- Education – **0.844222**
- Fashion – **0.807109**
- Travel – **0.702223**

Therefore, **Food is the best-performing content category based on average engagement rate**.

Travel has the lowest average engagement rate among the available content categories.

---

## Which Countries Have the Highest Average Engagement Rate?

```python
country_engagement = (
    df.groupby(
        'country'
    )['engagement_rate']
    .mean()
    .sort_values(ascending=False)
)

country_engagement
```

### Output

```text
country
Brazil       1.540704
Australia    1.324339
France       1.146402
UAE          1.112352
Canada       0.916659
UK           0.850966
Japan        0.769623
Germany      0.759000
India        0.655005
USA          0.576580
Name: engagement_rate, dtype: float64
```

### Insight

**Brazil** has the highest average engagement rate at **1.540704**.

The top-performing countries are:

- Brazil – **1.540704**
- Australia – **1.324339**
- France – **1.146402**
- UAE – **1.112352**

The USA has the lowest average engagement rate in this comparison at **0.576580**.

Therefore, **Brazil shows the highest average engagement performance among the countries in the dataset**.

---

# 7.2 User Trends

## How Age Affects Engagement

```python
age_engagement = (
    df.groupby(
        'age'
    )['engagement_rate']
    .mean()
)

age_engagement
```

### Output

The average engagement rate for each age is displayed.

To measure the overall relationship between age and engagement rate:

```python
df[
    ['age', 'engagement_rate']
].corr()
```

### Output

```text
                       age  engagement_rate
age               1.000000         0.008039
engagement_rate   0.008039         1.000000
```

### Insight

The correlation between age and engagement rate is approximately:

```text
0.008039
```

This value is extremely close to zero.

Therefore, there is **almost no linear relationship between age and engagement rate** in this dataset.

This suggests that age does not have a meaningful linear effect on engagement rate based on the available data.

---

## Performance Difference for Verified Accounts

```python
verified_performance = (
    df.groupby(
        'is_verified'
    )['engagement_rate']
    .mean()
)

verified_performance
```

### Output

```text
is_verified
False    0.954744
True     1.054250
Name: engagement_rate, dtype: float64
```

### Insight

Verified accounts have an average engagement rate of:

```text
1.054250
```

Non-verified accounts have an average engagement rate of:

```text
0.954744
```

The difference is approximately:

```text
1.054250 - 0.954744 = 0.099506
```

Therefore, **verified accounts have a higher average engagement rate than non-verified accounts** in this dataset.

---

# 7.3 Behavioral Insights

## Best Time of Day for Impressions

First, the posting hour was extracted from the `posted_at` column.

```python
df['post_hour'] = df['posted_at'].dt.hour

df['post_hour'].value_counts().sort_index()
```

### Output

```text
post_hour
0    5000
Name: count, dtype: int64
```

### Explanation

All **5000 records have a `post_hour` value of 0**.

This indicates that the dataset contains date information but does not contain different posting times throughout the day.

When a date without a time component is converted to datetime format, Python represents the missing time as:

```text
00:00:00
```

Therefore, every record receives an hour value of `0`.

The average impressions for the available hour can also be calculated:

```python
hourly_impressions = (
    df.groupby(
        'post_hour'
    )['impression_count']
    .mean()
)

hourly_impressions
```

### Output

```text
post_hour
0    50013.7328
Name: impression_count, dtype: float64
```

### Insight

The average impression count associated with hour `0` is **50,013.7328**.

However, this **does not mean that midnight is the best posting time**.

Since all 5000 records have the same hour value, there is no time-of-day variation available for comparison.

Therefore, **the best time of day for impressions cannot be determined from this dataset**.

This is a limitation of the available timestamp data.

---

## Device Type Impact on Watch Time

```python
device_watch_time = (
    df.groupby(
        'device_type'
    )['watch_time_sec']
    .mean()
    .sort_values(ascending=False)
)

device_watch_time
```

### Output

```text
device_type
mobile     4087.830760
tablet     3979.736429
desktop    3974.792521
Name: watch_time_sec, dtype: float64
```

### Insight

Mobile users have the highest average watch time at approximately:

```text
4087.83 seconds
```

The results are:

- Mobile – **4087.83 seconds**
- Tablet – **3979.74 seconds**
- Desktop – **3974.79 seconds**

Therefore, **mobile devices are associated with the highest average watch time in this dataset**.

Tablet and desktop users show relatively similar average watch times.

---

# 7.4 Sentiment Analysis

## Which Sentiment Performs Best?

```python
sentiment_performance = (
    df.groupby(
        'sentiment'
    )['engagement_rate']
    .mean()
    .sort_values(ascending=False)
)

sentiment_performance
```

### Output

```text
sentiment
negative    1.038486
neutral     0.991170
positive    0.919744
Name: engagement_rate, dtype: float64
```

### Insight

Negative sentiment posts have the highest average engagement rate at:

```text
1.038486
```

The results are:

- Negative – **1.038486**
- Neutral – **0.991170**
- Positive – **0.919744**

Therefore, **negative sentiment posts generated the highest average engagement rate in this dataset**.

This result does not necessarily mean that negative content is better overall.

It only shows that negative posts received a higher average engagement rate within this particular dataset.

---

## Behavior of Negative and Neutral Sentiment Posts

```python
sentiment_behavior = df.groupby(
    'sentiment'
)[
    [
        'likes',
        'comments',
        'shares',
        'watch_time_sec',
        'engagement_rate'
    ]
].mean()

sentiment_behavior
```

### Output

| Sentiment | Likes | Comments | Shares | Watch Time (sec) | Engagement Rate |
|---|---:|---:|---:|---:|---:|
| Negative | 10208.081250 | 1516.094792 | 1007.307292 | 4054.639583 | 1.038486 |
| Neutral | 9923.180092 | 1528.404060 | 996.565160 | 4031.823183 | 0.991170 |
| Positive | 10180.046956 | 1480.650617 | 1005.086749 | 3988.646240 | 0.919744 |

### Insight

Negative sentiment posts have:

- Highest average likes – **10,208.08**
- Highest average shares – **1,007.31**
- Highest average watch time – **4,054.64 seconds**
- Highest average engagement rate – **1.038486**

Neutral sentiment posts have the highest average number of comments:

```text
1528.40 comments
```

Positive posts have an average engagement rate of:

```text
0.919744
```

which is the lowest among the three sentiment groups.

Therefore, **negative posts show stronger overall engagement and watch time, while neutral posts generate slightly more comments on average**.

---

# 8. Final Conclusion

The Social Media Engagement Analytics project successfully analyzed **5000 social media records** using Python.

The dataset was cleaned, transformed, explored, statistically analyzed, and visualized using Pandas, Matplotlib, Seaborn, and Plotly.

## Content Performance

Video posts recorded the highest average engagement rate at **1.122365**, followed by text posts at **1.064549**.

The **Food** category was the best-performing content category with an average engagement rate of **1.358628**.

Among the countries analyzed, **Brazil** recorded the highest average engagement rate at **1.540704**.

## User Trends

Age showed almost no linear relationship with engagement rate.

The correlation between age and engagement rate was only **0.008039**.

Verified accounts had a higher average engagement rate of **1.054250**, compared with **0.954744** for non-verified accounts.

## Behavioral Insights

Mobile users recorded the highest average watch time at approximately **4087.83 seconds**.

Tablet users recorded approximately **3979.74 seconds**, while desktop users recorded approximately **3974.79 seconds**.

For posting time, all **5000 records had a post hour of 0**.

Therefore, the dataset does not contain enough time-of-day variation to determine the best posting time for impressions.

The value at hour `0` should not be interpreted as evidence that midnight is the best posting time.

## Sentiment Analysis

Negative sentiment posts recorded the highest average engagement rate at **1.038486**.

Negative posts also recorded the highest average:

- Likes
- Shares
- Watch time
- Engagement rate

Neutral posts recorded the highest average number of comments at approximately **1528.40**.

Positive posts had the lowest average engagement rate among the three sentiment groups at **0.919744**.

## Overall Finding

The analysis shows that **post type, content category, country, account verification status, device type, and sentiment are associated with differences in social media engagement within this dataset**.

Age has almost no linear relationship with engagement rate.

The project also identified an important data limitation: the `posted_at` field does not contain useful time-of-day variation, so the best posting hour cannot be determined.

Overall, this project demonstrates how Python can be used to perform a complete social media data analysis workflow, from data cleaning and statistical analysis to visualization and insight generation.

---

# 9. Technologies Used

- Python
- Pandas
- Matplotlib
- Seaborn
- Plotly
- Jupyter Notebook

---

# 10. Project Structure

```text
Social-Media-Engagement-Analytics/
│
├── social_media_engagement_5000.csv
├── social_media_engagement_analysis.ipynb
├── README.md
└── requirements.txt
```

---

# 11. How to Run the Project

## Step 1 – Clone or Download the Repository

Download the project files and keep the dataset and notebook in the same folder.

---

## Step 2 – Install Required Libraries

```bash
pip install pandas matplotlib seaborn plotly jupyter
```

---

## Step 3 – Start Jupyter Notebook

```bash
jupyter notebook
```

---

## Step 4 – Open the Analysis Notebook

Open:

```text
social_media_engagement_analysis.ipynb
```

Make sure the dataset:

```text
social_media_engagement_5000.csv
```

is available in the same folder.

Run the notebook cells from top to bottom.

---

# 12. Requirements

The `requirements.txt` file can contain:

```text
pandas
matplotlib
seaborn
plotly
jupyter
```

---

# 13. Key Skills Demonstrated

- Python Programming
- Data Importing
- Data Cleaning
- Missing Value Handling
- Duplicate Handling
- Data Type Conversion
- Feature Engineering
- Pandas Data Manipulation
- GroupBy Operations
- Descriptive Statistics
- Correlation Analysis
- Exploratory Data Analysis
- Matplotlib Visualization
- Seaborn Visualization
- Plotly Interactive Visualization
- Data Interpretation
- Insight Generation
