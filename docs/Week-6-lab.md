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
##       [,1]      [,2]      [,3]      [,4]       [,5]       [,6]      [,7]
##  [1,]   NA 0.5530723 0.5728337 0.9045692 0.23489788 0.77424993 0.9013671
##  [2,]   NA        NA 0.9562368 0.4565661 0.03558100 0.61896066 0.6210222
##  [3,]   NA        NA        NA 0.4729998 0.03562992 0.64799815 0.6445325
##  [4,]   NA        NA        NA        NA 0.28137558 0.65667378 0.8008826
##  [5,]   NA        NA        NA        NA         NA 0.05815621 0.1613390
##  [6,]   NA        NA        NA        NA         NA         NA 0.8791864
##  [7,]   NA        NA        NA        NA         NA         NA        NA
##  [8,]   NA        NA        NA        NA         NA         NA        NA
##  [9,]   NA        NA        NA        NA         NA         NA        NA
## [10,]   NA        NA        NA        NA         NA         NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.9728429 0.8609993 0.6201856
##  [2,] 0.4402153 0.4528646 0.2023890
##  [3,] 0.4576273 0.4683010 0.2084567
##  [4,] 0.8567534 0.9495371 0.7096744
##  [5,] 0.1232413 0.3513348 0.4366224
##  [6,] 0.7126550 0.6331254 0.3217444
##  [7,] 0.9063073 0.7638280 0.5065435
##  [8,]        NA 0.8116613 0.5055871
##  [9,]        NA        NA 0.7809599
## [10,]        NA        NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 2
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
##          5% 
## 0.001601691
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
