# Big Sugar
By Sahith Cherumandanda and Kavya Sriram

## Introduction
This dataset contains information on recipes from 'food.com'. It contains over 80,000 recipes with data about a recipe's nutrition values (calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)), the number of ingredients needed, and it's average rating (1.0-5.0), among other values. The average rating was calculated using a separate dataset containing reviews and ratings for different recipes. 

In analyzing this dataset, a natural question that arises is as follows: Do recipes with higher-than-average sugar content have higher ratings?

Answering the above question using this dataset has immediate consequences. If the answer is 'yes', when searching for reputable recipes going forward, bear in mind that highly rated recipes tend to be sweeter on average.

## Cleaning and EDA
#### Data Cleaning
The following columns were cleaned: 'submitted', 'tags', 'nutrition', 'steps', and 'ingredients'. Each value in these columns were likely entered into one text box; thus, the content would not have been automatically organized as a list. The 'submitted' column contained the date of when a recipe was added to 'food.com'. Each date was stored as a `string`. To clean the column, we changed the type of each date to pandas `datetime` object. Each value in the 'tags', 'nutrition', 'steps', and 'ingredients' columns was stored as a `string` that resembled a `list`. To clean each of those columns, we stripped the unnecessary quotation marks and turned each `string` into a `list` of the required type (`int` for 'nutrition' and `string` for the rest). Finally, we added a column to the DataFrame, called 'sugar', consisting solely of the third value of each list in the 'nutrition' column, i.e. a recipe's sugar content.

Though several of the cleaned columns are ignored in our analysis, we chose clean them all the same for the sake of uniformity. That being said, cleaning 'submitted' and 'nutrition' was necessary for our analysis, as information contained in those columns become relevant later.

In the below slice of the DataFrame, we have dropped the 'id', 'contributor_id', and 'steps' as they have little relevance to our analysis. These are the top 5 rows of the DataFrame.

| name                                 |   minutes | submitted   | nutrition                                     |   n_steps |   n_ingredients |   rating |   sugar |
|:-------------------------------------|----------:|:------------|:----------------------------------------------|----------:|----------------:|---------:|--------:|
| 1 brownies in the world    best ever |        40 | 2008-10-27  | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 |               9 |        4 |      50 |
| 1 in canada chocolate chip cookies   |        45 | 2011-04-11  | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 |              11 |        5 |     211 |
| 412 broccoli casserole               |        40 | 2008-05-30  | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 |               9 |        5 |       6 |
| millionaire pound cake               |       120 | 2008-02-12  | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 |               7 |        5 |     326 |
| 2000 meatloaf                        |        90 | 2012-03-06  | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 |              13 |        5 |      12 |

#### Univariate Analysis
Observe the descriptive statistics of the 'minutes' column below.

|       |        minutes |
|:------|---------------:|
| count | 83782          |
| mean  |   115.031      |
| std   |  3990.87       |
| min   |     0          |
| 25%   |    20          |
| 50%   |    35          |
| 75%   |    65          |
| max   |     1.0512e+06 |

Notice that the minimum value is 0 and the maximum value is over 1,000,000, both of which are unrealistic values. As such, in the below histogram plotting the frequency of values in the 'minutes' column, we chose to restrict the the column values to the middle 98th-percentile to exclude unreasonable values.

The values are binned into 50 groups to make the plot more viewer friendly. The histogram describes an expected trend: the majority of recipes are cooked under 2 hours, and few recipes exceed 5 hours of cooking time.

<iframe src="assets/cooking-time-hist.html" width=800 height=600 frameBorder=0></iframe>

Additionally, observe the below histogram describing the 'rating' column. Notice, the stark difference between the amount of highly-rated recipes versus otherwise. 

<iframe src="assets/ratings-hist.html" width=800 height=600 frameBorder=0></iframe>

#### Bivariate Analysis
The following scatterplot describes the relationship between the values in a given recipe's 'rating' and 'minutes' columns. The top left of the plot is densely populated, and patters out as we move to the bottom right. This marks a negative correlation between a ratings and minutes; as the time to cook increases, fewer recipes are likely to be rated highly.

<iframe src="assets/rating-minutes-hist.html" width=800 height=600 frameBorder=0></iframe>

#### Interesting Aggregates
We cleaned the 'rating' column such that each rating was rounded to the first decimal place. Next, by grouping by rating and taking the median of the 'sugar' column, we generated the below DataFrame. We took the median as the distribution of the 'sugar' column is quite skewed right. Below are the top 10 rows (containing recipes rated above 4). This table is significant as the top row has the highest value in the 'sugar' column.

|   rating |   sugar |
|---------:|--------:|
|      5.0 |      24 |
|      4.9 |      20 |
|      4.8 |      21 |
|      4.7 |      20 |
|      4.6 |      21 |
|      4.5 |      22 |
|      4.4 |      21 |
|      4.3 |      23 |
|      4.2 |      23 |
|      4.1 |      23 |

## Assessment of Missingness
#### NMAR Analysis
We believe the missingess of the 'description' is NMAR (Not Missing At Random). We believe that the contributors of recipes simply chose not add a description for the recipe. This could be because a description would have been too long, or if the contributor believed the recipe needed no introduction as it was a common recipe.

#### Missingness Dependency
We chose to analyze the non-trivial missingness of the 'rating' column. We compared the 'n_ingredients' column depending on whether a rating was missing or not. The distribution is plotted below. The red hue represents the distribution of the 'n_ingredients' column when rating is missing, the blue hue represents the distribution of the 'n_ingredients' column when rating is not missing.

<iframe src="assets/rating-missing-n-ingredients-hist.html" width=800 height=600 frameBorder=0></iframe>

We conducted a permutation test to analyze the missingness of 'rating' based on 'n_ingredients' using the Kolmogorov-Smirnov statistic. The p-value was approximately 0.11. Thus, we conclude that the 'rating' column's missingness is likely not dependent on the 'n_ingredients' column. This has little effect on our hypothesis testing.

Similarily, we compared the missingness of the 'rating' column with the year each recipe was submitted. The distribution is plotted below, and the color scheme follows that of the plot above.

<iframe src="assets/rating-missing-year-hist.html" width=800 height=600 frameBorder=0></iframe>

We conducted a permutation test to analyze the missingness of 'rating' based on 'n_ingredients' using the total variation distance statistic. The p-value was 0.0. Thus, we conclude that the 'rating' column's missingness is likely dependent on the year from the 'submitted' column. Again, this has little effect on our hypothesis testing.

## Hypothesis Testing
Question (Restated): Do recipes with higher-than-average sugar content have higher ratings?

**Null Hypothesis**: Recipes with higher-than-average sugar content have the same average rating as the average rating of all recipes in the dataset.
**Alternative Hypothesis**: Recipes with higher-than-average sugar content have a higher average rating than the average rating of all recipes in the dataset.

We chose the mean rating as our test statistic (the distribution is numerical) and a 0.05 significance level. Moreover, we chose to restrict the dataset to the bottom 97th percentile of values in the 'sugar' column to exclude unreasonably high values. After repeatedly sampling portions of the dataset, calculating the average rating of the sample, and comparing our observed statistic to the simulated statistics, our resulting p-value was 0.01. Below is a plot of the simulated and observed test statistics (red bar).

<iframe src="assets/test-stat-distribution.html" width=800 height=600 frameBorder=0></iframe>

Thus, we reject the null hypothesis and conclude that the fact that recipes with higher-than-average sugar content have a higher average rating than the average rating of all recipes in the dataset is not due to random chance.