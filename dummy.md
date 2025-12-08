### Univariate data analysis
<iframe
  src="Assets/n-ingredients-dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of n_ingredients is right-skewed, with a peak around 8–9 ingredients at around 8500 counts. Most recipes have fewer than 15 ingredients, and the tail extends to about 35, indicating most recipes use a moderate number of ingredients with a few using many more.

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

## Framing a Prediction Problem

Our problem is to predict the average rating of a recipe, which would be a regression problem since the ratings are continuous in the range [1.0, 5.0]. The response variable is the average rating of a recipe, aggregated across all user interactions. We chose to predict rating because it directly measures user satisfaction and recipe quality, and it's interesting to see which features (e.g. number of ingredients, nutrition, etc.) would impact how well users like a recipe.
