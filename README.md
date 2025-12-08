# Investigation on the Relationship Between Number Of Ingredients and Rating of Recipes
Final project for DSC80 at UCSD by Edward and Samson<br>

## Overview ##
This data science project, conducted at UCSD, focuses on exploring the relationship between the average rating of a recipe and the number of ingredients of the given recipe.<br>

## Introduction ##
Food plays a central role in everyday life, and cooking is both a practical necessity and a creative pursuit for many people. With the rise of online platforms, home cooks increasingly rely on user-generated recipes and ratings to decide what to prepare. These ratings serve as a form of collective judgment, reflecting not only taste preferences but also factors such as convenience, complexity, and overall cooking experience.<br>

One aspect of a recipe that may influence user perception is the number of ingredients it requires. Recipes with a long list of ingredients may be viewed as time-consuming or difficult to prepare, while those with fewer ingredients may be perceived as simpler and more approachable. **This raises an interesting question: does recipe complexity, as measured by the number of ingredients, affect how users rate a recipe?**<br>

To investigate this question, we analyze two datasets containing recipes and user ratings collected from [Food.com](https://www.food.com) since 2008. These datasets were originally curated for recommender system research in [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) by Majumder et al., and they provide rich information on recipe attributes and user feedback. Using this data, we examine the relationship between a recipe’s average rating and its number of ingredients to better understand how preparation complexity aligns with user satisfaction.<br>

The first dataset, `Raw_recipes.csv` (save into `recipes` dataframe), contains 83782 rows and 10 columns, recording the following information: <br>

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

The second dataset, `Raw_interactions.csv` (save into `interactions` dataframe), contains 731927 rows and 5 columns, recording the following information: <br>

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
1. **First, left merge `recipes` and `interactions`**: left on `id` and right on `recipe_id`, and then save it into dataframe called `df`. This will make sure we maintain all the recipes we have and create extra rows for those recipes with multiple reviews.<br>

2. **Second, check datatypes of all columns in `df`**. This will help us on further data cleaning such as converting datatype, filling null values, etc.<br>

-
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

3. **Third, fill all `rating` of 0 with `np.nan`**. The min rating should be 1 and the max rating should be 5. A rating of 0 indicates the user 'did not vote', which shouldn't be included during mean calculations. Since null values will be ingonred during pandas operations, we would like to replace all 0 with `np.nan`.<br>

4. **Compute average rating per recipe and add it back to the `recipes` dataframe**. As stated before, the merged `df` dataframe contains multiple row with different rating for the same recipe. By taking the average of all rating on the same recipe, we will have better understanding on the rating of a given recipe. We achieved this through grouping recipe by their id and apply the aggregation mean function on the `rating` column, then merge it into `recipes` dataframe. <br>

5. **Split nutrition column into indiviudal columns of floats**. The original `nutrition` column stores multiple nutritional attributes as a single string containing comma-separated values enclosed in brackets. Each entry corresponds to a different nutritional metric, including calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. Because these values are stored as strings, they cannot be directly used for numerical analysis or modeling. To address this, we extracted each nutritional component by parsing the string, removing the surrounding brackets, and splitting the values by commas. Each component was then converted into a floating-point number and assigned to its own column. This transformation allows the nutritional information to be analyzed individually and enables more meaningful comparisons and statistical analysis across recipes.<br>

6. **Add `isFewIngredients` column to `recipes`**. To better analyze how recipe simplicity relates to user ratings, we created a binary indicator that classifies recipes based on their number of ingredients. We computed the median value of the n_ingredients column and used it as a threshold to distinguish between recipes with relatively few ingredients and those with many ingredients.In the `isFewIngredients` column, a value of `True` indicates that a recipe has a number of ingredients less than or equal to the median, and `False` otherwise.<br>

### Result ###
The resulting `recipes` dataframe has 83782 rows and 21 columns, with following datatypes for each column:<br>

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
| `rating` | float64 |
| `calories` | float64 |
| `totalFat` | float64 |
| `sugar` | float64 |
| `sodium` | float64 |
| `protein` | float64 |
| `saturatedFat` | float64 |
| `carbohydrates` | float64 |
| `isFewIngredients` | bool |

Here are the first 5 rows of cleaned `recipes` dataframe with question related columns:<br>
| name                                 |     id |   rating |   minutes |   n_ingredients | isFewIngredients   |
|:-------------------------------------|-------:|---------:|----------:|----------------:|:-------------------|
| 1 brownies in the world    best ever | 333281 |        4 |        40 |               9 | True               |
| 1 in canada chocolate chip cookies   | 453467 |        5 |        45 |              11 | False              |
| 412 broccoli casserole               | 306168 |        5 |        40 |               9 | True               |
| millionaire pound cake               | 286009 |        5 |       120 |               7 | True               |
| 2000 meatloaf                        | 475785 |        5 |        90 |              13 | False              |

### Univariate data anlysis ###
<iframe
  src="Assets/n-ingredients-dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of n_ingredients is right-skewed, with a peak around 8–9 ingredients at around 8500 counts. Most recipes have fewer than 15 ingredients, and the tail extends to about 35, indicating most recipes use a moderate number of ingredients with a few using many more.<br>

### Bivariate data analysis
<iframe
  src="Assets/bivariate-boxplot.html.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Recipes with fewer ingredients (1–15) show wider rating distributions with medians around 4–5 and many low ratings. As the number of ingredients increases (beyond 15–20), ratings become more concentrated at the top (4–5), and recipes with 25+ ingredients are almost exclusively rated 5, indicating that more complex recipes tend to receive higher and more consistent ratings.

### Interesting aggregates

|   n_ingredients |    mean |   min |
|----------------:|--------:|------:|
|               1 | 4.86154 |  4.25 |
|               2 | 4.6926  |  1    |
|               3 | 4.66213 |  1    |
|               4 | 4.63403 |  1    |
|               5 | 4.64748 |  1    |
|               6 | 4.63316 |  1    |
|               7 | 4.62412 |  1    |
|               8 | 4.61158 |  1    |
|               9 | 4.60643 |  1    |
|              10 | 4.61027 |  1    |
|              11 | 4.62285 |  1    |
|              12 | 4.61683 |  1    |
|              13 | 4.63191 |  1    |
|              14 | 4.61643 |  1    |
|              15 | 4.63062 |  1    |
|              16 | 4.62503 |  1    |
|              17 | 4.63264 |  1    |
|              18 | 4.68767 |  1    |
|              19 | 4.61172 |  1    |
|              20 | 4.60765 |  1    |
|              21 | 4.65919 |  1    |
|              22 | 4.69331 |  2.5  |
|              23 | 4.77889 |  3    |
|              24 | 4.60472 |  1    |
|              25 | 4.7073  |  2.67 |
|              26 | 4.75929 |  4    |
|              27 | 4.61    |  2    |
|              28 | 4.85941 |  4    |
|              29 | 4.966   |  4.8  |
|              30 | 4.86818 |  4.33 |
|              31 | 5       |  5    |
|              32 | 5       |  5    |
|              33 | 5       |  5    |
|              37 | 5       |  5    |

Mean ratings stay consistently high (mostly 4.6–4.9) regardless of ingredient count, though the very highest means appear at the upper end (≈28–33 ingredients). Minimum ratings increase for higher ingredient counts, indicating that simpler recipes show more variability and occasionally poorer outcomes.

