# EDA & Modeling of Recipes & Ratings Datasets
Final Project - DSC 80 @ UCSD 
- by Jessica Lu

## Project Introduction
#### **Overview**
This project analyzes a comprehensive recipe dataset from Food.com, containing thousands of user-submitted recipes along with nutritional information, preparation details, and user reviews and ratings. In a time where home cooking is increasingly popular as people become more conscious about what they eat and eating out is becoming more of a luxury, understanding what makes a recipe successful is more important than ever.

However, starting out cooking can be a difficult tasks, with so many recipes online and so many levels of complexity. That's why I want to look at how many steps a recipe has and if there's a way to predict that. 

**Research Question & Goal**:

Can we predict the complexity of a recipe (measured by number of steps) based on its characteristics such as preparation time, ingredients, and nutritional content? 

**Why:** 

Understanding what factors contribute to recipe complexity can help home cooks estimate the effort required before starting a recipe and help recipe creators better communicate the difficulty level of their dishes.

### **Dataset Overview**

Interactions dataset: 731,927 rows for every user interaction and 5 columns features

Raw recipes dataset: 83,782 rows for each unique recipe and 12 columns containing recipe information

### Relevant Columns from Recipes Dataset

| Column | Description |
|--------|-------------|
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

#### Relevant Columns from Ratings Dataset

| Column | Description |
|--------|-------------|
| `recipe_id` | Recipe ID |
| `rating` | Rating given |


## Data Cleaning & Exploration
#### **Cleaning**
The steps to clean the datasets are the following
1. To look at the interactions between our two datasets, I first merged them together on their recipe id. 
2. Interactions where there was no rating was given a 0, which heavily skews with finding different summary statistics and overall distribution, so I had to first replace that with NA. 
3. Since multiple people have different ratings to the same recipes, I had to groupby each recipe and create a column that shows the average rating of a recipe. Afterwards, to make the nutrition_list more usable, I created a individual column for each nutrition (calories, sodium, etc.) and had their corresponding nutriton values for each column. 
4. I removed rows where there are extreme outliers that don't make sense (e.g. a recipe that takes 1M+ minutes) as these recipes could be submitted by anyone and could be inaccurate.
5. I then dropped columns that would be uncessary in exploring the data (review, user_id, recipe_id, date).

|    | name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | description                                                                                                                                                                                                                                                          | ingredients                                                                                                                                                                    |   n_ingredients |   rating |   avg_rating |   calories |   total fat |   sugar |   sodium |   protein |   saturated fat |   carbohydrates |
|---:|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|---------:|-------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
|  0 | 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting']                                                  | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven! | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |        4 |            4 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
|  1 | 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy', 'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined', 'add in the flour mixture to the wet ingredients and blend until combined', 'scrape down the sides of the bowl and add the chocolate chips', 'mix until combined', 'scrape down the sides to the bowl again', 'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking', 'bake for 10 - 15 minutes or until golden brown on the outside and soft & chewy in the center', 'serve hot and enjoy !'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                               | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 |        5 |            5 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |

### **Plots**
**Univariate Analysis**

<iframe src="assets/nsteps_hist.html" width=400 height=300 frameBorder=0></iframe>

This is a histogram showing the Distribution of Number of Steps (n_steps). The distribution is right-skewed, with most recipes having between 5-15 steps, and a few recipes having very high counts. This suggests that most recipes are relatively simple (the biggest bin being [5,10))



**Bivariate Analysis**

<iframe src="assets/minstep_box.html" width=400 height=300 frameBorder=0></iframe>


This is a box-plot showing the number of steps by preperation time, which was sorted into bins of medium, long, quick, and very long. Quick had the lowest median amount of steps at 7, but surprisingly, "Very Long" was not far off with a median of 12 steps. Overall it is showing that amount of time and n_steps have some sort of positive correlation. 



**Interesting Aggregate**
| Time              |   n_steps_mean |   n_steps_median |   n_steps_std |   recipe_count |   avg_minutes |
|:------------------|---------------:|-----------------:|--------------:|---------------:|--------------:|
| Very Quick (0-15) |        5.53043 |                5 |       3.78584 |          15544 |       9.32791 |
| Quick (15-30)     |        9.29841 |                9 |       4.52615 |          19594 |      24.8697  |
| Medium (30-60)    |       11.4138  |               10 |       5.72016 |          23906 |      45.606   |
| Long (60-120)     |       13.039   |               12 |       6.98637 |          11184 |      81.5157  |
| Very Long (120+)  |       11.7427  |               10 |       8.21704 |           6847 |     264.637   |

The average and median number of steps increase as preparation time increases, reinforcing the trend that longer recipes tend to be more complex. It also shows again that “Very Long” surprisingly shows slightly lower averages than expected, suggesting some long-duration recipes may involve fewer but more time-intensive steps.

## Missingness Analysis
**MNAR Analysis**

The missing columns are rating, average rating, and description. I believe that average rating could possibly be MNAR due to maybe the fact that average recipes have no ratings as people don't have strong enough opinions on it to leave a rating. However, looking at the other columns available, it does suggest that there's a strong possibility of average ratings being MAR. 


**Missingness Dependency**
To further see if the missingness of avg_rating is MAR, I used a permutation test to see if the observed differences between missing and non missing values depends on other variables in the dataset. 

Using a significance test of 0.05:
- Dependence on n_steps: The observed difference in mean n_steps between recipes with missing vs. non-missing ratings was 1.37, with a p-value of 0.0. Since the p-value is below 0.05, we reject the null hypothesis and conclude that rating missingness likely does depends on the number of steps.
- Dependence on minutes: The observed difference in mean cooking time was 11.11 minutes, with a p-value of 0.0. We again reject the null hypothesis, suggesting that rating missingness depends on preparation time.
- Dependence on protein: The observed difference in mean protein content was 0.90, with a p-value of 0.166. Since this p-value is greater than 0.05, we fail to reject the null hypothesis, showing that rating missingness likely does not depend on protein content.
"/recipes-eda-modeling/assets/missbox.html"

<iframe src="assets/miss.html" width=400 height=300 frameBorder=0></iframe>


## Hypothesis Testing
**Null Hypothesis:** The mean number of steps is the same for recipes with average ratings above 4.5 and those with average ratings at or below 4.5. Any observed difference is due to random chance.


**Alternative Hypothesis:** The mean number of steps differs between recipes with average ratings above 4.5 and those with average ratings at or below 4.5.


**Why this test:** Since my goal is to improve prediction of n_steps, this permutation test helps determine whether avg_rating is associated with recipe complexity. If we reject the null hypothesis, it suggests that ratings are not random to the number of steps, meaning avg_rating may contain useful information for predicting n_steps. Therefore, this test helps provide evidence for whether including avg_rating as a feature would be beneficial for our model.


**Test Statistic:**) Difference in mean number of steps (high-rated recipes (> 4.5) − lower-rated recipes (≤ 4.5))
Significance Level: 0.05
Observed Statistic: 0.1162 (high-rated recipes have slightly more steps on average)
P-value: 0.02 (<0.05)


**Conclusion:** We reject the null hypothesis that any observed difference is due to chance and there is likely a difference in ratings between higher and lower rated recipes. There is statistically significant evidence that the number of steps differs between highly rated recipes and lower rated recipes, but the difference is quite small. 

## Prediction Problem
The prediction is the estimated number of steps (n_steps) needed to complete a recipe, based on features such as preparation time, number of ingredients, and nutritional information.

I will be using an Regression model as we're predicting a numerical, continuous number. 
n_steps is our response variable. 

I chose this because it can show the complexity of a recipe. Recipes with more steps typically require more detailed preparation and effort, making it a meaningful outcome to predict. 

Also, n_steps is a well-defined variable that has good variability. I used the R² metric to evaluate. R² measures the proportion of variance in the response variable that is explained by the model. I chose R^2 over other metrics such as RMSE as it is more readable, and has a scale of 0-1 to tell how accurate a model is. While MSE measures prediction error in more ambigious values, R² allows us to understand improvements that are relatives to the different models. 

## Baseline Model
**About the Model**
I trained a linear regression model to predict the number of steps (n_steps) required for a recipe. The model uses a pipeline that first standardizes the input features using a StandardScaler, followed by fitting a LinearRegression model. I chose linear regression because it is a simple and interpretable baseline model for predicting a quantitative outcome. It allows me to understand the linear relationship between input features and the target variable.

**Features Used**
- minutes _(quantitative, continuous)_: total cooking time of a recipe
- n_ingredients _(quantitative, discrete)_: number of ingredients in a recipe
**Target**
- n-steps _(quantitative, discrete)_: number of steps in a recipe

I choose those two features for my baseline as I could assume they have positive correlation to n_steps. I used StandardSclar to the input features due to minutes and n_ingredients being from very different scales, preventing that from influencing the model. 

**Model Performance**
- Train R²: 0.19772278188688763
- Test R²: 0.18899488694522193

Our baseline model has moderate-low predictive performance which shows that those two columns alone don't predict minutes. There is very low differences between our training and test R² which shows that our model is doing a good job of not overfitting, but might be underfitting due to its low R². 


## Final Model 
**Features Added**
- Nutrition Values (calories, total fat, protein, carbohydrates; _quantitative, continuous_): These nutrition features help capture the complexity of a recipe. Recipes with higher nutrition values could indicate the size of the recipe which may correlate with the number of steps.
- desc_length _(quantitative, continuous)_: This measures the length of the recipe description. Longer descriptions may mean more complex recipes, which could lead to a higher number of steps.
- minutes_per_ingredient _(quantitative, continuous)_: This captures how much time is spent per ingredient. Recipes with more time per ingredient may involve more detailed preparation that requires more steps.
- ingredients_per_minute _(quantitative, continuous)_: This shows how many ingredients are being used per minute. The more ingredients being used per minute could mean that there are less steps (e.g. throwing in a bunch of seasoning, high ingredients low time)
- fat_protein_ratio _(quantitative, continuous)_: This shows a balance between the nutrients and could potentially reflect the type of dish that has less/more ingredients. (e.g. cake which has high fat low protein has more steps than grilled chicken which has low fat high protein and less steps)
- avg_rating _(quantitative, continuous)_: This captures what people think about this recipe and recipes that are more difficult (more steps) or on the contrary be better tasting (more steps) could determine ratings.

**Modeling Algorithm**
- I used a decision tree regressor as y final model so it can capture interactions between features and be more in depth than a linear regression. Before modeling, I used a pipeline to process the numbers with QuantileTransformer and StandardScaler. QuantileTransformer makes the distribution normal which helps with the large amount of outliers in our dataset.

**Hyperparameters**
- I used GridSearchCV with 3 cross validations to prevent overfitting to the validation data. The hyperparameters I chose included max_depth which controlled the complexity of the tree, preventing it from overfitting (max depth). I also used min_sample_split also to prevent overfitting by continously splitting. 
- {'regressor__max_depth': 7, 'regressor__min_samples_split': 10} was our best hyperparameter

**Final Model Performance**
- Train R²: 0.31802960305681127
- Test R²: 0.28784107493460065

For both the testing and training set, the final model performed around 0.1~ better which is around 10% better in R² (ability to observe variance). This shows that there was significant improvement in our model, but definently has space for more. Again, the training and test R² performed around the same, showing that it is still not overfitting.

## Fairness Analysis
**Group X:** recipes with average rating > 4.5
**Group Y:** recipes with average rating ≤ 4.5

**Null Hypothesis:**
Our model is fair. Its R² for highly rated recipes and lower-rated recipes is roughly the same, and any observed differences are due to random chance.

**Alternative Hypothesis:**
Our model is unfair. Its R² differs between highly rated recipes and lower-rated recipes.

**Test Statistic:** R²(high rated) - R²(low rated)
Significance Level: 0.05
Observed Statistic: 0.014 (high-rated recipes have slightly more steps on average)
P-value: 0.351 (>0.05)

**Conclusion:** We fail to reject the null hypothesis meaning there is statistically no significance, showing that the model's R² differences is likely only due to random chance. 
