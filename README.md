# Recipe Research Project: Exploring the Impact of Cooking Time on Recipe Success

**Author**: Maya Que

## Project Overview

This is a data science project investigating the relationship between cooking time and recipe success. The dataset used is originally scrapped and used by authors of [this source](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf). This project is for DSC80 at UCSD. This is a project for the course DSC80 at UCSD. 

---

## Introduction

Cooking is an integral aspect of our daily lives, with ever growing sources of recipes available at our fingertips. In today's digital age, platforms such as food.com provide many user-contributed recipes with all the necessary details to make the dish at home. With the fast paced lifestyle becoming increasingly prevalent among individuals, especially busy students, professionals, parents and anyone with tight schedules, considering the impact of preparation time on the popularity and satisfacton with a recipe is a crucial element in selecting dishes that align in their lifestyles. In order to find out whether the preparation, or cooking time, of a recipe affects the quality of the recipe, this project aims to shed light on the relationship between preparation time and the user rating of food.

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

For this project, I added an additional column representing average ratings for each recipe in the dataframe to facilitate the analysis of different features of the recipe with their ratings.

The dataset for ratings contain 731927 rows, each with a review on a recipe. It contains the 5 columns listed below as well.

|Column|Description|
|---|---|
|`'user_id'`	|User ID|
|`'recipe_id'`	|Recipe ID|
|`'date'`	|Date of interaction|
|`'rating'`	|Rating given|
|`'review'`	|Review text|

In the project, I will be cleaning the data to get the data in a usable format and then doing exploratory data analysis (EDA) to show the distribution of columns and its relationships with each other.

Next, I will assess the missingness contained in the data by determing whether data is NMAR and analyzing missingness dependency. In NMAR analysis, I will focus on the examine the missingness of the description, review, and rating column and reasonably determine if there is a NMAR column. In analyzing missingness dependency, I perform permutation tests on the ratings column to analyze the dependecy of the missingness of this column on other columns. Specifically, I will test the missingness of ratings on the minutes and calories column.

Finally, I will conduct a hypothesis test on a research question investigating whether there is a difference in ratings for time efficient and inefficient recipes. I defined time efficient recipes as recipes with a preparation time of 60 minutes or less, and time inefficient recipes as recipes with a preparation recipes of over 60 minutes. Understanding the possible correlation behind features in a recipe can provide valuable insights into the factors that drive the success behind a recipe, allowing people to make more informed decisions about recipes that not only suit their taste preferences but also fit in their daily lifestyles. Recipe contributors will gain more knowledge on how to successfully design recipes that align to individuals time constraints or other needs.

## Data Cleaning


















