# Big Sugar
## Recipe Rating Analysis
Project for DSC 80 at UCSD
Sahith Cherumandanda and Kavya Sriram

## Introduction
This dataset contains information on recipes from 'food.com'. It contains over 80,000 recipes with data about a recipe's nutrition values (calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)), the number of ingredients needed, and it's average rating (1.0-5.0), among other values. The average rating was calculated using a separate dataset containing reviews and ratings for different recipes. 

In analyzing this dataset, a natural question that arises is as follows: Do recipes with higher-than-average sugar content have higher-than-average ratings?

Answering the above question using this dataset has immediate consequences. If the answer is 'yes', when searching for reputable recipes going forward, bear in mind that highly rated recipes tend to be sweeter on average.

## Cleaning and EDA
The following columns were cleaned: 'submitted', 'tags', 'nutrition', 'steps', and 'ingredients'. The 'submitted' column contained the date of when a recipe was added to 'food.com'. Each date was stored as a *string*. To clean the column, we changed the type of each date to pandas *datetime* object. Each value in the 'tags', 'nutrition', 'steps', and 'ingredients' columns was stored as a *string* that resembled a *list*. To clean each of those columns, we stripped the unnecessary quotation marks and turned each *string* into a *list* of the required type (*int* for 'nutrition' and *string* for the rest). Finally, we added a column to the DataFrame, called 'sugar', consisting solely of the third value of each list in the 'nutrition' column, i.e. a recipe's sugar content.

In the below slice of the DataFrame, we have dropped the 'id', 'contributor_id', and 'steps' as they have little relevance to our analysis. These are the top 5 rows of the DataFrame

| name                                 |   minutes | submitted           | nutrition                                     |   n_steps |   n_ingredients |   rating |   sugar |
|:-------------------------------------|----------:|:--------------------|:----------------------------------------------|----------:|----------------:|---------:|--------:|
| 1 brownies in the world    best ever |        40 | 2008-10-27 00:00:00 | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 |               9 |        4 |      50 |
| 1 in canada chocolate chip cookies   |        45 | 2011-04-11 00:00:00 | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 |              11 |        5 |     211 |
| 412 broccoli casserole               |        40 | 2008-05-30 00:00:00 | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 |               9 |        5 |       6 |
| millionaire pound cake               |       120 | 2008-02-12 00:00:00 | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 |               7 |        5 |     326 |
| 2000 meatloaf                        |        90 | 2012-03-06 00:00:00 | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 |              13 |        5 |      12 |

### Univariate Analysis
Below is a histogram of the middle 98th-percentile of the 'minutes' column. We chose to restrict the column values to the middle 98th-percentile, to exclue values where 'minutes' was 0 or where 'minutes' exceeded a reasonable range (the maximum value in the 'minutes' is over 1,000,000!). The values are binned into 50 groups to make the plot more viewer friendly. The histogram describes an expected trend: the majority of recipes are cooked under 2 hours, and few recipes exceed 5 hours of cooking time.

<iframe src="assets/file-name.html" width=800 height=600 frameBorder=0></iframe>