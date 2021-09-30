## 🍰 Delicious Treat with Plot3d


![cake4](https://user-images.githubusercontent.com/62857660/135534908-bbad528b-695e-4079-a7b9-b50a30c52682.PNG)

- Test with mutiple dataset and use ```plot3d()``` to  build the dessert.
- Use covariance matrix ```diag()``` in the context of linear algebra in generating data.
- Use MASS package ```mvnorm()``` to generate multivariate normal distribution points to form the "cloud". 

```
#code sample for adding the sprinkles: 

mu.sprinkles <- c(1,6.5,-1.5)
covmat.sprinkles <- diag(c(2,2,2))
cloud.sprinkles <- mvrnorm(n = 100, mu = mu.sprinkles, Sigma = covmat.sprinkles)
plot3d(cloud.sprinkles, type = 'l', add = TRUE, radius = 0.3, col='orange')
```

- Example outputs:

![cake3](https://user-images.githubusercontent.com/62857660/135530573-08ae7562-d26f-4906-8bb8-5d0214ad1c97.PNG)![cake2](https://user-images.githubusercontent.com/62857660/135531562-c9228c59-c786-40d0-8527-35157727761e.PNG)
