Week 6 Lab
=============
  
Today we will do a short exercise to illustrate the permutation method of dealing with multiple comparisons.

First, we will simulate 10 groups of data (say, heights) from the *same* distribution using the normal distribution and put the data for each group in a list for easy access:


```r
data.all<-list()
for (i in 1:10)
  {
    data.all[[i]]<-rnorm(10)  #Note the double brackets for a list
  }
```

Now we can compare each group pairwise using a t.test.


```r
p.values<-matrix(ncol=10,nrow=10)
for (i in 1:9)
  {
    for (j in (i+1):10)
      {
        p.values[i,j]<-t.test(data.all[[i]],data.all[[j]])$p.value 
      }
  }
p.values
```

```
##       [,1]      [,2]      [,3]      [,4]      [,5]       [,6]       [,7]
##  [1,]   NA 0.8138093 0.6925836 0.5670418 0.5668318 0.17699798 0.15953202
##  [2,]   NA        NA 0.5153191 0.6386281 0.6613785 0.12413734 0.09809714
##  [3,]   NA        NA        NA 0.4137840 0.3626386 0.29710665 0.30073634
##  [4,]   NA        NA        NA        NA 0.8355853 0.12548899 0.12224080
##  [5,]   NA        NA        NA        NA        NA 0.09167378 0.07093289
##  [6,]   NA        NA        NA        NA        NA         NA 0.89078155
##  [7,]   NA        NA        NA        NA        NA         NA         NA
##  [8,]   NA        NA        NA        NA        NA         NA         NA
##  [9,]   NA        NA        NA        NA        NA         NA         NA
## [10,]   NA        NA        NA        NA        NA         NA         NA
##            [,8]      [,9]     [,10]
##  [1,] 0.2967031 0.6476383 0.6320481
##  [2,] 0.1624963 0.4104862 0.4893164
##  [3,] 0.5597734 0.9851878 0.8974366
##  [4,] 0.2133107 0.3855030 0.3887031
##  [5,] 0.1161993 0.2781772 0.3570718
##  [6,] 0.5161534 0.2577423 0.3812162
##  [7,] 0.5621611 0.2460680 0.4047858
##  [8,]        NA 0.4772686 0.7050071
##  [9,]        NA        NA 0.8726581
## [10,]        NA        NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 0
```

We could correct this using the Bonferonni method:


```r
k<-45
new.threshold.B<-0.05/k
new.threshold.B
```

```
## [1] 0.001111111
```

```r
false.positives.B<-sum(p.values<new.threshold.B,na.rm=T)
false.positives.B
```

```
## [1] 0
```

We could correct this using the Dunn-Sidak method:


```r
k<-45
new.threshold.DS<-1-((1-0.05)^(1/k))
new.threshold.DS
```

```
## [1] 0.001139202
```

```r
false.positives.DS<-sum(p.values<new.threshold.DS,na.rm=T)
false.positives.DS
```

```
## [1] 0
```

We could correct this using the randomization method. This requires simulating data under the null hypothesis to generate a null distribution of p-values.



```r
p.values.all<-c()
min.p.values.all<-c()
for (k in 1:1000)
  {
    data.null<-list()
    for (i in 1:10)
      {
        data.null[[i]]<-rnorm(10)  #Note the double brackets for a list
      }
    p.values.null<-matrix(ncol=10,nrow=10)
    for (i in 1:9)
      {
        for (j in (i+1):10)
          {
            p.values.null[i,j]<-t.test(data.null[[i]],data.null[[j]])$p.value 
          }
      }
    p.values.all<-c(p.values.all,c(p.values.null)[!is.na(c(p.values.null))])
    min.p.values.all<-c(min.p.values.all,min(c(p.values.null)[!is.na(c(p.values.null))]))
  }
new.threshold.R<-quantile(min.p.values.all,probs=c(0.05))
new.threshold.R
```

```
##         5% 
## 0.00221306
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
