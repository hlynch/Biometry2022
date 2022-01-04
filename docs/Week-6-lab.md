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
##       [,1]      [,2]      [,3]      [,4]      [,5]      [,6]      [,7]
##  [1,]   NA 0.9963442 0.2866423 0.9945382 0.8741888 0.7227086 0.8744849
##  [2,]   NA        NA 0.2789900 0.9905570 0.8686337 0.7201855 0.8675605
##  [3,]   NA        NA        NA 0.2694005 0.3601113 0.1395329 0.2981176
##  [4,]   NA        NA        NA        NA 0.8725148 0.6929954 0.8709067
##  [5,]   NA        NA        NA        NA        NA 0.5949527 0.9797524
##  [6,]   NA        NA        NA        NA        NA        NA 0.5232985
##  [7,]   NA        NA        NA        NA        NA        NA        NA
##  [8,]   NA        NA        NA        NA        NA        NA        NA
##  [9,]   NA        NA        NA        NA        NA        NA        NA
## [10,]   NA        NA        NA        NA        NA        NA        NA
##             [,8]        [,9]      [,10]
##  [1,] 0.25391870 0.009793662 0.84547249
##  [2,] 0.24949617 0.008423378 0.84657268
##  [3,] 0.05180669 0.002175422 0.23506937
##  [4,] 0.23177905 0.005370706 0.83267982
##  [5,] 0.20789031 0.007920738 0.73294594
##  [6,] 0.31232018 0.002986949 0.92048503
##  [7,] 0.16735456 0.001374542 0.71529173
##  [8,]         NA 0.243604052 0.35673964
##  [9,]         NA          NA 0.02513735
## [10,]         NA          NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 8
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
## 0.001661367
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 1
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
