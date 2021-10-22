# SLR Model, Model Improvement, Leverage Points/Outliers, Confidence Level, and Prediction Intervals

Highlights:
- Examine the predictor and response variables by building a linear regression model using  ```lm()``` and ```plot()```
- Check influential and leverage points with ```cook.distance``` 
- Adding quadratic terms and remove outliers to improve the model.
- Build a standard deviation table using dplyr and Scale-Location model to check for variance changes.
- Transform the data to further improve the model and build a prediction table using ```predict.lm```
- Check confidence level using ```confint()```


## Table of Content
✈[Airfare Model](#airfare-model)

☕[Coffee Model](#coffee-model)


## Airfare Model
#### Step 1: Build a regression model using ```lm()```
```
airfares <- read.delim("airfares.txt")
fare.mod <- lm(Fare~Distance, data=airfares)
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
![2](https://user-images.githubusercontent.com/62857660/138510831-edc4f8b3-c2d3-450a-bc36-bdccf1adfcaf.JPG)

#### Step 3: Examine the model and ```plot()```:
```
plot(fare.mod)
```
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
![2](https://user-images.githubusercontent.com/62857660/138511042-60bd5ee7-8f5b-4d59-9080-9100879cb828.JPG)


#### Step 5: Obtain R-Squared from summary to see if we improve the model by removing outliers and/or add quadratic terms. It seems so because the original model seem to fit the data well. However, after removing the 2 outliers, we improved the R-Squared from 99.4% (original model) to 99.89% (new model). The line is straighter for the new model on Scale-Location mode, the variance stays more constant and doesn't have a steep uphill toward the end of the line compared to the original model. 
```
summary(fare.mod)
summary(fare2.mod)
```
![3](https://user-images.githubusercontent.com/62857660/138510973-2074f789-6334-45fb-91da-5d92cb8fa607.JPG)

#

## Coffee Model
#### Step 1: Build a slr using ```lm()``` and compute 95% prediction intervals for Y = Customers when x=Baristas=2 and when x=Baristas=8
```{r}
coffee <- read.delim("coffee.txt")
coffee.mod <- lm(Customers~Baristas, data=coffee)
plot(Customers~Baristas, data=coffee) #plotting the regression, you can also use ggplot here and plot in one step. 
abline(coffee.mod)

predict.lm(coffee.mod, newdata = data.frame(Baristas = c(2)), interval = 'pred', level = 0.95)
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(8)), interval = 'pred', level = 0.95)
```
![image](https://user-images.githubusercontent.com/62857660/138508825-c04997a8-790b-426a-8a71-aa455ba0fbfc.png)

#### Step 2: The regression model assumes that the variance of the errors is the same for every value of the predictor variable x.  Check this assumption in four methods. Once we examined the results with the four method, we will see from the standard deviation table, wthe standard deviation of customers increases as x, number of baristas increases. This is a clear sign that the variance of Y increase with the  increase in number of baristas. From the standardized residuals versus x plot, it is evident that the variability in the standardized residuals tend to increase with the number of baristas. It also has a funnel shape, suggesting the error variance is not constant. From r vs x plot, we can see from the fitting line that there is an increasing trend, which suggests the that the variance if errors increases with x.  Similarly, from the scale-location plot, we can see that the variance of the errors increases with the increase in fitted values.

1st method: use a standard deviation table:
```
StdDev_Table <- coffee %>% group_by(Baristas) %>% summarise(Numbers_of_Count = n(), StdDev_Customers = sd(Customers))
```
![std](https://user-images.githubusercontent.com/62857660/138509023-315fa2d5-f73d-4db7-a0f7-9a460bedeeb3.JPG)

2nd method: Plot the standardized residuals versus x = Baristas:
```
StdResiduals <- rstandard(coffee.mod)
std_res_vs_baristas <- data.frame(coffee$Baristas, StdResiduals)
plot(std_res_vs_baristas, xlab="Baristas", ylab="Standardized Residuals", pch=19)
grid()
```
![image](https://user-images.githubusercontent.com/62857660/138509112-919f1b21-62b3-4cb5-972a-e7d39fa5e2f9.png)

3rd method: Compute the square root of the absolute value of the standardized residuals, and fit the  SLR model of r on x = Baristas: 
```
r <- abs(StdResiduals)^0.5
Baristas <- coffee$Baristas
r_vs_Baristas <- data.frame(Baristas, r)
r_vs_Baristas.mod <- lm(r~Baristas, data=r_vs_Baristas)
plot(r_vs_Baristas, pch=19)
abline(r_vs_Baristas.mod, col=2)
grid()
```
![image](https://user-images.githubusercontent.com/62857660/138509391-2017e42e-15d4-4d7c-9dd3-2298e4e60fe6.png)

4th method: Plot r versus the fitted values yhat:
```
coffee.mod <- lm(Customers ~ Baristas, data=coffee)
plot(coffee.mod, which=3)
```
![image](https://user-images.githubusercontent.com/62857660/138509702-79e8643e-9ba1-45ca-8dd7-9a0fa49d9829.png)


#### Step 3: We've shown that the SLR assumption of constant variance **does not hold** and that means our results are no good. One way to solve this problem is by transforming the data. Use `dplyr` to add two columns to `coffee`:
```
coffee1 <- coffee %>% mutate(sqrtbaristas = Baristas^0.5, sqrtcustomers= Customers^0.5)
coffee1.mod <- lm(sqrtcustomers~sqrtbaristas, data=coffee1)
summary(coffee1.mod)
```

#### Step 4: Use this model to compute 95% prediction intervals for Y = Customers when x=Baristas=2 and when x=Baristas=8:
```
predict_2<-predict.lm(coffee1.mod, newdata = data.frame(sqrtbaristas = c(sqrt(2))), interval = 'pred', level = 0.95)
predict_2 <- (predict_2)^2
predict_8<-predict.lm(coffee1.mod, newdata = data.frame(sqrtbaristas = c(sqrt(8))), interval = 'pred', level = 0.95)
predict_8<-(predict_8)^2
```

#### Step 5: Repeat same steps as the original coffee model with the transformed data. The standard deviation table shows that standard deviation of Y (square root of customers) do not follow a trend. This time the standard residual vs square root of Baristas plot did not have the funnel shape, indicating that the variability remains somewhat constant. In the r vs x (square root of Baristas) plot, the residuals do not follow a trend, and appear to be random. The line on the Scale-Location plot also looks more straight instead of a steeper curve up in the end. The residuals are randomly scattered around the fit line with roughly similar variability at all fitted values.These results suggest that the homoscedasticity assumption is valid.
```
* Check the assumption of constant variance for the model with transformed data using the same four checks listed in the previous problem.  Things should look better now.  Result explanation: 
```{r}
#Check 1: Std Dev Table
StdDev_Table1 <- coffee1 %>% group_by(sqrtbaristas) %>% summarise(Numbers_of_Count = n(), StdDev_Customers = sd(sqrtcustomers))
StdDev_Table1

#Check 2: Standardized Residuals vs Baristas
StdResiduals1 <- rstandard(coffee1.mod)
r1 <- abs(StdResiduals1)^0.5

std_res_vs_baristas1 <- data.frame(coffee1$sqrtbaristas, StdResiduals1)
plot(std_res_vs_baristas1, xlab="square root of Baristas", ylab="Standardized Residuals", pch=19)
grid()

#Check 3: r vs Baristas
Baristas1 <- coffee1$sqrtbaristas
r_vs_Baristas1 <- data.frame(Baristas1, r1)
r_vs_Baristas1.mod <- lm(r1~Baristas1, data=r_vs_Baristas1)
plot(r_vs_Baristas1, pch=19)
abline(r_vs_Baristas1.mod, col=2)
grid()

#Check 4: r vs y_hat
coffee1.mod <- lm(sqrtcustomers ~ sqrtbaristas, data=coffee1)
 
plot(coffee1.mod, which=3)
```
![image](https://user-images.githubusercontent.com/62857660/138510610-80326616-4329-4489-968b-251a1af9b902.png)



#### Step 6: Make a data frame to compare the predictions and PI's from the two models
```
x_Baristas <- c("2(transformed data)", "2(raw data)", "8(transformed data)", "8(raw data)")
table_predict <- round(rbind(transformed_2,raw_2,transformed_8,raw_8),0)
table_predict <- data.frame(cbind(x_Baristas, table_predict))
colnames(table_predict)<- c("x,Baristas", "Prediction", "Lower Limit", "Upper Limit")
rownames(table_predict ) <- NULL
table_predict 
```
![table](https://user-images.githubusercontent.com/62857660/138491862-e5465c83-d43c-4ab8-91a3-0b679b2da7d8.JPG)
