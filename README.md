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
  src="Assets/bivariate-boxplot.html"
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

## Assessment of Missingness ##
In the cleaned dataset `recipes`, there are three columns with missing values: `name`, `description`, and `rating`. We will assess the missingness on the dataframe with the following analysis: <br>

### NMAR Analysis ###
The missingness in the `description` column is best characterized as Not Missing At Random (NMAR). A recipe description is optional and entirely user-provided, meaning whether a description is included depends on the contributor’s deliberate choice rather than on a value observed elsewhere in the dataset. For example, contributors may omit descriptions when they believe the recipe is self-explanatory already. <br>

### Missingness Dependency ###
#### Minutes and Rating ####
**Number of Minutes and Rating** <br>
To further investigate the missingness mechanism of the rating column, we examined whether the probability of a missing rating depends on the recipe’s preparation time, measured by the minutes column.<br>

**Null Hypothesis:** <br>
The missingness of ratings does not depend on the number of minutes required to prepare the recipe.<br>

**Alternate Hypothesis:** <br>
The missingness of ratings does depend on the number of minutes required to prepare the recipe.<br>

**Test Statistic:** <br>
We used the absolute difference between the mean number of minutes for recipes with observed ratings and the mean number of minutes for recipes with missing ratings. This statistic measures the magnitude of the difference in preparation time between the two groups regardless of direction**.

**Method:** <br>
We conducted a permutation test by randomly shuffling the minutes column while keeping the missingness pattern of rating fixed. For each permutation, we recomputed the test statistic to generate an empirical distribution under the null hypothesis. The p-value was calculated as the proportion of permutation statistics greater than or equal to the observed test statistic.

**Significance Level:** <br>
We used a significance level of 0.05 to determine statistical significance.

<iframe
  src="Assets/min-dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We ran permutation by shuffling the the `minutes` column for 1000 times to collect 1000 simulating absolute mean differences in the two distributions as described in the test statistic.

<iframe
  src="Assets/emp-dist1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The **observed statistic** of **117.342** is indicated by the red vertical dotted line in the graph. The **p_value** we found after performing the permutation test is **0.034**, which is smaller than the significant level of **0.05**. Therefore, we **reject the null hypothesis**. The missingness of ratings does depend on the number of minutes required to prepare the recipe.<br>


#### Carbohydrates and Rating #### 
We next examined whether the missingness of the rating column depends on the carbohydrate content of a recipe. Specifically, we tested if there is a systematic difference in carbohydrate values between recipes with observed ratings and those with missing ratings.<br>

**Null Hypothesis:** <br>
The missingness of ratings does not depend on the carbohydrate content of the recipe. <br>

**Alternate Hypothesis:**
The missingness of ratings does depend on the carbohydrate content of the recipe. <br>

**Test Statistic:**
We used the absolute difference between the mean carbohydrate content of recipes with observed ratings and the mean carbohydrate content of recipes with missing ratings. This statistic captures the magnitude of the difference between the two groups without regard to direction. <br>

**Method:**
A permutation test was conducted by randomly permuting the carbohydrates column while keeping the missingness pattern of the rating column unchanged. For each permutation, we recomputed the test statistic to construct an empirical null distribution. The p-value was calculated as the proportion of permutation statistics greater than or equal to the observed test statistic. <br>

**Significance Level:** <br>
We used a significance level of 0.05 to determine statistical significance. <br>

<iframe
  src="Assets/carb-dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We ran permutation by shuffling the the `minutes` column for 1000 times to collect 1000 simulating absolute mean differences in the two distributions as described in the test statistic.

<iframe
  src="Assets/emp-dist2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The **observed statistic** of **0.351** is indicated by the red vertical dotted line in the graph. The **p_value** we found after performing the permutation test is **0.464**, which is much bigger than the significant level of **0.05**. Therefore, we **fail to reject the null hypothesis**. We do not have eough information to conclude that the missingness of ratings depends on the carbohydrates of the recipe.<br>

## Hypothesis Test / Permutation Test ##
As discussed earlier, we are interested in understanding whether the number of ingredients in a recipe influences how users rate it. In particular, we want to examine whether recipes with fewer ingredients are rated differently compared to those with more ingredients. We define recipes with few ingredients as those whose number of ingredients is less than or equal to the median number of ingredients across all recipes. This classification is represented by the boolean variable isFewIngredients. <br>

To investigate this question, we conducted a permutation test with the following hypotheses, test statistic, and significance level.<br>

**Null Hypothesis:** <br>
People rate recipes with few ingredients and recipes with many ingredients on the same scale. <br>

**Alternative Hypothesis:** <br>
People rate recipes with many ingredients higher than recipes with few ingredients. <br>

**Test Statistic:** <br>
The difference in mean ratings between recipes with many ingredients and recipes with few ingredients (mean rating of non-few-ingredient recipes minus mean rating of few-ingredient recipes). <br>

**Significance Level:** <br>
0.05 <br>

We chose to use a permutation test because we do not assume any prior knowledge about the underlying population distribution of recipe ratings. The permutation test allows us to assess whether the observed difference in mean ratings is consistent with the ratings being randomly assigned across recipe types. In other words, it helps determine whether the two groups of ratings could have come from the same population.<br>

Additionally, we used the difference in means rather than the absolute difference because we have a directional hypothesis: we are specifically testing whether recipes with many ingredients receive higher ratings than those with fewer ingredients. We assumed that people would rate recipes with more ingredients a higher score becuase they are tend to be more falvorized.<br>

To run the test, we first divided the recipes into two groups: those with few ingredients and those with many ingredients using `isFewIngredients` column. The **observed test statistic**, calculated as the difference between the mean of many ingredients group and mean of few ingredietns group, was **-0.004**.

We then randomly shuffled the isFewIngredients labels 1,000 times while keeping the ratings fixed, recalculating the test statistic for each permutation to generate a null distribution. Using this distribution, we obtained a **p-value** of **0.815**.

<iframe
  src="Assets/perm-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Conclusion ###
Since the **p-value** is **0.815**, we **fail to reject** the null hypothesis. We do not have enough information to conclude that people rate recipes with many ingredients higher than recipes with few ingredients.<br>

## Framing a Prediction Problem ##

Our problem is to predict the average rating of a recipe, which would be a regression problem since the ratings are continuous in the range [1.0, 5.0]. The response variable is the average rating of a recipe, aggregated across all user interactions. We chose to predict rating because it directly measures user satisfaction and recipe quality, and it's interesting to see which features (e.g. number of ingredients, nutrition, etc.) would impact how well users like a recipe.

The primary evaluation metric is Root Mean Squared Error (RMSE). Compared to other metrics like MSE, RMSE makes the error more interpretable as it's in the same unit as what we're trying to predict, which is the main reason we chose to use it. 

The information we have prior making our prediction are all the columns in the `recipes` dataset (listed at the result of the data cleaning section) except the `rating` column as that's what we're trying to predict. These features capture nutritional profile, recipe complexity, and categorization that may influence ratings, enabling predictions for new recipes based solely on recipe characteristics known before any ratings exist.

## Baseline Model ##

For the baseline model, we used a linear regression model with three features, consisting of two quantitative features `calories` and `protein` and one nominal feature `isLong`. `calories` and `protein` come from the `recipes` dataframe and are standardized with `StandardScaler`. The `isLong` feature is created using the `minutes` column in `recipes`, where recipes requiring more than 200 minutes (a little more than the mean) are true for `isLong` and false otherwise. `isLong` is one-hot encoded with drop='first' (one binary column). 

The model is trained on 80% of the data with a 20% test split (random_state=42), with an RMSE of 0.6357. The model is a simple baseline, and the low feature count (3) likely limits predictive power. So, we wouldn't consider the current model as 'good', but it serves as a good starting point for comparison with more sophisticated models that include additional engineered features.

## Final Model ##

For the final model, we used the features in the baseline model, along with 4 new features `steps_per_minute`, `has_dessert`, `has_healthy`, and `is_main_dish`.

The following are the features in the baseline model and why we kept them. 
* `calories`: Calorie content reflects recipe type and portion size. Users may prefer moderate-calorie recipes, and calories can signal healthiness, satiety, and recipe category, which likely influence satisfaction.
* `protein`: Protein content is a common nutritional focus. Higher-protein recipes may appeal to health-conscious users.
* `isLong`: Indicates recipes taking >200 minutes. Cooking time affects accessibility and expectations; longer recipes may attract users seeking complex dishes, while shorter ones appeal to those prioritizing convenience. This captures time-related preferences that likely influence ratings.
These features are encoded similarly as in the baseline model.

The following are the new feautures we added and why we added them.
* `steps_per_minute` (quantitative): this feature is engineered by taking the number of steps and dividing it by the number of minutes of the recipe. it captures how intense or demanding a recipe feels. Two recipes may take the same amount of time, but one with many rapid-fire steps creates more cognitive load and feels more stressful or complex to follow. This perceived difficulty strongly influences user satisfaction and thus ratings.
* `has_dessert` (nominal): this feature is extracted from tags. This feature identifies desserts, and desserts may have different rating patterns than savory dishes, so this captures category-specific effects.
* `has_healthy` (nominal): this feature is extracted from tags. "Healthy" recipes may attract different expectations and rating behaviors, capturing health-oriented preferences.
* `is_main_dish` (nominal): this feature is extracted from tags. Main dishes may differ from sides/appetizers in complexity, expectations, and rating patterns, and these category-specific rating patterns are not fully captured by nutritional information or cooking time alone, which is why this feature may be helpful.

The model we used is Lasso Regression, because the L1 regularization would help handle potential multicollinearity and reduce the risk of overfitting, which is needed since we are using more features. The quantitative features are transformed using `StandardScaler` and the nominal features are one-hot encoded with drop='first'. Using GridSearchCV with 10-fold cross-validation, the hyperparameters we tuned for is the regularization constant `alpha`, and the best came out to be 1e-14 (which is very low, and could mean that no regularization is needed). The RMSE on the test set came out to be 0.6296, which is indeed lower than our baseline model.

## Fairness Analysis ##
For our fairness analysis, we examined whether our model performs differently for recipes with few ingredients versus those with many ingredients. We use the same column `isFewIngredients` as in previous sections.<br>

We evaluated the fairness of the model using error parity, specifically by comparing the **Root Mean Squared Error (RMSE)** across the two groups. RMSE was chosen because it penalizes larger prediction errors more heavily, making it well-suited for assessing whether the model makes systematically larger mistakes for one group than the other. A model that exhibits similar RMSE values across groups can be considered more equitable in terms of predictive accuracy. <br>

**Null Hypothesis:** <br>
Our model is fair. The RMSE for recipes with few ingredients and recipes with many ingredients is approximately the same, and any observed difference is due to random chance. <br>

**Alternative Hypothesis:** <br>
Our model is unfair. The RMSE for recipes with few ingredients differs from the RMSE for recipes with many ingredients. <br>

**Test Statistic:** <br>
The absolute difference in RMSE between the two groups (few ingredients vs. many ingredients). <br>

**Significance Level:** <br>
0.05<br>

<iframe
  src="Assets/fair-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Again, we run the pertutation test by shuffling 1000 times. The **obsreved test statistic** is **0.023**. Using this null distribution, we calculated the p-value as the proportion of permuted test statistics that were greater than or equal to the observed test statistic. The resulting **p-value** was **0.159**.<br>

**Conclusion:** <br>
At a significance level of 0.05, we fail to reject the null hypothesis. This indicates that there is insufficient evidence to conclude that the model’s prediction error differs between recipes with few ingredients and those with many ingredients. As a result, the model does not appear to exhibit unfairness with respect to the number of ingredients based on RMSE parity.<br>
