<img src="/docs/images/multicollinearity/multicollinearity.png"  width="800" />


A key goal of fitting a linear regression model is to isolate the relationship between each independent variable (predictors) and the dependent variable (outcome), but multicollinearlity might be a strong obstacle to reach that goal.

Multicollinearity occurs If a predictor is highly correlated with one or more other predictors, so that the predictors are no longer **"INDEPENDENT"** variables.

Multicollinearlity may not affect the model performance , but certainly , it will affect the model interpretability ,When we interpret a linear regression model (Y=a*X1+b*X2) we state that with one-unit increase in X,Y increases by b units while holding X2 as a constant .In the presence of multicollinearity , this interpretation is no longer valid , as X1 changes , X2 will change as they are highly correlated. So, in short, multicollinearity reduced the statistical power of linear regression. This may in turn disable us to identify the effect of the individual predictors on the target.

One way to test for multicollinearity is using **Variance Inflation Factor (VIF=1/(1-R-squared) )** , its clear from the mentioned forumla that VIF is directly proportional to R-squared coefficient. And R-squared coefficient mainly represents the proportion of the variance for a dependent variable that's explained by an independent variables , so if R-squared for a model is 80% , this means that the predictor (X) was able to explain 80% of the outcome (Y) variability.

Back to multicollinearity testing :) if we have a linear regression model (Y=aX1+bX2+cX3) we are fitting three regression models. <br>
Model 1 : regress X1 based on X2 and X3 . <br>
Model 2 : regress X2 based on X1 and X3 . <br> 
Model 3 : regress X3 based on X1 and X2 . <br>

Then we can calculate the R-squared coefficient and the VIF for each of these three models.In the ideal state , variables X1 , X2 and X3 none of them should be able to explain the variability of the other. so ideally , VIF for each of the above models should approach one , and the higher VIF the stronger the multicollinearity is.

Most research papers consider VIF > 5 is a strong indicator for a multicollinearity problem.

There are lots of potential solutions for multicollineaity , the most popular and simple one is to drop the correlated  predictors. From a practical point of view, there is no point in keeping 2 very similar predictors in our model.

In essence , multicollinearity affects the coefficients and p-values, but it does not influence the predictions, precision of the predictions, and the goodness of the model . If your primary goal is to make predictions and to fit a performant model, and you don’t need to understand the role of each independent variable, you don’t need to reduce severe multicollinearity and its fine to keep the correlated variables in the model,However. if you care about model interpretability and need to understand how each predictor affect the outcome then multicollinarity is a considerable blocker for you.
