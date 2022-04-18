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
##       [,1]      [,2]       [,3]       [,4]      [,5]      [,6]      [,7]
##  [1,]   NA 0.9168522 0.06201274 0.98204510 0.3035766 0.2871321 0.7518740
##  [2,]   NA        NA 0.03145437 0.90106187 0.2224174 0.2043845 0.6599311
##  [3,]   NA        NA         NA 0.07870774 0.3382818 0.3127788 0.1254327
##  [4,]   NA        NA         NA         NA 0.3346687 0.3194348 0.7769008
##  [5,]   NA        NA         NA         NA        NA 0.9969884 0.4901295
##  [6,]   NA        NA         NA         NA        NA        NA 0.4741594
##  [7,]   NA        NA         NA         NA        NA        NA        NA
##  [8,]   NA        NA         NA         NA        NA        NA        NA
##  [9,]   NA        NA         NA         NA        NA        NA        NA
## [10,]   NA        NA         NA         NA        NA        NA        NA
##            [,8]      [,9]      [,10]
##  [1,] 0.2846624 0.1982105 0.06364106
##  [2,] 0.2100157 0.1419338 0.03389979
##  [3,] 0.4142616 0.6620379 0.94613056
##  [4,] 0.3136045 0.2217195 0.07959704
##  [5,] 0.9326994 0.7032328 0.33383638
##  [6,] 0.9328861 0.6957281 0.31128237
##  [7,] 0.4576777 0.3281561 0.12629021
##  [8,]        NA 0.7710488 0.40447909
##  [9,]        NA        NA 0.63779184
## [10,]        NA        NA         NA
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
## 0.001972115
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
