## SLR Model Improvement, Leverage Points and Prediction Intervals

Highlights:
- Examine the independent and dependent variables by building a linear regression model using  ```lm()```
- Check influential and leverage points with ```cook.distance``` 
- Adding quadratic terms and remove outliers to improve the model.
- Build a standard deviation table using dplyr and Scale-Location model to check for variance changes.
- Transform the data to further improve the model and build a prediction table using ```predict.lm```

#
## Airfare Model
#### Step 1: Build a slr using ```lm()```
```
fare.mod <- lm(Fare~Distance, data=airfares)
plot(fare.mod)
```
#### Step 2: Check for leverage points using standardized residual table + cook distance:
```
table <- data.frame(Case = 1:nrow(airfares), 
                    Fare = airfares$Fare,
                    Distance = airfares$Distance,
                    Residuals = fare.mod$residuals,
                    leverage = lm.influence(fare.mod)$hat,
                    StdResiduals = rstandard(fare.mod))

leverage_points <- subset(table, leverage > (4/nrow(airfares)))
outliers <- subset(leverage_points, abs(StdResiduals) > 2)
subset(cooks.distance(fare.mod), cooks.distance(fare.mod) > 4/(nrow(airfares)- 2)) #pont 13 and 17 are outliers
```
#### Step 3: Examine the model and ```plot()```:
1. From the residuals vs fitted model, it's not a straight line, so between predictor and response does not show a linear relationship. Perhaps an another model such as a quadratic model will explaining the data in this set better. 
2. Second plot the Normal Q-Q shows the residuals are mostly normally distributed, with some points departed off in the beginning of the line. 
3. Good to have a horizontal line (homoscedasticity) on Scale-Location model. Residuals are wider spread at the middle of the plot. The assumption of the variance is constant is not as "valid". 
4. Two points are not within the Cook's distance in the Residuals vs Leverage model, those will be the influential outliers. Checking the the standardized residuals, we will find the 2 leverage points on 13 and 17:

![000005 (1)](https://user-images.githubusercontent.com/62857660/138492474-fe2ab316-ff91-4d60-a13f-a66fdc0f5485.png)

#### Step 4: Remove the bad leverage points or outliers and add quadratic term:
```
fare2 <- filter(airfares, !(City %in% c(13,17)) ) #remove the outliers discovered in step 2
fare2.mod <- lm(Fare~Distance, data=fare2)

fare2 <- mutate(fare2, Distance2 = Distance^2)  #adding quadratic term to data.frame
fare2.mod <- lm(Fare ~ Distance + Distance2, data = fare2) #adding quadratic term to model

which.max((hatvalues(fare2.mod))) #checking the points again to show which # with the largest leverage statistics
which(rstandard(fare2.mod)< -2 | rstandard(fare2.mod)>2) #shows which # will be the new outliers or bad leverage points

subset(cooks.distance(fare2.mod), cooks.distance(fare2.mod) > 4/(nrow(fare2)- 2))#On the Cook's distance plot, we discovered an another outlier/bad leverage point at 15. 

```

#### Step 5: Obtain R-Squared from summary to see if we improve the model by removing outliers and/or add quadratic terms. It seems so because the original model seem to fit the data well. However, after removing the 2 outliers, we improved the R-Squared from 99.4% (original model) to 99.89% (new model). The line is straighter for the new model on Scale-Location mode, the variance stays more constant and doesn't have a steep uphill toward the end of the line compared to the original model. 
```
summary(fare.mod)
summary(fare2.mod)
```

#
## Coffee Model
#### Coffee model 95% prediction intervals when we have 2 or 8 baristas:
```
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(2)), interval = 'pred', level = 0.95)
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(8)), interval = 'pred', level = 0.95)
```

### Coffee model 95% prediction interval table output
![table](https://user-images.githubusercontent.com/62857660/138491862-e5465c83-d43c-4ab8-91a3-0b679b2da7d8.JPG)
