# Recipe Research Project: Exploring the Impact of Cooking Time on Recipe Success

**Author**: Maya Que

## Project Overview

This is a data science project investigating the relationship between cooking time and recipe success. The dataset used is originally scrapped and used by authors of [this source](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf). This project is for DSC80 at UCSD. This is a project for the course DSC80 at UCSD. 

---

## Introduction

Cooking is an integral aspect of our daily lives, with ever-growing sources of recipes available at our fingertips. In today's digital age, platforms such as food.com provide many user-contributed recipes with all the necessary details to make the dish at home. With the fast-paced lifestyle becoming increasingly prevalent among individuals, especially busy students, professionals, parents, and anyone with tight schedules, considering the impact of preparation time on the popularity and satisfaction with a recipe is a crucial element in selecting dishes that align with their lifestyles. To find out whether the preparation, or cooking time, of a recipe affects the quality of the recipe, this project aims to shed light on the relationship between preparation time and the user rating of food.

The dataset for recipes contains 83782 recipes from 2008 to 2018 on food.com. It also contains 10 columns that record the following information. 

|Column	                 |Description|
|---                     |---        |
|`'name'	`            |Recipe name|
|`'id'`	                 |Recipe ID|
|`'minutes'`	         |Minutes to prepare recipe|
|`'contributor_id'`	     |User ID who submitted this recipe|
|`'submitted'`	            | Date recipe was submitted|
|`'tags'`	              |Food.com tags for recipe|
|`'nutrition'`	          |Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein    (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”|
|`'n_steps'`	          |Number of steps in recipe|
|`'steps'`	              |Text for recipe steps, in order|
|`'description'`	     | User-provided description|

For this project, I added an additional column representing average ratings corresponding to each recipe in this dataframe to facilitate analysis.

The dataset for ratings contain 731927 rows, each with a review on a recipe. It contains the 5 columns listed below.

|Column|Description|
|---|---|
|`'user_id'`	|User ID|
|`'recipe_id'`	|Recipe ID|
|`'date'`	|Date of interaction|
|`'rating'`	|Rating given|
|`'review'`	|Review text|

In the following, I will be cleaning the data to get the data in a usable format and then doing exploratory data analysis (EDA) to show the distribution of columns and their relationships with each other.

Next, I will assess the missingness contained in the data by determining whether the data is NMAR and analyzing missingness dependency. In NMAR analysis, I will focus on examining the missingness of the description, review, and rating column and reasonably determine if there is an NMAR column. In analyzing missingness dependency, I will perform permutation tests on the rating column to analyze the dependency of the missingness of this column on other columns. Specifically, I will test the missingness of ratings on the minutes and calories column.

Finally, I will conduct a hypothesis test on a research question investigating whether there is a difference in ratings for time-efficient and inefficient recipes. I defined time-efficient recipes as recipes with preparation times of 60 minutes or less and time-inefficient recipes as recipes with preparation times of over 60 minutes. 

Understanding the possible correlation behind features in a recipe can provide valuable insights into the factors that drive the success behind a recipe, allowing people to make more informed decisions about recipes that not only suit their taste preferences but also fit into their daily lifestyles. Recipe contributors will gain more knowledge on how to successfully design recipes that align with individuals' time constraints or other needs.

---
## Data Cleaning

Before conducting analysis, I first cleaned the data to increase the readability and accuracy of the data.

> Converting Columns to Correct Types

Upon checking the type of each column in the dataframe, I noticed that the columns `tags`, `nutrition`, `steps`, and `ingredients` contain values that look like lists but are actually strings that look like lists. I converted these columns into lists of strings to eliminate the confusion as well as make it easier to access in the future if needed. I also converted the `submitted` column into datetime data type so I could examine the trend of features over time later.

> Seperating `nutrition` into Individual Columns

After converting the strings into lists, I found that the list in the nutrition column contains information in the form of `'calories', 'total fat (PDV)', 'sugar (PDV)', 'sodium (PDV)', 'protein (PDV)', 'saturated fat (PDV)', 'carbohydrates (PDV)'`. I created an individual column for each of the unique nutrients in the list and converted each value to float data type so it is more convenient for future calculation.

> Left Merge Recipe and Ratings Datasets Together

I merge the two dataframes together based on their common column, `id` and `recipe_id`, so that the recipes are matched with their corresponding rating to make comparison easier.

> Fill all Ratings of 0 with np.nan

Since ratings usually range from 1 to 5, a rating of 0 is not typically viewed as a valid rating and is likely to be a default value for missing ratings. To correctly indicate that 0 indicates a missing value in atings, I filled such values with np.nan instead.

> Creating Average Rating per Recipe Column

Since this project focuses on examining the ratings of recipes, I made a new series calculating the average rating for the recipe and then added it into the dataframe as a column, naming it `avg_rating`.

The cleaned dataframe and merged dataframe are shown below respectively, with only the important columns extracted for display.

Cleaned:

|     id | name                                 |   minutes | submitted           |   n_steps |   n_ingredients |   ave_rating |
|-------:|:-------------------------------------|----------:|:--------------------|----------:|----------------:|-------------:|
| 333281 | 1 brownies in the world    best ever |        40 | 2008-10-27 00:00:00 |        10 |               9 |            4 |
| 453467 | 1 in canada chocolate chip cookies   |        45 | 2011-04-11 00:00:00 |        12 |              11 |            5 |
| 306168 | 412 broccoli casserole               |        40 | 2008-05-30 00:00:00 |         6 |               9 |            5 |
| 286009 | millionaire pound cake               |       120 | 2008-02-12 00:00:00 |         7 |               7 |            5 |
| 475785 | 2000 meatloaf                        |        90 | 2012-03-06 00:00:00 |        17 |              13 |            5 |


Merged:

|     id | name                                 |   minutes | submitted   |   user_id | date       |   rating | review                                                                                                                                                                                                                                                                                                                                           |
|-------:|:-------------------------------------|----------:|:------------|----------:|:-----------|---------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 333281 | 1 brownies in the world    best ever |        40 | 2008-10-27  |    386585 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs.                                                                                   |
| 453467 | 1 in canada chocolate chip cookies   |        45 | 2011-04-11  |    424680 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide yard sale, & I made the whole batch & used them as enticements for potential buyers ~ what the hey, a free cookie as delicious as these are, definitely works its magic! Will be making these again, for sure! Thanks for posting the recipe! |
| 306168 | 412 broccoli casserole               |        40 | 2008-05-30  |     29782 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for this recipe. I was a bit worried about the tsp of soy sauce but it gave the casserole the best flavor. YUM!                                                                                                                                  |


---
## EDA

### Univariate Analysis

#### Distribution of Years

In univariate analysis, I first analyze the distribution of the years when recipes were submitted. Before plotting, I extracted the year from the submitted datetime column and assigned it to a new column named year. 

<iframe src="assets/fig1.html" width=800 height=600 frameBorder=0></iframe>

The distribution shows that the distribution is skewed right, with more recipes submitted in earlier years, particularly 2008, than in recent years. This suggests that recipes in our data were created quite some time ago and that fewer people have been submitting recipes to food.com in recent years, perhaps due to the emergence of newer recipe platforms. The observation suggests that the trends seen in our recipe data may be more applicable to earlier years rather than now.

#### Distribution of Minutes

Then, I examined the distribution of preparation time of recipes in the dataset. Due to the wide range of minutes in the dataframe, I selected only a portion of the distribution (minutes from  0 to 200) due to extreme outliers. 

<iframe src="assets/fig2.html" width=800 height=600 frameBorder=0></iframe>

Even without the outliers, one can see that there is a large right skew, which indicates that most recipes take a short amount of time as opposed to a long amount of time. In addition, one can see that most recipes have minutes that are a multiple of 5, which may be due to recipe contributors being more likely to input an estimated recipe preparation time rather than the exact time. The graph is centered around 30, meaning most recipes have a preparation time of 30 minutes. 

### Bivariate Analysis

I examined the relationship between two variables in bivariate analysis. 

#### Number of Minutes over Years

First, I used a box plot to illustrate the relationship between the number of minutes for recipes and the year the recipe was submitted. 

<iframe src="assets/fig3.html" width=800 height=600 frameBorder=0></iframe>

From the boxplot, if one focuses on the median and third quartile, one can see a slight increase in the minutes taken for recipes over the years, although it remains mostly steady. This suggests that there may be a slight correlation between minutes and years, with recipes taking a slightly longer time to prepare to be more popular in later years in our dataset. Additionally, one can also see that there are many more extreme outliers in the earlier years rather than in the later years. This may be due to the fact that our dataset has more recipes submitted in those earlier years, so there is a wider range of minutes for recipes during that time.


#### Number of Steps over Minutes

Next, I used a line graph to examine the relationship between the minutes for recipes and the number of steps for recipes. 

<iframe src="assets/fig4.html" width=800 height=600 frameBorder=0></iframe>

The line graph illustrates that as the number of minutes increases from 0 to 60, the mean number of steps for a recipe increases steadily as well. This makes sense because the longer the preparation time needed for a recipe, the more steps it is likely to have. However, when the minutes needed for a recipe increase beyond 60, the mean number of steps for a recipe plateaus into a flatter slope with increasing fluctuations as minutes become even larger, showing almost no relationship between the two variables. The large fluctuations as minutes increase may be due to the fact that while some recipes have longer preparation time due to more steps in the cooking process, other recipes have longer preparation time because the waiting time for the food to cook may be longer but the actual number of steps do not increase as much.

### Interesting Aggregates

Next, I am curious to see if there is a trend between recipe minutes and healthy recipes. Since healthier recipes are likely to have higher protein levels, I compared recipe minutes with the amount of protein (PDV) in recipes. I constructed a pivot table that shows the mean, median, minimum, and maximum of protein with minutes.

|   ('minutes', '') |   ('mean', 'protein (PDV)') |   ('median', 'protein (PDV)') |   ('min', 'protein (PDV)') |   ('max', 'protein (PDV)') |
|------------------:|----------------------------:|------------------------------:|---------------------------:|---------------------------:|
|                 0 |                   118       |                           118 |                        118 |                        118 |
|                 1 |                     5.82533 |                             0 |                          0 |                        163 |
|                 2 |                     7.20705 |                             1 |                          0 |                        281 |
|                 3 |                     8.43093 |                             2 |                          0 |                        182 |
|                 4 |                    14.8352  |                             9 |                          0 |                        129 |

From the table, it appears that the protein is constantly fluctuating up and down over the number of minutes, so it is difficult to determine if there is a relationship. To get a visual representation of the pivot table, we convert it to the below line plot.

<iframe src="assets/fig5.html" width=800 height=600 frameBorder=0></iframe>

From the line plot, we can see that the maximum protein amount appears to be decreasing as the minutes increase, although there are still a couple of peaks near the higher end of minutes. This suggests that the range of protein for recipes decreases as time increases, with recipes that take a longer time offering fewer options for high-protein meals. This trend can be attributed to the earlier observation that the number of recipes decreases as time increases, which explains why the protein range is also decreasing over minutes.

To take a closer look at the plot, we zoom in on the distribution of mean protein over minutes. 

<iframe src="assets/fig6.html" width=800 height=600 frameBorder=0></iframe>

It can be observed that contrary to the maximum values, there seems to be a sharp increase in mean protein from 0 to 16 minutes, upon which it then plateaus and slowly increases until 77 minutes. At that point, the graph begins to fluctuate dramatically as minutes increase to 200 minutes. This suggests that recipes with longer cooking times have more protein than recipes with shorter cooking times, although this trend seems to disappear once preparation time increases past 77 minutes. However, it is interesting to note that the greatest peaks for mean protein still occur at the higher end of preparation time, specifically in the 100-150 minutes range. Overall, it appears that most recipes have about 30-40 PDV protein, which is generally an ideal amount for a balanced diet, suggesting that most of the recipes in the dataset are healthy recipes.

---
## Assessment of Missingness

In this part, I assessed the missingness of the merged dataframe.

### NMAR Analysis

A column in the merged dataframe with missing values that is possibly NMAR is the reviews column. In NMAR data, missingness is dependent on the missing value itself. People who write reviews tend to be those who have tried the recipe and have positive or negative feelings about it. Meanwhile, people who have not tried a recipe and thus, have neutral opinions about the recipe may be less likely to write a review. Since the missingness of the review is likely to due the possibility that people do not have any opinions on the recipe to leave a review, the chance that the review is missing depends on the actual missing value, making the missingness NMAR. However, if there is another column indicating additional information, such as the difficulty level of the recipe, there may be some dependency between this column and the missingness of review since people may be less likely to leave a review on recipes that are neither easy nor hard, thus making the review missingness MAR.

### Missingness Dependency

I noticed there were a lot of missing values in the rating column and tested to see if the missingness of ratings depended on other columns or not. I tested the missingness dependency of ratings on the two columns minutes and calories, using permutation tests to determine the relationships. 

#### 1. Rating and Minutes (MCAR)

Null Hypothesis: The missingness of average ratings is not dependent on minutes

Alternative Hypothesis: The missingness of average ratings is dependent on minutes

I shuffled the `rating` column for permutation. Since `minutes` is a numeric variable, I used the absolute mean difference of `minutes` when `rating` is missing or not missing as the test statistic. 

Below is the distribution of minutes with null and non-null ratings.

<iframe src="assets/fig7.html" width=800 height=600 frameBorder=0></iframe>

From the distribution, it can be seen that both distributions look quite similar. However, to be sure, I continued to conduct a permutation test.

<iframe src="assets/fig8.html" width=800 height=600 frameBorder=0></iframe>

The graph above shows the empirical distribution of the test statistic with 1000 permutations with the red line representing the observed test statistic. From the permutation test, I calculated the p-value to be 0.124, which is greater than the significance level of 5%, so we failed to reject the null hypothesis. Based on our results, it appears that the missingness of `rating` does not depend on the `minutes` column, so the missingness of `rating` is MCAR.

#### 2. Rating and Calories (MAR)

Null Hypothesis: The missingness of rating is not dependent on the number of calories

Alternative Hypothesis: The missingness of rating is dependent on the number of calories

Similar to the previous test, I shuffled the `rating` column for permutation. Since `calories` are a numeric variable, I used the absolute difference of `calories` when `rating` is and is not null as the test statistic. 

<iframe src="assets/fig9.html" width=800 height=600 frameBorder=0></iframe>

From the distribution, we notice that there is a slight difference in the distributions when the rating is missing and when it is not. The distribution when the rating is missing is slightly to the right of the distribution when the rating is not missing. For further analysis, I ran a permutation test on the columns.

<iframe src="assets/fig10.html" width=800 height=600 frameBorder=0></iframe>

The plot shows the empirical distribution of the test statistic in 100 permutations, with the red line indicating the observed test statistic. I calculate the p-value to be approximately 0.00, which is less than the significance threshold of 0.05. Thus, we reject the null hypothesis that rating is not dependent on calories. Based on the results, it appears that the missingness of rating does depend on the calories column. The missingness of rating is likely MAR, since recipes with higher or lower calories may be more likely to have more missingness in the ratings column.

---
## Hypothesis Testing

The research question I am investigating is if there is a relationship between recipe time and ratings. In other words, do recipes with higher time efficiency (less cooking time) have higher ratings? Since recipes that take less cooking time are generally faster and simpler to make, people would be more likely to give the recipe a higher rating. Therefore, I will be conducting a one-sided test to examine if the relationship between `minutes` and `rating` is truly significant, or just due to random chance. I used a permutation test on the distribution of ratings on recipes with longer preparation times and shorter preparation times to see whether ratings actually change or not.


**Null Hypothesis**: The ratings in fast recipes and slow recipes have the same distribution, and the observed differences are due to random chance.

**Alternative Hypothesis**: The less time it takes to make a recipe, the higher the rating is. The observed difference cannot be explained by random chance alone. 

I chose to use only minutes that are less than and equal to 200 to remove extreme outliers that may impact the observational statistics. I also used the `minutes` column to make a new column `slow`, which is true if the number of minutes is greater than 60 (one hour), and false if the number of minutes is less than or equal to 60. 

| slow   |   minutes |   avg_rating |
|:-------|----------:|-------------:|
| False  |   29.1694 |      4.62931 |
| True   |  370.846  |      4.61345 |

Since I am measuring how different two numerical distributions are (average rating for fast and slow recipes), the test statistic I used is the difference in group means (mean rating of fast recipes - mean rating of slow recipes). I chose to use the significance level of 5% to increase accuracy of the randomized test result. 

The observed difference in means was calculated to be 0.015854362618372697.

### Permutation Test

<iframe src="assets/fig11.html" width=800 height=600 frameBorder=0></iframe>

The plot shows the empirical distribution of the test statistics in 1000 permutations, with the red line indicating observed test statistics on the far right of the distribution. I calculated the p-value to be 0.001, which is less than the significance level of 0.05. Therefore, we reject the null hypothesis. From the test conducted, it can be concluded that the observed data in the dataset shows evidence against the null hypothesis that the differences in the distributions of average ratings in fast and slow recipes are due to random chance. This result is reasonable since the recipes that take a longer time can be more complex and people may be more likely to mess up at cooking the dish. Moreover, people want their food fast and may become impatient for waiting a long time for the recipe, which can lead to giving a lower rating to the recipe. Furthermore, people who are busy are also more likely to try recipes that are faster and more time-efficient to make, which can contribute to the higher ratings of faster recipes.