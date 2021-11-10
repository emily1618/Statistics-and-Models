# Using KNN to predict the outcome

_Highlights:_
- KNN is a nonparametic model.
- We used all the data to make each prediction and we made many predictions.
- The value of K is picked by trial and error.  
- Use KNN as an alternative to SLR.

## Table of Content
🔵 [Blue or Orange](#blue-or-orange)

💹 [Predict the Sales](#predict-the-sales)

## Blue or Orange
Problem: Reproduce the plot with the same grid points and the same 12 data points. Run KNN to reproduce the decison boundary. 

_Objective: Using KNN method in R to observe what points the model picked._

![2 14](https://user-images.githubusercontent.com/62857660/141152087-37bf3f7c-c034-4c92-b548-839024ec8e9a.png)

#### Step 1: Reproduce the grid points.
```
x <- seq(0, 49, by=1)
y <- seq(0, 49, by=1)
data <- expand.grid(x,y)
names(data)[1] <- "x"
names(data)[2] <- "y"

#data
plot(data, xlab="X", ylab="Y", pch = 20, cex = .5, col="grey")

X <- c(16,3,21,9,46,40,34,28,40,18,34,27)
Y <- c(2,17,24,32,37,42,6,12,17,37,42,46)
Color <- c(rep("blue",6),rep("orange",6))
grid.dat <- data.frame(X,Y,Color)
grid.dat

plot(data, xlab="X", ylab="Y", pch = 20, cex = .5, col="grey")
points(grid.dat[1:6,], col="blue")
points(grid.dat[7:12,], col="orange")
```
![image](https://user-images.githubusercontent.com/62857660/141152955-e5b6b45d-139b-48bc-b03b-b6e0c3b22f89.png)

#### Step 2: Run KNN (assume K=3) on each grid point to decide whether to predict it as :large_blue_circle: or :orange_circle:.
```
k <- 3
result <- NULL

for (i in 1:nrow(data)) {
    gridwithdistance <-mutate(grid.dat,D=sqrt((X-data$x[i])^2+(Y-data$y[i])^2))
    
    votes <- slice_min(gridwithdistance, D, n=k) #find the top 3
    answer <- ifelse(sum(votes=='blue')>= ((k+1)/2),'blue','orange') #vote to get blue or orange
    result[i] <- answer  #store the winner in a vector
  }

#list of all winner color for each set of point
plot(data, xlab="X", ylab="Y", pch = 20, cex = .5, col=result)
points(grid.dat[1:6,],pch='o',cex=1.8, col="blue", lwd = 4)
points(grid.dat[7:12,],pch='o',cex =1.8, col="orange", lwd =4)
```
![image](https://user-images.githubusercontent.com/62857660/141154154-5f32e83c-3dcf-4a91-88f4-c0a247822c2f.png)


## Predict the Sales
Problem: Predict the sales result.

_Objective: Choosing a value for K and using KNN method in R to predict the sales at two different points for the following dataset._

![Screenshot 2021-11-10 112622](https://user-images.githubusercontent.com/62857660/141164213-8d7e5aaf-83c3-4c31-9f6a-d3576b091458.jpg)

#### Step 1: Run and plot a SLR model
```{r}
x0 <- c(8, 38)
mod.TV <- lm(sales ~ TV, data= advertising)
yhat <- predict.lm(mod.TV, newdata = data.frame(TV = x0))
plot(sales ~ TV, data = advertising)
lines(advertising$TV, mod.TV$fitted.values, col=2, lwd=2)
points(x0, yhat, col=2, cex=1.2, pch=19)
```
![Screenshot 2021-11-10 115027](https://user-images.githubusercontent.com/62857660/141166378-378d0b50-42ea-4771-a58f-8b1d93c41226.jpg)

#### Step 2: Predict for x_0 = 8 and x_0 = 38 using KNN regression with K = 5.  
```
x0 <- 8
advertising <- data.frame(advertising, d0 = abs(advertising$TV - x0))
yhat10 <- advertising %>% arrange(d0) %>% head(5) %>% select(sales) %>% unlist() %>% mean()

x0 <- 38
advertising$d0 <- abs(advertising$TV - x0)
yhat50 <- advertising %>% arrange(d0) %>% head(5) %>% select(sales) %>% unlist() %>% mean()

plot(sales ~ TV, data = advertising)
lines(advertising$TV, mod.TV$fitted.values, col=2, lwd=2)
points(c(8, 38), c(yhat10, yhat40), pch=19, cex = 1.2, col=5)
points(c(8, 38), yhat, col=2, cex=1.2, pch=19)
```
![image](https://user-images.githubusercontent.com/62857660/141165484-2b1aef01-dbd4-4e1a-96f1-bb8039f7e298.png)


#### Step 3: Compute KNN for every observation.  
```
advertising <- read.csv('Advertising.csv', row.names=1)
plot(sales ~ TV, data = advertising)
lines(advertising$TV, mod.TV$fitted.values, col=2, lwd=2)

advertising <- arrange(advertising, TV)
advertising <- data.frame(advertising, d0 = NA)

n <- dim(advertising)[1]
yhat <- NULL
K <- 5

for(i in 1:n){
  x0 <- advertising$TV[i]
  advertising$d0 <- abs(advertising$TV - x0)
  yi <- advertising %>% arrange(d0) %>% head(K) %>% select(sales) %>% unlist() %>% mean()
  yhat <- c(yhat, yi)
}

lines(advertising$TV, yhat, col=6, lwd=2)
```
![image](https://user-images.githubusercontent.com/62857660/141167636-a09652fe-57e9-46a1-a7df-d3a1248b3def.png)

