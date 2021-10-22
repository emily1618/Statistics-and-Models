## 🍰 Delicious Treats with Plot3d


![000005](https://user-images.githubusercontent.com/62857660/138489170-b701b844-6006-4af6-be57-bb062a9fd928.png)

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

![cake3](https://user-images.githubusercontent.com/62857660/135530573-08ae7562-d26f-4906-8bb8-5d0214ad1c97.PNG)![donut](https://user-images.githubusercontent.com/62857660/135559949-47e22486-7db4-4388-988d-7046c1209dc4.JPG)

