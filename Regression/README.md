## Linear Regression Model Improvement, Leverage Points and Prediction Intervals

- Examine the independent and dependent variables by building a linear regression model using  ```lm()```
- Check influential and leverage points with ```cook.distance``` 
- Adding quadratic terms and remove outliers to improve the model.
- Build a standard deviation table using dplyr and Scale-Location model to check for variance changes.
- Transform the data to further improve the model and build a prediction table using ```predict.lm```

![000005](https://user-images.githubusercontent.com/62857660/138492173-639a5cf5-1acf-434d-affc-a12be3b5c00c.png)

#### Explanation for the ORIGINAL airfares model plot using ```plot(airfares)```:
1. From the residuals vs fitted model, it's not a straight line, so between predictor and response does not show a linear relationship. Perhaps an another model such as a quadratic model will explaining the data in this set better. 
2. Second plot the Normal Q-Q shows the residuals are mostly normally distributed, with some points departed off in the beginning of the line. 
3. Good to have a horizontal line (homoscedasticity) on Scale-Location model. Residuals are wider spread at the middle of the plot. The assumption of the variance is constant is not as "valid". 
4. Two points are not within the Cook's distance in the Residuals vs Leverage model, those will be the influential outliers. Checking the the standardized residuals, we will find the 2 leverage points on 13 and 17:

#### Airfares model code example for checking for leverage points, outliers and influential points:
```
leverage_points <- subset(table, leverage > (4/nrow(airfares)))
outliers <- subset(leverage_points, abs(StdResiduals) > 2)
subset(cooks.distance(fare.mod), cooks.distance(fare.mod) > 4/(nrow(airfares)- 2))
```

#### Airfares model code example for removing outliers and adding quadratic term to improve the model: 
```
step 1: remove outliers:
fare2 <- filter(airfares, !(City %in% c(13,17)) )
fare2.mod <- lm(Fare~Distance, data=fare2)

step 2: add quadratic term:
fare2 <- mutate(fare2, Distance2 = Distance^2)  #adding quadratic term to data.frame
fare2.mod <- lm(Fare ~ Distance + Distance2, data = fare2) #adding quadratic term to model

step 3: checking the points again:
which.max((hatvalues(fare2.mod))) #shows which # with the largest leverage statistics
which(rstandard(fare2.mod)< -2 | rstandard(fare2.mod)>2) #shows which # will be the new outliers or bad leverage points
```

#### Coffee model 95% prediction intervals when we have 2 or 8 baristas:
```
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(2)), interval = 'pred', level = 0.95)
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(8)), interval = 'pred', level = 0.95)
```

### Coffee model 95% prediction interval table output
![table](https://user-images.githubusercontent.com/62857660/138491862-e5465c83-d43c-4ab8-91a3-0b679b2da7d8.JPG)
