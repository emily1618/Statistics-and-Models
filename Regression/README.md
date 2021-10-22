## Linear Regression Model Improvement, Leverage Points and Prediction Intervals


![000005](https://user-images.githubusercontent.com/62857660/138489170-b701b844-6006-4af6-be57-bb062a9fd928.png)

- Check influential and leverage points with ```cook.distance``` 
- Adding quadratic terms and remove outliers to improve the model.
- Build a standard deviation table using dplyr and Scale-Location model to check for variance changes.
- Transform the data to further improve the model and build a prediction table using ```predict.lm```.


#### Code sample for checking for leverage points, outliers and influential points:
```
leverage_points <- subset(table, leverage > (4/nrow(airfares)))
outliers <- subset(leverage_points, abs(StdResiduals) > 2)
subset(cooks.distance(fare.mod), cooks.distance(fare.mod) > 4/(nrow(airfares)- 2))
```

#### Code sample for removing outliers and adding quadratic term to improve the model: 
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

#### Code sample for 95% prediction intervals when we have 2 or 8 baristas:
```
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(2)), interval = 'pred', level = 0.95)
predict.lm(coffee.mod, newdata = data.frame(Baristas = c(8)), interval = 'pred', level = 0.95)
```

