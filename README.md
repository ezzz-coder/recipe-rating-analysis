# Investigation on the Relationship Between Number Of Ingredients and Rating of Recipes
Final project for DSC80 at UCSD by Edward and Samson<br>

## Overview ##
This data science project, conducted at UCSD, focuses on exploring the relationship between the average rating of a recipe and the number of ingredients of the given recipe.<br>

## Introduction ##
Food plays a central role in everyday life, and cooking is both a practical necessity and a creative pursuit for many people. With the rise of online platforms, home cooks increasingly rely on user-generated recipes and ratings to decide what to prepare. These ratings serve as a form of collective judgment, reflecting not only taste preferences but also factors such as convenience, complexity, and overall cooking experience.<br>

One aspect of a recipe that may influence user perception is the number of ingredients it requires. Recipes with a long list of ingredients may be viewed as time-consuming or difficult to prepare, while those with fewer ingredients may be perceived as simpler and more approachable. **This raises an interesting question: does recipe complexity, as measured by the number of ingredients, affect how users rate a recipe?**<br>

To investigate this question, we analyze two datasets containing recipes and user ratings collected from [Food.com](https://www.food.com) since 2008. These datasets were originally curated for recommender system research in [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) by Majumder et al., and they provide rich information on recipe attributes and user feedback. Using this data, we examine the relationship between a recipe’s average rating and its number of ingredients to better understand how preparation complexity aligns with user satisfaction.<br>

The first dataset, `Raw_recipes.csv`, contains 83782 rows and 10 columns, recording the following information: <br>

| Column | Description |
|-------|------------|
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Minutes to prepare recipe |
| `contributor_id` | User ID who submitted this recipe |
| `submitted` | Date recipe was submitted |
| `tags` | Food.com tags for recipe |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for "percentage of daily value" |
| `n_steps` | Number of steps in recipe |
| `steps` | Text for recipe steps, in order |
| `description` | User-provided description |

The second dataset, ``Raw_interactions.csv`, contains 731927 rows and 5 columns, recording the following information: <br>

| Column | Description |
|--------|-------------|
| `user_id` | User ID |
| `recipe_id` | Recipe ID |
| `date` | Date of interaction |
| `rating` | Rating given |
| `review` | Review text |

Each row of this dataset represents a user review to a given recipe. The data includes multiple reviews from different user to a speific recipe, resulting in the large number of rows.<br>

To analyze the relationship between number of ingredients and rating, we need to make two new columns: `rating` column, which is updated by taking the average of all ratings of a given recipe after dataframe merge, and `isFewIngredients` column, which is computed by comparing `n_ingredients` column with the median number of ingredients used. More Details will be given in the data cleaning section.<br>

## Data Cleaning and Exploratory Data Analysis ##
1. First, left merge `Raw_recipes` and `Raw_interactions`, left on `id` and right on `recipe_id`, and then save it into dataframe called `df`. This will make sure we maintain all the recipes we have and create extra rows for those recipes with multiple reviews.<br>

2. Second, check datatypes of all columns in `df`. This will help us on further data cleaning such as converting datatype, filling null values, etc.<br>

| Column | Data Type |
|--------|-----------|
| `name` | object |
| `id` | int64 |
| `minutes` | int64 |
| `contributor_id` | int64 |
| `submitted` | object |
| `tags` | object |
| `nutrition` | object |
| `n_steps` | int64 |
| `steps` | object |
| `description` | object |
| `ingredients` | object |
| `n_ingredients` | int64 |
| `user_id` | float64 |
| `recipe_id` | float64 |
| `date` | object |
| `rating` | float64 |
| `review` | object |

### Univariate data anlysis ###
<iframe
  src="Assets/n-ingredients-dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of n_ingredients is right-skewed, with a peak around 8–9 ingredients at around 8500 counts. Most recipes have fewer than 15 ingredients, and the tail extends to about 35, indicating most recipes use a moderate number of ingredients with a few using many more.


