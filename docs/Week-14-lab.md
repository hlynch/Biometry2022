Week 14 Lab
=============

In lab we'll go through 

1. Some practice with PCA using the semester survey results

2. Some practice with GLMs using the semester survey results

There are a number of functions you could use in R to do principal components analysis. We will use the 'prcomp' function, but there is a very closely related function called 'princomp' as well as a function called 'principal' which is in the 'psych' package.



```r
readings<-read.csv("~/Dropbox/Biometry/Week 14 Multivariate analyses and Review/Week 14 Lab/Readings 2022.csv",header=T)

missing<-is.na(readings$Useful)|is.na(readings$Difficult)|is.na(readings$Interesting)
Useful<-aggregate(readings$Useful[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

Difficult<-aggregate(readings$Difficult[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

Interesting<-aggregate(readings$Interesting[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

#Length.means.readings<-aggregate(readings$Length[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

pca.result<-prcomp(~Useful+Interesting+Difficult,retx=T)
```

Before printing out the result, let's make sure everyone understands what I was doing with the aggregate commands, and how the 'prcomp' function input works. 

To print out a summary of the PCA, we use


```r
summary(pca.result)
```

```
## Importance of components:
##                           PC1    PC2    PC3
## Standard deviation     0.9508 0.7149 0.4025
## Proportion of Variance 0.5732 0.3240 0.1027
## Cumulative Proportion  0.5732 0.8973 1.0000
```

We see that PCA1 is associated with over 57% of the variation in responses. So, what is PCA1?


```r
pca.result$rotation
```

```
##                     PC1       PC2        PC3
## Useful       0.08589313 0.4406129  0.8935785
## Interesting  0.56793537 0.7152477 -0.4072716
## Difficult   -0.81857913 0.5424767 -0.1888048
```

PCA1 is an axis which describes papers that are more Interesting and less Difficult, with a very small weight towards papers that are Useful. In other words, a large positive PCA1 score would be associated with an interesting paper that was easy to read. Note that the principal components denote an axis, but the direction is arbitrary. Since no direction is implied by the sign, we do not interpret this as saying that most papers were interesting and easy. Instead we would say that the papers largely fall along a common axis in which Interesting/Easy to read papers are at one end, and Boring/Difficult to read papers are at the other end. (For now I am ignoring the small influence of Useful on PCA1.)

We can visualize this using the function 'biplot'


```r
biplot(pca.result)
```

<img src="Week-14-lab_files/figure-html/unnamed-chunk-4-1.png" width="672" />

Biplots take some getting used to, and when they have many more dimensions, they become increasingly difficult to interpret. However, papers high on PC1 are generally Interesting and Easy to read and papers low on PC1 are generally Boring and more Difficult to read. Papers high on PC2 are generally more Interesting and Difficult and Useful and papers low on PC2 are generally less Interesting but Easy to read and less Useful.

Which papers were highly positive on PC2? These are papers that were both Useful and Interesting.


```r
readings[readings$Index==15,1][1]
```

```
## [1] "Siddhartha, R. D., E. B. Fowlkes, and B. Hoadley. 1989. Risk analysis of the space shuttle: Pre-challenger prediction of failure. Journal of the American Statistical Association 84(408): 945-957."
```

```r
readings[readings$Index==25,1][1]
```

```
## [1] "Johnson, J.B., and K.S. Omland. 2004. Model selection in ecology and evolution. TRENDS in Ecology and Evolution 19(2): 101-108."
```

I totally agree on the Siddhartha paper!

You can play around with this yourself and see why I added the [1] at the end. When I pull out the rows with the Index identified by the PCA, I get the list of all entries (since we had >1 team rating the papers) and so I only print the first one.

Which paper fell out along the Difficult axis?


```r
readings[readings$Index==18,1][1]
```

```
## [1] "Hurlbert, S. H. 1984. Pseudoreplication and the design of ecological field experiments. Ecological Monographs 54(2): 187-211."
```

Indeed, Hurlbert has a lot of information. Also, 


```r
readings[readings$Index==2,1][1]
```

```
## [1] "Bolker Chapter 6 (Posted on Bb)"
```

Alas, poor Bolker! Bolker is often rated as difficult. I keep this chapter around because his thinking is so "spot on" and the material in his book will serve you well if you continue on doing quantitative modelling.

These papers are the only stand outs this year. The other readings cluster together.

One thing to keep in mind is that a PCA identifies *variation* in the dataset. It's worth putting these numbers in context of the overall means.


```r
mean(Useful)
```

```
## [1] 3.992857
```

```r
mean(Difficult)
```

```
## [1] 2.5
```

```r
mean(Interesting)
```

```
## [1] 3.632143
```

So the average reading scored pretty high for being Useful and Interesting and was rated of medium Difficulty.

You might be interested in how these ratings have changed over time (I was!):

<div class="figure" style="text-align: center">
<img src="Readings time series.png" alt="Readings ratings through the years" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-9)Readings ratings through the years</p>
</div>

We can do the same for the problem sets:


```r
PS<-read.csv("~/Dropbox/Biometry/Week 14 Multivariate analyses and Review/Week 14 Lab/ProblemSets 2022.csv",header=T)

missing<-is.na(PS$Useful)|is.na(PS$Difficult)|is.na(PS$Interesting)

Useful.means.PS<-aggregate(PS$Useful[!missing], by=list(Index=PS$Week[!missing]),FUN=mean)$x

Difficult.means.PS<-aggregate(PS$Difficult[!missing], by=list(Week=PS$Week[!missing]),FUN=mean)$x

Interesting.means.PS<-aggregate(PS$Interesting[!missing], by=list(Week=PS$Week[!missing]),FUN=mean)$x

pca.result<-prcomp(~Useful.means.PS+Interesting.means.PS+Difficult.means.PS,data=PS,retx=T)
```

Notice that it has simply labeled them in order, so 7=Week #9 PS, 8=Week #10 PS, 9=Week #11 PS, 10=Week #12 PS, and 11=Week #13 PS.

To print out a summary of the PCA, we use


```r
summary(pca.result)
```

```
## Importance of components:
##                           PC1    PC2    PC3
## Standard deviation     0.8474 0.6995 0.2560
## Proportion of Variance 0.5641 0.3844 0.0515
## Cumulative Proportion  0.5641 0.9485 1.0000
```

We see that for the problem sets, PC1 is even more dominant (85% of the variation). So, what is PCA1?


```r
pca.result$rotation
```

```
##                             PC1        PC2        PC3
## Useful.means.PS      -0.7894404 -0.1122903 -0.6034689
## Interesting.means.PS -0.4937346 -0.4679620  0.7329650
## Difficult.means.PS    0.3647054 -0.8765857 -0.3139865
```

PC1 almost evenly combines all three factors, and the axis divides problem sets judged Useless/Boring/Difficult and those that are Useful/Interesting/Easy. (Reminder: the signs of the PCs is arbitrary, so the signs on the rotation could have all be flipped.) Looking across all the PC axes, we want papers that are low (negative) on PC1 and low (negative) on PC2. PC3 is a toss up, because that axis represents a trade-off between Useful and Interesting.


```r
biplot(pca.result)
```

<img src="Week-14-lab_files/figure-html/unnamed-chunk-13-1.png" width="672" />

We can see that problem set 6 is the one that is really driving variation here! If we were to eliminate week 6, the others are all varying primarily on PC2.

Again, looking at the means:


```r
mean(Useful.means.PS)
```

```
## [1] 4.348485
```

```r
mean(Difficult.means.PS)
```

```
## [1] 3.643939
```

```r
mean(Interesting.means.PS)
```

```
## [1] 4.155303
```

The problem sets overall rated as being very Useful and Interesting but also sort of Difficult. 

The changes through time show a significant trend (not shown) in Interesting, but generally problem sets are becoming more interesting over time and are becoming slightly more Useful but also a hair more Difficult as well.

<div class="figure" style="text-align: center">
<img src="Problem set time series.png" alt="Problem set ratings through the years" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-15)Problem set ratings through the years</p>
</div>


Missing at random - practice with GLMs
--------------------------------------

This year, we had no missing data! Usually, there are missing data for some of the readings, so for the purposes of the rest of the lab, I will use last year's data. 


```r
readings<-read.csv("~/Dropbox/Biometry/Week 14 Multivariate analyses and Review/Week 14 Lab/Readings 2021.csv",header=T)
missing<-is.na(readings$Useful)|is.na(readings$Difficult)|is.na(readings$Interesting)
Useful<-aggregate(readings$Useful[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

Difficult<-aggregate(readings$Difficult[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

Interesting<-aggregate(readings$Interesting[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x

Length.means.readings<-aggregate(readings$Length[!missing], by=list(Index=readings$Index[!missing]),FUN=mean)$x
```

One could ask the question, are these data missing at random? In the problem set for Week #13, we completed the dataset using random imputation. In other words, we assumed that data were missing at random and we drew with replacement from the other values to replace missing datapoints. However, in this case, it seems likely that data are not missing at random. I suspect that papers were not evaluated because no one read them, and that something about the papers may predict whether the papers were read or not. We can answer this question by constructing a model for "missingness" which assumes that the probability of being evaluated is distributed as Binom(n,p) where p is the probability of being evaluated (and presumably, of having been read in the first place).

First, I need to go through the data and figure out how many times a paper was evaluated. 


```r
num.missing<-vector(length=max(readings$Index))
for (i in 1:max(readings$Index))
{
  num.missing.useful<-sum(as.numeric(is.na(readings$Useful[readings$Index==i])))
  num.missing.difficult<-sum(as.numeric(is.na(readings$Difficult[readings$Index==i])))
  num.missing.interesting<-sum(as.numeric(is.na(readings$Interesting[readings$Index==i])))
  max.missing<-max(num.missing.useful,num.missing.difficult,num.missing.interesting)
  num.missing[i]<-max.missing
}
```

For simplicity, I am considering "evaluated" as evaluated for all three categories (Useful, Difficult, and Interesting).

Now I use a Binomial GLM to model the probability of being evaluated as a function of Useful, Interesting, and Difficult (as rated by the other groups). Note that there were 5 groups total, so n=5.


```r
fit<-glm(cbind(5-num.missing,num.missing)~Useful+Difficult+Interesting,family="binomial")
summary(fit)
```

```
## 
## Call:
## glm(formula = cbind(5 - num.missing, num.missing) ~ Useful + 
##     Difficult + Interesting, family = "binomial")
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.6523   0.2319   0.8001   1.0451   1.4609  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)  
## (Intercept) -1.15040    2.82422  -0.407    0.684  
## Useful       1.09309    0.64480   1.695    0.090 .
## Difficult    0.04547    0.45650   0.100    0.921  
## Interesting -0.05625    0.51023  -0.110    0.912  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 49.173  on 31  degrees of freedom
## Residual deviance: 45.877  on 28  degrees of freedom
## AIC: 70.197
## 
## Number of Fisher Scoring iterations: 5
```

None of the covariates are significant, which isn't a surprise. Because I (accidentally) didn't pass out the Week 14 readings until Monday night, it was only the Week 14 readings that had many NAs, so we would not expect (except possibly by chance) any association with the factors of Useful, Difficult, or Interesting. 

We might suspect a high degree of multicollinearity among the predictors. We can use PCA to create new orthogonal covariates which (more efficiently) capture the variability in the survey results. 

I will rerun the PCA for the readings. (Keep in mind that we are now looking at last year's data.)


```r
pca.result<-prcomp(~Useful+Interesting+Difficult,retx=T)
summary(pca.result)
```

```
## Importance of components:
##                           PC1    PC2    PC3
## Standard deviation     0.9084 0.5395 0.3891
## Proportion of Variance 0.6510 0.2296 0.1194
## Cumulative Proportion  0.6510 0.8806 1.0000
```

```r
pca.result$rotation
```

```
##                    PC1       PC2        PC3
## Useful      -0.1571361 0.7179282  0.6781500
## Interesting -0.6425818 0.4471147 -0.6222356
## Difficult    0.7499312 0.5335425 -0.3910697
```

PCA1 captures about 91% of the variability, so we try using just PCA1 in our GLM. 


```r
fit<-glm(cbind(5-num.missing,num.missing)~pca.result$x[,1],family="binomial")
summary(fit)
```

```
## 
## Call:
## glm(formula = cbind(5 - num.missing, num.missing) ~ pca.result$x[, 
##     1], family = "binomial")
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.7500   0.5157   0.9595   1.0049   1.1519  
## 
## Coefficients:
##                   Estimate Std. Error z value Pr(>|z|)    
## (Intercept)         2.2729     0.2723   8.349   <2e-16 ***
## pca.result$x[, 1]  -0.1135     0.2932  -0.387    0.699    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 49.173  on 31  degrees of freedom
## Residual deviance: 49.026  on 30  degrees of freedom
## AIC: 69.346
## 
## Number of Fisher Scoring iterations: 5
```

Still not significant.


```r
fit<-glm(cbind(5-num.missing,num.missing)~Length.means.readings,family="binomial")
summary(fit)
```

```
## 
## Call:
## glm(formula = cbind(5 - num.missing, num.missing) ~ Length.means.readings, 
##     family = "binomial")
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.7169   0.2359   0.9747   1.0418   1.0753  
## 
## Coefficients:
##                       Estimate Std. Error z value Pr(>|z|)    
## (Intercept)            2.05040    0.40637   5.046 4.52e-07 ***
## Length.means.readings  0.02434    0.03673   0.663    0.507    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 49.173  on 31  degrees of freedom
## Residual deviance: 48.612  on 30  degrees of freedom
## AIC: 68.932
## 
## Number of Fisher Scoring iterations: 5
```

Length is not statistically correlated with whether a paper was rated, which is not surprising in this case because we know that the only papers not rated were the ones for the last week.
