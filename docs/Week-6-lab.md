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
##       [,1]      [,2]      [,3]      [,4]       [,5]      [,6]      [,7]
##  [1,]   NA 0.1151726 0.1231267 0.2201888 0.95901449 0.7831837 0.4086019
##  [2,]   NA        NA 0.6887921 0.9079772 0.03692913 0.2300956 0.6011168
##  [3,]   NA        NA        NA 0.6674750 0.07774603 0.2062273 0.4612736
##  [4,]   NA        NA        NA        NA 0.14282538 0.3555079 0.7248719
##  [5,]   NA        NA        NA        NA         NA 0.7130761 0.3221282
##  [6,]   NA        NA        NA        NA         NA        NA 0.5855799
##  [7,]   NA        NA        NA        NA         NA        NA        NA
##  [8,]   NA        NA        NA        NA         NA        NA        NA
##  [9,]   NA        NA        NA        NA         NA        NA        NA
## [10,]   NA        NA        NA        NA         NA        NA        NA
##            [,8]      [,9]      [,10]
##  [1,] 0.6484029 0.3381491 0.82535353
##  [2,] 0.2495066 0.7041285 0.04252506
##  [3,] 0.2291745 0.5314425 0.06949441
##  [4,] 0.4020986 0.8194018 0.12674443
##  [5,] 0.5497807 0.2536333 0.83176955
##  [6,] 0.8772921 0.5015140 0.61170630
##  [7,] 0.6653052 0.9033006 0.27790177
##  [8,]        NA 0.5700036 0.46657202
##  [9,]        NA        NA 0.21992663
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
## 0.001860691
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
