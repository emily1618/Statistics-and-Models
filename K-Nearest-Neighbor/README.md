# Using KNN to predict the outcome

_Highlights:_
- Run KNN to produce decision boundary

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
