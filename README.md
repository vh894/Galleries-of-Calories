# Galleries of Calories
## By Esther Cho and Vanessa Hu

## Framing the Problem
One of the primary features that consumers commonly consider when looking at a recipe is the amount of calories that it contains. Calories is one of the most important nutritional values of a food since it’s a measure of how much energy that food will provide you. As a continuation from our previous work about classifying the healthiness of recipes, we decided to create a model that would predict the amount of calories that a recipe would have based on certain features in a recipe. Since the response variable (the amount of calories) in our model is a quantitative variable, our overall problem is a regression problem. Due to our model being a regression problem, we’re limited to only using either RMSE or R^2 as our metric. RMSE is used to estimate the distance between your model’s predicted outcome and the actual response variable while R^2 is used to see how well the model’s prediction is. Since we’re more interested in the quality of our predictions, we used R^2 to measure the accuracy of our model.

## Baseline Model
For our baseline model, we created our pipeline by utilizing StandardScalar and SGDRegressor. After analyzing all of the features in the dataset, we decided to use the ‘n_ingredients’ and ‘total fat (PDV)’ features to predict the amount of calories. Recipes that require more ingredients will mean that the final meal will involve a greater amount of food in it, thus the calories for that dish will be greater as well. Furthermore, according to the USDA,[^1] one gram of fat contains around nine calories, which is more than most other macronutrients. As a result, we predicted that the ‘total fat (PDV)’ feature would be a reliable predictor of our model. Since both of our features are quantitative variables, our baseline model didn’t involve any ordinal or nominal data, so we didn’t have to perform any encodings. We only used StandardScalar to transform our data features. To access the accuracy of our model, we called the pipeline’s score method, which gave us a R^2 value that was approximately 0.73. Higher R^2 values signify that the predicted values in the model are close to the actual response values in the dataset, so we believe that our baseline model is a good approximation and predictor of the amount of calories in a recipe.

## Final Model
To make our model more accurate, we decided to add more features that would help with predicting calories. We looked into the “tags'' feature and discovered the value counts for each tag across all recipes. We recognized that some of these tags could indicate whether or not a recipe has a high or low calorie count. For instance, tags like “dietary”, “low_in-something”, or “low-carb” would suggest a low calorie meal, while tags like “cuisine”, “number-of-servings”, “main-dish” suggest a high calorie meal. Using this logic, there should be a relationship between the types of tags and amount of calories in a recipe. We decided to make a binarizer transformer in our model that would mark tags containing “dietary”, “low_in-something”, or “low-carb” as 0, and if it did not contain those words, we would assign that row to a 1, as most of the other tags are neutral or indicate high calories. 
<iframe src="assets/file-saturatedfat_vs_cal.html" width=800 height=600 frameBorder=0></iframe>
One more feature we added was a KBinsDiscretizer of the column “saturated fat (PDV)”. Based off of the previously mentioned article from the USDA,[^1] there are 9 calories per gram of saturated fat, making it one of the main sources of calories among all macronutrients. Due to this observation, we knew that we wanted to use the saturated fat feature as one of our predictors. However, most of the data for this feature was clustered around the center of the data. Additionally, there was a lack of an equational relationship between saturated fat and calories. To solve this dilemma, we decided to create categorical values in the feature. We grouped the saturated fat amounts into three different groups: high, medium, and low. Higher bins correspond with greater calorie amounts and vice versa.

For our model, we decided to keep using the SGDRegressor and used GridSearchCV to find the best hyperparameters using 10-Fold cross validation. The best hyperparameters that we found were using an alpha level of 0.01 with a penalty of “l2”. Overall, from our Baseline Model’s R^2 value of 0.73, we were able to improve our model’s score to a final R^2 of 0.9758. 

## Fairness Analysis

<iframe src="assets/file-n_steps_vs_cal.html" width=800 height=600 frameBorder=0></iframe>
As you can see from the graph showing the relationship between number of steps and  amount of calories, a lot more recipes clustered around the smaller n_steps with a larger range of calories. As the steps increased, there were overall fewer calories and recipes. We wanted to see if our model was fair for higher versus lower amounts of steps, despite there being different ranges in calories and number of recipes in general. We decided to use RMSE as our evaluation metric since, in absolute terms, it summarizes how well a regression model can predict the value of a response variable. We ran a permutation test to see if the difference in RMSE is significant for short versus long recipes using the following hypotheses: 

- Null Hypothesis: Our model is fair. Its RMSE for short recipes and long recipes are roughly the same, and any differences are due to random chance.
- Alternative Hypothesis: Our model is unfair. Its RMSE for short recipes is lower than its RMSE for long recipes.

Test statistic: Difference in RMSE (short minus long).
Significance level: 0.01.

<iframe src="assets/file-fairness.html" width=800 height=600 frameBorder=0></iframe>
As shown in the graph above, we got a p-value of 0.111, which is greater than our significance level of 0.01. Therefore, we failed to reject the null hypothesis. There is not sufficient evidence to support the claim that our model’s RMSE for short recipes is lower than its RMSE for long recipes.


[^1]:<a href="https://www.usda.gov/media/press-releases/2014/01/16/american-adults-are-choosing-healthier-foods-consuming-healthier">Food and Nutrition Information Center (FNIC)</a>
