Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to formally define a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

So now we know the general idea behind confidence intervals but we don't yet know how to calculate them. To do that, we'll actually walk through an example of bootstrap using pennies. Each of you should have gathered the ages of ten pennies. (If a penny was made in 2021, that penny would be 1 year old, etc.)

*Data*: 10 pennies that the students have

*Test statistic*: Median

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 10 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 10 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 10 times.)
2. Calculate the median age from that sample of pennies.
3. Repeat

Do this a few times with your actual physical pennies, and then once you get the idea, you can make a vector in R of your penny ages and use R to speed up the sampling. Don't forget to sample with replacement.

Gather a collection of 100 median estimates, each one calculated using a different bootstrapped dataset. Calculate the Bias and the Variance of the estimator for the Median.

We now want to take this one step further and estimate the confidence intervals for the median age of a penny in circulation. We actually have two primary mechanisms for generating confidence intervals for the statistic.

**Method #1**: Simply take the quantiles directly from the distribution of $\hat{\theta}^{*}$:

$$
\theta_{LL} = \mbox{2.5th percentile of } \hat{\theta}^{*}
$$
$$
\theta_{UL} = \mbox{97.5th percentile of } \hat{\theta}^{*}
$$

Notice that (by construction) 95$%$ of the $\hat{\theta}^{*}$ values fall in the interval $(\theta_{LL},\theta_{UL})$. <span style="color: orangered;">This is the very definition of the 95th percentile confidence interval.</span>

**OR** 

**Method #2**: We can use the Normal approximation:

We have a second method that won't make 100\% sense until next week, but it turns out that if we assume the bootstrapped estimates follow a Normal distribution, 

$$
\hat{\theta^{*}} \sim N(\hat{\theta},\hat{se}^{2})
$$

we can use the fact that the 95th percentile confidence interval is approximately given by:

$$
\hat{\theta}_{LL}=\hat{\theta}-1.96*\hat{se}
$$
$$
\hat{\theta}_{UL}=\hat{\theta}+1.96*\hat{se}
$$

It turns out that 95$\%$ of the probability for a Standard Normal distribution lies between (-1.96$\sigma$,+1.96$\sigma$). (We will show this more formally next week.) 

NB: If you are going to go through the trouble of doing the bootstrap sampling, I don’t know why you would make a Normal approximation at the very end to construct the CIs. I recommend Method #1.

**<span style="color: green;">Checkpoint #1: Use your penny data to calculate the 95th percentile confidence interval using Method #1 and Method #2. What did you get?</span>**

Testing hypotheses through permutation
------------------------------------

These examples use data on the speeds of the top 20 racing pigeons from a race in Alma, GA on February 7,2021. 

**Example #1**: Use permutation methods to test whether Cock or Hen birds fly at different speeds (speeds are in meters-per-minute) (in other word: $H_{0}$: No difference in speeds between the C and H groups):

C=$\{1359.8,1355.3,1355.1,1353.0,1349.8,1348.8,1345.2\}$

H=$\{1357.5,1356.4,1355.1,1353.5,1353.2,1352.5,1350.0,1349.8,1346.2,1344.9,1344.4,1343.9,1342.6\}$

**<span style="color: green;">Checkpoint #2: Is this a one-tailed or a two-tailed test?</span>**

Make sure that you understand what is being done here, as this example is very closely related to the problem set.


**Example #2**: Using the same data, provide a 95% confidence interval for the difference in mean speed based on 1000 bootstrap samples

Note that these two approaches are very closely related. Do you see why either approach can be used to test the null hypothesis? **<span style="color: green;">Checkpoint #3: What is the null hypothesis here?</span>**

**Example #3**: Now we will do one slightly more complicated example from Phillip Good's book "Permutation tests: A practical guide to resampling methods and testing hypotheses":

Holmes and Williams (1954) studied tonsil size in children to verify a possible association with the virus \textit{S. pyrogenes}. Test for an association between \textit{S. pyrogenes} status and tonsil size. (Note that you will need to come up with a reasonable test statistic.)

<div class="figure" style="text-align: center">
<img src="Table2categories.png" alt="Data on tonsil size and S. pyrogenes status. Source: Good (1994)" width="40%" />
<p class="caption">(\#fig:unnamed-chunk-1)Data on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Now lets consider the full dataset, where tonsil size is divided into three categories. How would we do the test now? **<span style="color: green;">Checkpoint #4: What is the new test statistic? (There are many options.)</span>** What 'labels' do you permute?

<div class="figure" style="text-align: center">
<img src="Table3categories.png" alt="Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)" width="50%" />
<p class="caption">(\#fig:unnamed-chunk-2)Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Basics of bootstrap and jackknife
------------------------------------

To get started with bootstrap and jackknife techniques, we start by working through a very simple example. First we simulate some data


```r
x<-seq(0,9,by=1)
```

This will constutute our "data". Let's print the result of sampling with replacement to get a sense for it...


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 5 6 7 8 9 
## 1 1 1 1 1 1 1 1 2
```

Now we will write a little script to take bootstrap samples and calculate the means of each of these bootstrap samples


```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
```

The actual number of bootstrapped samples is arbitrary *at this point* but there are ways of characterizing the precision of the bootstrap (jackknife-after-bootstrap) which might inform the number of bootstrap samples needed. *In practice*, people tend to pick some arbitrary but large number of bootstrap samples because computers are so fast that it is often easy to draw far more samples than are actually needed. When calculation of the statistic is slow (as might be the case if you are using the samples to construct a phylogeny, for example), then you would need to be more concerned with the number of bootstrap samples. 

First, lets just look at a histogram of the bootstrapped means and plot the actual sample mean on the histogram for comparison



```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Calculating bias and standard error
-----------------------------------

From these we can calculate the bias and standard deviation for the mean (which is the "statistic"):

$$
\widehat{Bias_{boot}} = \left(\frac{1}{k}\sum^{k}_{i=1}\theta^{*}_{i}\right)-\hat{\theta}
$$


```r
bias.boot<-mean(xmeans)-mean(x)
bias.boot
```

```
## [1] -0.0476
```

```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=5,col="black")
abline(v=mean(xmeans),lwd=2,col="yellow")
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-7-1.png" width="672" />

$$
\widehat{s.e._{boot}} = \sqrt{\frac{1}{k-1}\sum^{k}_{i=1}(\theta^{*}_{i}-\bar{\theta^{*}})^{2}}
$$


```r
se.boot<-sd(xmeans)
```

We can find the confidence intervals in two ways:

Method #1: Assume the bootstrap statistics are normally distributed


```r
LL.boot<-mean(xmeans)-1.96*se.boot #where did 1.96 come from?
UL.boot<-mean(xmeans)+1.96*se.boot
LL.boot
```

```
## [1] 2.61138
```

```r
UL.boot
```

```
## [1] 6.29342
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.4
```

Let's compare this to what we would have gotten if we had used normal distribution theory. First we have to calculate the standard error:


```r
se.normal<-sqrt(var(x)/length(x))
LL.normal<-mean(x)-qt(0.975,length(x)-1)*se.normal
UL.normal<-mean(x)+qt(0.975,length(x)-1)*se.normal
LL.normal
```

```
## [1] 2.334149
```

```r
UL.normal
```

```
## [1] 6.665851
```

In this case, the confidence intervals we got from the normal distribution theory are too wide.

**<span style="color: green;">Checkpoint #6: Does it make sense why the normal distribution theory intervals are too wide?</span>** Because the original were were uniformly distributed, the data has higher variance than would be expected and therefore the standard error is higher than would be expected.

There are two packages that provide functions for bootstrapping, 'boot' and 'boostrap'. We will start by using the 'bootstrap' package, which was originally designed for Efron and Tibshirani's monograph on the bootstrap. 

To test the main functionality of the 'bootstrap' package, we will use the data we already have. The 'bootstrap' function requires the input of a user-defined function to calculate the statistic of interest. Here I will write a function that calculates the mean of the input values.


```r
library(bootstrap)
theta<-function(x)
  {
    mean(x)
  }
results<-bootstrap(x=x,nboot=1000,theta=theta)
results
```

```
## $thetastar
##    [1] 4.5 4.6 4.4 5.5 5.3 6.2 5.6 5.0 4.1 4.2 4.2 5.3 4.2 4.4 4.5 5.0 4.4 5.5
##   [19] 4.6 2.9 3.5 4.2 5.3 3.8 5.3 6.1 4.2 2.8 3.2 5.0 4.9 3.7 2.6 6.0 3.1 6.2
##   [37] 3.1 5.2 4.2 4.1 5.0 4.1 3.8 4.3 5.7 3.4 4.0 4.4 3.5 3.6 3.0 4.1 5.3 4.7
##   [55] 5.3 2.9 2.3 5.8 3.5 4.5 4.7 5.1 5.9 4.5 5.0 3.8 5.3 5.3 3.0 1.9 3.4 3.0
##   [73] 5.4 4.7 4.9 3.9 2.9 4.8 3.3 4.4 3.9 6.3 5.4 6.1 5.5 4.8 3.9 4.4 4.7 5.2
##   [91] 4.9 5.3 4.4 4.4 4.6 5.2 5.3 4.2 4.4 3.8 5.8 3.9 4.5 3.4 4.0 5.1 3.2 4.8
##  [109] 3.6 4.1 2.5 4.5 3.9 5.0 4.2 4.4 5.7 4.8 5.4 5.3 4.9 5.6 2.2 4.4 3.1 3.6
##  [127] 4.0 2.7 4.7 4.1 4.3 3.8 2.8 4.5 4.3 5.8 5.9 4.3 4.8 5.6 4.8 6.5 4.0 3.0
##  [145] 2.9 4.9 4.0 3.1 4.7 5.2 4.0 4.6 4.5 4.5 3.2 6.2 2.5 4.1 4.9 2.7 6.6 4.7
##  [163] 5.3 6.2 5.3 3.4 5.2 4.1 4.8 4.9 5.2 5.5 3.7 4.1 4.5 4.0 5.2 3.8 4.1 5.2
##  [181] 4.9 3.7 4.5 3.8 5.3 4.7 4.0 5.3 4.2 4.0 4.9 3.5 4.9 4.6 5.5 6.4 3.5 3.3
##  [199] 4.0 4.7 3.4 5.0 5.3 2.7 3.1 4.6 4.6 5.4 3.9 4.6 4.9 5.0 4.0 5.5 3.1 3.8
##  [217] 5.6 5.3 5.5 4.6 3.6 3.8 4.9 4.6 2.9 3.5 3.7 4.9 5.0 5.3 3.4 4.6 4.6 4.6
##  [235] 5.4 3.9 4.2 5.5 4.8 4.8 5.1 3.3 4.0 3.3 6.4 2.9 4.1 4.7 5.1 5.6 5.5 3.0
##  [253] 2.9 4.8 5.8 4.7 3.3 4.5 2.8 4.3 5.2 5.4 4.5 5.2 5.2 5.9 3.6 5.3 3.0 5.0
##  [271] 4.9 3.9 3.5 4.4 5.4 4.6 3.7 4.8 3.5 5.0 4.1 3.0 4.1 4.0 4.0 5.6 5.3 2.6
##  [289] 4.2 5.1 3.6 3.4 3.2 4.6 4.2 4.0 4.0 7.0 4.6 4.9 4.9 6.3 5.3 4.8 5.2 5.0
##  [307] 2.2 4.4 4.5 3.9 4.9 3.4 5.5 4.4 5.7 2.5 5.5 4.6 3.0 5.6 5.1 4.4 3.5 4.9
##  [325] 6.3 4.0 3.1 5.3 4.6 5.3 6.4 4.3 5.1 3.0 4.0 4.7 2.4 3.3 3.4 4.3 4.4 6.5
##  [343] 4.6 5.1 4.0 2.2 4.2 3.9 4.4 5.2 5.2 4.7 3.5 4.2 3.8 3.0 3.3 4.0 4.4 5.9
##  [361] 2.5 3.6 4.3 5.3 3.2 5.3 5.0 6.2 3.3 4.1 3.6 4.3 4.6 4.4 3.9 4.6 5.5 4.9
##  [379] 4.2 5.6 4.0 4.1 5.8 3.7 5.5 5.4 4.5 5.9 3.3 3.7 3.2 5.3 4.8 4.5 4.8 4.7
##  [397] 4.8 6.2 5.3 6.1 4.3 3.6 3.8 4.3 5.6 4.4 3.5 4.2 2.2 4.4 6.1 3.6 4.5 5.3
##  [415] 5.4 4.1 5.4 4.9 4.4 5.2 4.7 4.3 4.2 5.5 3.6 4.4 5.1 5.0 4.4 4.7 4.1 6.4
##  [433] 3.1 4.6 4.1 4.6 2.5 4.0 4.0 6.5 4.5 3.0 5.8 4.7 4.9 3.8 4.8 4.0 5.3 4.3
##  [451] 2.9 6.3 6.9 3.1 5.1 5.4 4.0 5.3 4.5 3.6 4.8 5.6 4.9 4.5 4.8 4.7 4.0 3.7
##  [469] 4.5 4.4 3.2 5.1 3.3 4.6 4.9 3.5 4.9 3.5 4.7 3.2 3.3 5.1 5.6 4.6 3.3 4.9
##  [487] 5.2 4.9 5.2 3.4 5.0 2.4 5.8 5.2 4.7 3.5 3.6 4.5 3.5 2.9 3.9 3.9 4.6 5.0
##  [505] 4.5 4.1 3.2 4.7 4.1 6.1 3.6 4.2 4.4 3.3 5.9 4.8 5.0 3.3 4.7 4.9 3.3 5.8
##  [523] 4.8 4.3 4.0 5.4 5.3 4.4 6.5 5.5 5.0 4.7 2.7 4.7 4.0 3.5 5.3 4.3 3.4 3.0
##  [541] 4.5 3.1 2.6 4.1 4.6 4.7 3.4 2.7 3.0 6.1 5.5 4.9 5.3 5.7 5.8 4.5 3.4 4.3
##  [559] 5.7 4.1 5.6 4.4 2.7 5.0 4.1 3.8 4.5 3.8 3.6 5.1 4.2 4.0 4.7 3.2 5.2 4.2
##  [577] 3.6 1.3 4.5 4.9 5.6 4.6 4.0 2.7 4.1 5.7 5.8 5.1 5.0 6.2 5.5 3.5 3.5 3.6
##  [595] 4.9 5.9 4.0 6.4 3.6 5.0 4.1 4.2 3.8 1.6 5.5 5.1 5.3 2.6 4.1 6.1 4.6 4.3
##  [613] 4.7 5.1 5.0 4.4 5.7 4.8 5.1 4.1 4.1 3.2 4.1 4.1 4.9 3.7 5.7 3.2 4.4 3.2
##  [631] 3.9 5.9 5.3 4.4 3.6 5.0 3.8 4.6 3.6 2.4 4.0 3.8 2.8 3.9 4.0 4.5 4.2 4.5
##  [649] 4.5 5.4 5.1 5.4 4.6 4.0 3.8 3.0 3.5 4.6 4.5 3.9 5.0 4.2 2.8 6.1 5.1 5.7
##  [667] 5.1 6.5 4.6 3.7 3.7 4.5 4.5 4.4 5.5 5.0 5.6 5.9 3.8 4.0 5.6 5.4 4.6 4.2
##  [685] 5.2 5.7 3.3 5.3 5.5 5.2 3.9 3.0 4.1 4.5 5.3 4.5 4.6 3.0 3.7 3.6 3.7 4.5
##  [703] 5.4 4.4 4.5 5.4 4.4 4.3 4.7 3.8 3.0 5.2 3.6 3.6 6.7 4.6 4.6 5.0 3.8 4.6
##  [721] 4.2 4.8 5.0 4.9 5.9 3.1 3.8 5.4 4.6 5.2 5.1 4.4 4.0 3.7 4.0 3.7 4.2 5.0
##  [739] 5.7 3.4 3.0 3.5 5.4 4.3 5.6 3.2 4.5 4.7 5.2 4.8 5.6 3.9 5.4 5.5 5.6 6.2
##  [757] 4.7 5.5 6.1 4.0 3.1 3.8 5.2 4.3 5.0 5.8 3.2 5.5 4.5 3.6 4.7 4.4 5.8 5.9
##  [775] 5.3 3.6 3.4 4.8 3.0 4.6 5.6 5.2 3.5 4.3 5.1 4.0 4.0 5.9 5.1 6.0 5.7 4.7
##  [793] 3.1 4.9 4.7 4.1 3.4 4.8 3.5 3.1 6.1 3.9 3.4 4.4 5.2 3.8 4.4 4.0 5.0 4.0
##  [811] 4.6 5.0 5.7 5.5 3.1 4.5 5.5 4.8 4.4 5.3 4.7 4.0 4.8 2.8 4.9 4.0 4.5 3.1
##  [829] 3.2 3.7 5.9 4.4 3.6 5.5 5.8 4.9 5.6 4.6 5.2 3.7 4.6 5.3 6.1 4.4 4.1 4.8
##  [847] 5.0 4.7 5.2 4.6 2.2 4.8 3.9 3.3 5.1 3.4 6.0 4.8 4.8 3.9 5.8 5.9 4.1 4.6
##  [865] 3.7 4.1 4.4 4.0 4.0 4.6 4.3 3.3 3.9 4.1 3.2 3.3 5.1 3.4 5.5 5.2 4.4 4.7
##  [883] 5.0 3.9 3.8 4.3 5.8 3.6 4.5 3.6 6.3 5.0 4.4 3.2 2.7 5.3 3.9 4.9 6.1 2.9
##  [901] 5.4 5.1 3.3 3.5 4.1 5.0 5.2 5.6 3.5 5.4 4.9 3.1 5.5 4.1 4.4 6.0 3.2 3.6
##  [919] 3.6 3.2 4.6 3.6 3.1 4.7 4.3 4.3 4.0 3.5 4.9 4.1 5.0 6.1 2.7 4.1 4.8 5.1
##  [937] 4.2 3.0 5.7 5.8 3.7 3.1 4.6 4.7 4.7 5.1 4.7 4.2 6.1 5.3 4.8 5.9 5.6 3.5
##  [955] 3.3 5.0 4.7 4.6 3.2 4.5 5.3 4.7 5.5 3.9 5.1 5.4 6.3 5.2 4.0 3.7 5.6 4.2
##  [973] 6.0 5.7 3.8 5.0 5.6 4.9 4.1 3.4 5.1 4.9 3.8 4.6 4.3 5.3 5.7 4.3 7.7 3.9
##  [991] 4.0 5.8 4.1 4.3 4.1 4.6 6.1 4.1 4.7 3.6
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta)
```

```r
quantile(results$thetastar,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.2
```

Notice that we get exactly what we got last time. This illustrates an important point, which is that the bootstrap functions are often no easier to use than something you could write yourself.

You can also define a function of the bootstrapped statistics (we have been calling this theta) to pull out immediately any summary statistics you are interested in from the bootstrapped thetas.

Here I will write a function that calculates the bias of my estimate of the mean (which is 4.5 [i.e. the mean of the number 0,1,2,3,4,5,6,7,8,9])


```r
bias<-function(x)
  {
  mean(x)-4.5
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=bias)
results
```

```
## $thetastar
##    [1] 4.1 4.8 3.8 4.8 3.4 2.8 4.5 4.7 4.4 4.2 4.8 5.3 6.3 4.8 4.2 4.8 4.3 4.1
##   [19] 6.8 5.4 4.1 5.4 3.1 5.8 4.5 4.0 5.2 5.4 4.5 5.8 2.8 3.9 5.9 3.4 4.0 5.6
##   [37] 5.3 5.9 3.9 4.6 3.9 4.4 4.3 3.4 3.0 4.7 5.3 4.8 4.1 5.2 3.8 4.8 4.1 4.2
##   [55] 2.8 3.8 4.4 4.7 4.7 4.4 3.3 3.4 5.9 7.4 4.6 4.9 5.1 4.8 5.4 3.3 3.1 4.4
##   [73] 3.8 6.7 5.9 5.3 3.6 3.6 4.1 4.9 4.4 3.7 5.2 4.8 5.2 3.4 3.4 5.6 4.6 3.5
##   [91] 2.5 4.0 4.2 4.0 4.1 5.0 5.8 5.8 5.0 3.7 4.3 4.0 6.2 4.0 4.3 4.4 3.0 5.6
##  [109] 4.2 4.0 3.0 4.5 3.7 4.2 4.2 3.5 5.0 4.9 5.5 4.3 4.9 4.8 5.9 6.1 2.8 4.6
##  [127] 5.6 3.0 4.6 5.6 4.3 5.0 4.1 4.2 5.2 5.7 2.3 4.5 5.0 5.8 4.1 3.8 3.1 3.4
##  [145] 5.0 5.9 4.1 4.7 4.1 4.9 5.5 4.7 3.6 5.3 3.9 4.3 3.9 4.9 6.5 3.4 4.5 4.7
##  [163] 6.1 4.4 4.6 3.9 5.2 3.3 4.4 3.8 3.3 5.0 4.0 4.5 5.1 3.8 5.7 5.1 4.4 5.4
##  [181] 4.6 4.0 5.3 5.3 5.2 4.8 4.8 4.9 4.1 4.8 5.0 5.2 4.7 5.2 4.3 2.7 4.9 3.2
##  [199] 5.1 5.3 6.1 4.6 5.1 4.1 3.5 5.1 5.4 5.1 5.3 3.8 5.3 2.4 5.2 4.3 3.2 3.4
##  [217] 4.6 5.4 4.1 5.1 4.5 3.1 3.6 4.2 4.9 5.1 4.1 3.0 4.4 4.6 4.0 5.1 2.9 5.7
##  [235] 5.4 4.0 4.0 6.1 3.1 5.4 4.7 6.1 4.0 4.8 4.2 3.9 3.4 4.1 5.4 6.6 4.7 3.9
##  [253] 4.0 6.3 4.5 3.4 4.0 3.9 5.2 4.9 2.3 5.3 6.7 2.6 6.2 4.3 3.5 4.9 3.3 5.1
##  [271] 6.8 3.6 3.4 5.2 5.0 4.4 5.0 4.2 3.6 3.0 4.2 2.7 3.6 4.0 3.7 4.9 4.4 4.2
##  [289] 4.3 4.3 4.6 5.6 6.1 3.4 5.4 3.8 4.7 4.4 3.6 4.5 6.1 5.5 4.7 3.2 3.7 3.8
##  [307] 4.3 3.0 4.9 3.9 4.9 3.5 6.2 3.5 4.6 4.7 6.0 5.5 4.5 4.5 5.3 3.0 5.6 3.9
##  [325] 5.4 5.1 5.1 4.2 4.7 6.4 4.6 5.0 4.4 3.9 4.5 4.3 4.9 4.2 3.8 3.1 4.5 5.1
##  [343] 3.5 4.2 4.0 5.1 4.8 4.8 5.6 6.6 3.6 5.1 4.3 4.6 6.3 5.9 3.1 3.8 4.8 5.6
##  [361] 4.2 4.3 3.5 4.7 4.6 4.8 4.7 4.9 5.6 4.8 4.2 4.0 4.8 3.4 3.7 4.7 4.8 4.2
##  [379] 5.7 3.9 5.2 5.3 5.2 4.5 3.4 3.4 4.9 4.7 3.9 5.0 4.0 2.3 4.0 4.4 4.8 4.4
##  [397] 4.7 6.0 4.5 4.6 4.1 5.9 5.3 3.0 5.3 5.7 4.9 4.3 5.0 3.8 5.0 6.4 3.7 3.8
##  [415] 3.8 4.7 3.7 6.6 5.8 4.9 4.0 4.0 2.6 4.5 5.3 5.5 5.2 4.6 4.7 4.7 4.7 3.3
##  [433] 3.2 4.4 4.1 3.6 3.1 4.4 4.0 4.5 3.4 4.9 3.0 4.8 4.5 3.8 5.8 5.2 4.7 2.4
##  [451] 3.4 3.2 3.9 3.3 4.0 6.2 2.7 6.3 3.3 5.5 4.1 3.6 4.2 5.1 4.8 4.3 4.9 3.9
##  [469] 5.2 4.6 4.6 2.7 4.6 3.6 5.6 6.1 4.3 3.7 5.6 3.6 4.0 5.2 4.4 2.9 5.0 4.8
##  [487] 3.6 6.5 5.1 6.2 5.2 6.8 3.9 4.1 5.1 4.7 4.8 4.1 6.3 3.9 4.6 4.7 4.3 5.7
##  [505] 3.7 5.1 5.7 4.9 5.2 5.0 6.1 3.7 4.3 5.6 2.7 2.7 4.1 4.4 4.3 3.7 4.2 3.0
##  [523] 5.9 3.8 5.0 3.6 3.6 3.8 5.0 3.8 3.9 4.3 3.9 4.9 4.7 5.0 4.4 4.1 4.1 4.9
##  [541] 4.4 3.8 6.0 4.8 4.4 7.3 4.6 5.3 3.8 5.0 6.2 5.0 4.1 5.3 3.5 5.1 4.6 5.1
##  [559] 4.3 4.5 3.9 3.6 4.8 4.2 5.8 4.5 3.9 2.4 5.1 3.6 3.6 3.2 6.2 4.2 4.9 3.8
##  [577] 3.5 6.0 4.3 3.8 5.8 5.5 3.6 5.8 5.0 5.2 5.6 2.6 1.7 5.5 5.1 4.4 5.4 4.7
##  [595] 3.7 3.7 4.3 3.7 4.4 4.6 5.0 5.5 4.6 4.6 4.5 3.1 5.1 6.1 5.1 5.5 4.3 4.7
##  [613] 5.1 4.6 3.8 4.5 5.9 3.7 4.6 4.6 6.3 5.7 3.2 4.3 6.4 3.9 5.3 3.4 6.2 5.7
##  [631] 5.4 5.1 4.4 6.3 4.1 5.1 4.2 4.6 4.3 3.5 2.7 5.4 5.3 4.1 2.8 3.3 4.1 3.8
##  [649] 4.2 4.4 3.8 3.4 5.7 5.8 4.7 4.4 5.8 4.1 6.0 5.1 5.7 5.4 4.0 4.1 4.3 3.6
##  [667] 4.2 5.7 5.1 4.7 3.9 4.4 3.3 3.2 4.8 4.6 4.2 6.0 3.6 5.3 3.2 4.6 4.3 4.1
##  [685] 3.8 4.4 4.9 4.4 6.2 5.0 6.7 5.1 5.9 5.1 4.1 5.1 4.2 4.5 3.9 5.3 4.4 2.7
##  [703] 4.8 5.4 5.0 4.1 5.1 4.5 5.0 5.7 5.7 4.1 4.8 4.8 5.0 3.5 3.0 6.1 5.3 3.6
##  [721] 6.0 4.3 4.5 4.2 4.8 5.4 4.9 4.3 4.4 5.4 4.5 6.0 5.8 4.3 3.0 3.9 5.2 3.8
##  [739] 5.1 4.1 5.2 4.7 6.5 4.2 4.2 3.5 5.5 5.7 3.5 5.7 3.4 3.0 5.4 4.1 6.0 3.8
##  [757] 4.9 4.4 5.1 4.5 5.6 3.9 6.1 4.6 5.2 4.0 2.9 5.6 3.9 4.1 4.4 4.2 5.7 3.9
##  [775] 6.3 4.3 5.6 4.7 4.7 3.7 5.1 6.0 5.6 4.9 4.5 5.4 5.3 4.2 4.5 2.4 5.7 4.6
##  [793] 4.5 3.3 4.3 3.8 4.2 6.5 5.0 3.1 4.9 3.2 3.7 5.1 4.8 4.9 4.9 3.5 5.3 4.0
##  [811] 5.1 4.8 6.0 4.4 3.6 3.7 4.0 4.7 5.4 5.3 4.7 5.2 5.2 4.5 4.2 4.0 3.5 4.9
##  [829] 5.5 3.8 5.5 4.8 5.1 3.3 4.7 4.5 4.6 4.5 4.1 5.0 6.6 5.3 4.0 4.6 2.6 5.4
##  [847] 5.1 2.8 4.5 4.7 4.6 5.1 5.1 5.0 6.3 4.3 5.4 3.6 6.0 3.5 6.0 3.7 4.2 4.0
##  [865] 5.2 5.7 4.8 3.8 4.0 4.3 5.7 5.8 3.2 5.0 4.2 4.6 3.8 4.2 3.6 3.9 4.6 4.4
##  [883] 6.4 4.2 3.1 4.8 4.3 4.1 3.4 4.2 5.6 4.2 4.9 5.1 3.4 5.4 4.5 5.3 2.6 4.2
##  [901] 5.3 4.7 5.2 3.6 3.8 5.9 4.1 5.1 4.4 3.5 5.0 5.1 4.7 2.8 5.1 2.8 6.4 5.8
##  [919] 4.0 3.8 3.9 3.6 3.3 4.5 5.5 4.7 4.2 6.4 3.4 5.4 2.5 4.7 5.0 5.1 4.2 4.7
##  [937] 5.6 5.0 4.8 4.7 3.5 3.6 6.2 3.4 4.7 4.2 3.7 5.7 3.0 4.4 4.7 3.2 5.5 2.4
##  [955] 3.2 5.1 5.2 5.6 4.5 4.1 5.2 4.4 4.4 5.4 3.7 4.1 4.5 5.2 5.0 4.3 4.4 3.1
##  [973] 4.4 6.4 5.2 3.8 2.3 4.2 4.5 4.1 4.0 4.8 4.4 4.4 4.3 2.8 4.1 5.2 5.3 5.7
##  [991] 4.2 4.0 5.5 2.9 5.2 3.0 4.7 4.8 5.2 3.0
## 
## $func.thetastar
## [1] 0.0391
## 
## $jack.boot.val
##  [1]  0.45329815  0.43167155  0.37348066  0.23722826  0.10030581  0.03060606
##  [7] -0.12634731 -0.26862170 -0.30635838 -0.45013928
## 
## $jack.boot.se
## [1] 0.9298115
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = bias)
```

Compare this to 'bias.boot' (our result from above). Why might it not be the same? Try running the same section of code several times. See how the value of the bias ($func.thetastar) jumps around? We should not be surprised by this because we can look at the jackknife-after-bootstrap estimate of the standard error of the function (in this case, that function is the bias) and we can see that it is not so small that we wouldn't expect some variation in these values.

Remember, everything we have discussed today are estimates. The statistic as applied to your data will change with new data, as will the standard error, the confidence intervals - everything! All of these values have sampling distributions and are subject to change if you repeated the procedure with new data.

Note that we can calculate any function of $\theta^{*}$. A simple example would be the 72nd percentile:


```r
perc72<-function(x)
  {
  quantile(x,probs=c(0.72))
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=perc72)
results
```

```
## $thetastar
##    [1] 3.5 5.1 3.8 6.8 5.4 4.5 4.1 5.9 3.2 5.2 5.5 4.7 5.2 5.5 3.6 5.9 4.1 3.1
##   [19] 4.5 4.0 3.8 4.6 4.8 3.3 4.8 4.6 3.7 5.3 4.6 4.1 4.1 5.3 5.0 5.4 5.3 5.3
##   [37] 4.2 5.1 3.3 5.9 3.4 4.1 5.4 3.7 4.1 5.0 3.8 2.8 3.6 4.4 4.8 3.7 4.8 5.2
##   [55] 3.6 4.5 4.1 4.2 4.4 4.7 5.1 4.6 5.2 4.7 6.6 5.2 5.6 4.4 4.9 4.8 5.0 5.2
##   [73] 3.6 4.8 3.2 6.2 3.3 4.2 5.8 4.6 5.7 4.3 4.0 4.0 4.9 5.6 4.0 4.9 4.1 4.2
##   [91] 3.8 4.1 3.5 3.6 4.8 4.4 5.0 3.6 1.9 4.9 3.8 4.4 5.5 4.2 4.5 3.7 4.5 6.1
##  [109] 3.8 3.8 2.7 5.9 3.1 4.6 4.4 4.7 4.9 2.7 3.8 5.0 5.4 4.5 5.2 4.7 3.2 4.8
##  [127] 3.9 5.9 4.2 4.1 5.2 4.4 5.0 4.2 4.0 4.5 4.3 4.2 3.4 3.7 4.3 4.5 4.7 4.4
##  [145] 3.8 5.7 3.4 4.8 4.2 5.4 5.5 3.9 4.7 4.8 3.7 6.1 4.7 4.0 3.7 6.2 4.6 5.1
##  [163] 3.8 4.9 2.7 5.1 3.4 4.3 4.3 4.0 5.6 4.0 3.8 4.7 4.4 5.1 4.2 4.1 4.0 4.4
##  [181] 2.7 3.9 5.3 6.2 6.8 4.8 4.2 4.8 4.3 3.3 3.9 5.9 5.0 3.7 5.0 4.6 3.8 3.8
##  [199] 5.6 5.8 5.2 5.5 4.0 5.4 4.2 4.2 4.6 5.5 4.6 4.7 4.0 4.1 5.4 4.7 5.3 3.7
##  [217] 5.7 3.9 4.7 4.7 5.0 2.7 4.0 5.9 4.8 3.8 4.4 4.5 4.9 2.9 4.8 4.2 5.5 4.2
##  [235] 5.3 4.5 3.6 3.7 3.5 4.5 5.7 4.5 5.3 5.1 3.5 5.3 4.7 4.3 5.0 6.1 5.7 3.7
##  [253] 3.8 4.9 4.0 3.8 3.6 4.0 3.7 4.0 4.2 5.2 4.4 3.4 5.5 5.5 4.4 6.4 3.3 4.3
##  [271] 3.6 4.8 3.6 5.1 3.8 3.4 5.1 4.4 5.2 4.9 4.9 2.8 4.3 4.5 2.4 3.9 4.2 5.3
##  [289] 3.3 4.3 3.9 5.3 4.6 3.8 2.6 5.3 4.8 3.9 4.6 3.2 3.4 4.5 3.7 3.7 3.8 3.0
##  [307] 4.4 3.9 3.3 3.6 5.8 3.3 3.9 4.0 5.1 5.5 4.6 6.1 2.5 3.7 4.1 4.7 4.6 2.9
##  [325] 3.7 4.1 4.8 3.7 5.5 2.5 3.8 6.3 4.2 5.5 3.8 3.4 5.3 6.3 2.6 3.6 4.0 2.9
##  [343] 5.3 3.6 3.2 5.5 5.3 5.4 5.4 3.7 5.8 3.1 5.9 2.4 4.3 5.5 5.1 5.3 4.2 4.8
##  [361] 3.3 6.5 4.0 5.1 6.7 4.8 5.2 3.6 4.2 3.1 4.2 4.5 4.2 5.0 4.7 7.1 2.1 5.0
##  [379] 5.2 2.8 4.0 4.8 5.3 4.1 4.5 4.3 4.2 3.9 4.8 4.5 4.0 5.1 5.3 3.7 3.9 5.0
##  [397] 4.6 4.7 4.6 5.1 3.2 5.3 3.0 4.7 3.1 4.7 3.1 3.8 4.1 5.9 4.2 5.0 4.2 6.2
##  [415] 3.3 3.5 3.2 3.9 3.7 4.5 4.9 2.6 5.9 4.3 4.9 4.9 4.8 3.2 5.5 3.1 3.6 4.4
##  [433] 4.4 4.5 5.1 5.2 5.2 4.5 5.1 3.9 3.4 3.7 5.7 5.3 3.3 4.1 3.7 4.7 4.7 2.8
##  [451] 3.7 4.3 5.4 4.9 6.1 5.2 2.7 2.9 3.1 3.7 5.2 2.9 4.1 4.3 3.1 3.9 4.8 5.4
##  [469] 3.4 5.1 4.1 7.0 2.9 6.0 5.9 5.2 6.2 4.2 6.1 5.2 4.2 4.7 5.4 5.4 5.0 3.7
##  [487] 4.1 4.4 3.4 5.0 3.7 4.0 2.4 3.6 4.7 3.4 5.3 4.9 4.2 5.9 4.8 3.5 4.4 3.6
##  [505] 3.3 4.2 4.3 4.7 4.0 3.9 3.6 6.5 3.9 5.6 5.0 4.3 4.2 5.0 3.6 3.3 4.7 4.4
##  [523] 4.0 4.4 5.4 5.0 4.3 4.5 4.6 6.0 4.2 5.8 4.7 5.0 4.4 4.4 5.1 4.0 4.3 4.7
##  [541] 6.4 3.6 4.2 4.0 3.1 3.6 6.0 3.4 4.0 3.3 4.1 5.1 3.8 5.6 5.2 3.1 6.3 4.5
##  [559] 6.6 4.8 4.2 3.8 3.6 5.3 4.7 2.4 4.1 4.4 3.3 4.6 6.0 4.7 5.0 5.5 4.5 5.4
##  [577] 4.1 4.1 5.1 4.6 5.2 4.5 5.5 4.0 4.2 5.1 3.7 5.0 4.6 4.1 3.9 6.3 3.8 3.5
##  [595] 3.9 4.4 4.6 4.2 4.6 4.0 4.3 4.1 4.7 3.1 5.4 4.5 5.4 4.0 6.2 2.6 4.5 4.6
##  [613] 4.7 3.6 4.1 4.6 4.6 4.4 4.1 3.6 5.2 3.4 5.0 4.6 3.4 4.4 2.5 3.6 3.5 3.4
##  [631] 5.4 4.3 5.3 4.6 5.4 5.4 4.8 5.3 6.5 3.3 7.3 4.7 4.7 6.4 6.0 2.9 4.7 3.2
##  [649] 5.0 3.1 4.7 4.8 6.3 6.6 5.2 2.8 5.0 4.6 5.2 4.8 5.6 5.2 6.7 4.9 5.0 4.3
##  [667] 4.6 4.1 4.6 7.1 4.5 3.5 4.9 5.8 4.8 3.8 3.6 4.7 4.8 3.9 5.5 4.2 5.4 5.0
##  [685] 4.3 3.7 4.9 4.0 5.0 3.3 2.3 5.6 3.9 3.3 3.7 2.8 4.8 4.3 3.6 4.1 4.9 4.3
##  [703] 4.4 4.3 4.8 4.5 4.5 2.5 4.3 2.3 4.3 4.7 3.7 5.8 3.6 5.1 5.9 4.2 3.4 5.5
##  [721] 4.9 3.4 5.0 4.1 3.2 4.3 3.7 4.7 3.6 4.7 4.6 5.2 3.5 4.4 4.7 6.5 3.4 5.8
##  [739] 5.6 4.9 4.9 3.1 4.7 6.4 4.2 3.9 4.6 5.0 3.7 4.2 4.4 5.5 4.2 4.5 3.0 3.8
##  [757] 4.6 5.4 4.7 3.3 4.3 5.1 4.4 5.6 5.9 6.1 5.0 3.1 4.2 3.7 4.2 5.0 3.9 5.0
##  [775] 3.7 3.7 4.7 5.5 4.7 4.7 5.3 7.0 3.8 4.0 4.5 6.4 3.8 4.0 6.3 4.6 2.6 3.4
##  [793] 3.2 4.1 6.1 3.7 4.6 4.5 2.7 3.6 6.4 3.7 6.0 4.9 4.6 3.7 5.7 3.7 5.2 4.8
##  [811] 3.6 4.0 4.1 3.3 4.9 4.5 6.5 3.3 3.8 3.6 3.3 3.4 4.5 2.8 5.9 3.4 3.1 4.0
##  [829] 4.4 5.4 6.1 5.0 4.6 6.2 7.2 5.2 5.1 5.2 4.7 4.7 4.4 6.1 3.7 4.9 5.4 3.7
##  [847] 4.4 5.4 4.2 4.9 3.7 4.0 4.5 4.3 7.2 5.5 4.6 3.2 4.2 3.0 5.0 4.9 3.9 3.9
##  [865] 4.9 5.5 4.3 5.6 3.3 3.8 5.3 4.8 3.4 4.6 4.0 4.9 4.5 6.4 4.8 4.6 5.2 4.1
##  [883] 4.6 3.3 6.2 4.5 3.9 3.5 4.7 4.4 5.8 4.9 6.9 5.8 4.1 4.0 5.1 5.6 4.2 5.4
##  [901] 6.0 2.7 4.9 5.5 2.8 3.3 3.8 4.5 5.8 4.1 4.0 3.5 6.2 5.1 3.9 3.4 5.3 4.6
##  [919] 4.7 4.6 4.3 3.4 4.1 4.6 5.5 4.2 4.8 3.7 5.3 3.9 5.4 3.9 4.8 3.0 4.6 3.8
##  [937] 5.1 4.7 4.0 4.8 5.6 4.1 5.3 4.2 4.9 5.3 4.0 4.6 4.3 5.3 3.6 4.3 5.6 3.9
##  [955] 5.6 4.0 4.6 5.2 4.3 4.3 4.4 6.3 3.3 4.6 5.0 4.5 6.3 3.7 4.9 4.9 3.6 5.0
##  [973] 2.7 4.7 4.0 3.8 3.9 4.6 3.8 4.7 4.7 3.5 4.5 3.8 4.1 6.0 5.1 5.5 3.6 4.3
##  [991] 4.8 3.1 4.4 4.5 5.1 4.8 3.6 3.4 4.9 4.8
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.3 5.2 5.2 5.2 5.0 4.8 4.7 4.5 4.5
## 
## $jack.boot.se
## [1] 0.99
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = perc72)
```

On Tuesday we went over an example in which we bootstrapped the correlation coefficient between LSAT scores and GPA. To do that, we sampled pairs of (LSAT,GPA) data with replacement. Here is a little script that would do something like that using (X,Y) data that are independently drawn from the normal distribution


```r
xdata<-matrix(rnorm(30),ncol=2)
```

Everyone's data is going to be different. With such a small sample size, it would be easy to get a positive or negative correlation by random change, but on average across everyone's datasets, there should be zero correlation because the two columns are drawn independently.


```r
n<-15
theta<-function(x,xdata)
  {
  cor(xdata[x,1],xdata[x,2])
  }
results<-bootstrap(x=1:n,nboot=50,theta=theta,xdata=xdata) 
#NB: xdata is passed to the theta function, not needed for bootstrap function itself
```

Notice the parameters that get passed to the 'bootstrap' function are: (1) the indexes which will be sampled with replacement. This is different that the raw data but the end result is the same because both the indices and the raw data get passed to the function 'theta' (2) the number of bootrapped samples (in this case 50) (3) the function to calculate the statistic (4) the raw data.

Lets look at a histogram of the bootstrapped statistics $\theta^{*}$ and draw a vertical line for the statistic as applied to the original data.


```r
hist(results$thetastar,breaks=30,col="pink")
abline(v=cor(xdata[,1],xdata[,2]),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Parametric bootstrap
---------------------

Let's do one quick example of a parametric bootstrap. We haven't introduced distributions yet (except for the Gaussian, or Normal, distribution, which is the most familiar), so lets spend a few minutes exploring the Gamma distribution, just so we have it to work with for testing out parametric bootstrap. All we need to know is that the Gamma distribution is a continuous, non-negative distribution that takes two parameters, which we call "shape" and "rate". Lets plot a few examples just to see what a Gamma distribution looks like. (Note that the Gamma distribution can be parameterized by "shape" and "rate" OR by "shape" and "scale", where "scale" is just 1/"rate". R will allow you to use either (shape,rate) or (shape,scale) as long as you specify which you are providing.

<img src="Week-2-lab_files/figure-html/unnamed-chunk-18-1.png" width="672" />


Let's generate some fairly sparse data from a Gamma distribution


```r
original.data<-rgamma(10,3,5)
```

and calculate the skew of the data using the R function 'skewness' from the 'moments' package. 


```r
library(moments)
theta<-skewness(original.data)
head(theta)
```

```
## [1] 0.3601703
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##     shape       rate  
##   5.132009   6.718056 
##  (2.224515) (3.059277)
```

Now lets sample with replacement from this new distribution and calculate the skewness at each step:


```r
results<-c()
for (i in 1:1000)
  {
  x.star<-rgamma(length(original.data),shape=fit$estimate[1],rate=fit$estimate[2])
  results<-c(results,skewness(x.star))
  }
head(results)
```

```
## [1] 0.7570436 0.8826752 0.8528558 1.3347715 0.9518794 1.3476444
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-22-1.png" width="672" />

Now we have the bootstrap distribution for skewness (the $\theta^{*}$ s), we can compare that to the equivalent non-parametric bootstrap:


```r
results2<-bootstrap(x=original.data,nboot=1000,theta=skewness)
results2
```

```
## $thetastar
##    [1]  0.1056420675 -0.4071180651  0.3715989673  0.4221943048  0.4022082769
##    [6]  0.2424930200  0.4455174653  0.5368213941  0.4625334812 -0.0150312705
##   [11] -0.2134686777 -0.3867761476 -0.0104746848  0.1108378743  0.5553779725
##   [16] -1.2890802229  0.2969509421  0.0425958856  0.0598333440 -0.3757311873
##   [21]  0.3618371853 -0.3070739087  0.2163570553  0.0462164826 -0.4232930697
##   [26]  0.4357932340  0.3589854613  0.2718261298  0.6709205036 -0.4040938788
##   [31]  0.1570626346  0.4376229268  0.5989748321  0.5625681688 -0.3853314958
##   [36]  0.6734282052 -0.1438465030  0.3554043481 -0.1212762017  0.9464857109
##   [41] -0.0951255989 -0.3558803804  0.4245402741  1.8730522281  0.0021834411
##   [46] -0.3480491404  0.0436124848  0.0559331508  0.2324760092  0.4343654098
##   [51]  0.4564334428 -0.6327373507  0.2454305506  0.2372092496  0.0562354488
##   [56]  0.3362877773 -0.2485499880  0.0610108053  0.1226864108  0.3065004037
##   [61] -0.1146316632  0.3655084705  0.0756410169  0.3277228301  0.1079789801
##   [66]  0.2088505916 -0.2423750519  0.4112236027 -0.3466215386  1.8412150517
##   [71] -0.3736185222  0.3784070537  0.7074532286 -0.1261747417  0.2322997637
##   [76] -0.3674965573  0.1447788151  0.2499711269  0.9568121186  0.0358693889
##   [81]  0.7113032863 -0.2912545266  0.2941868623 -0.2160712332  0.2631691283
##   [86]  0.7260059342  1.3609233704 -1.0351277242 -0.7808257323  0.1175074582
##   [91]  0.0536382524  0.6126548008 -0.3750809977 -0.4662079491  0.6008201751
##   [96]  0.8287334519  1.2779504610 -0.3678519107  0.2772080158 -0.5938487483
##  [101]  1.3178948892  1.8420230059  0.5480885610  2.2932305684  0.6875451942
##  [106]  0.2519638772  0.9496516555  0.2324520701 -0.2870317315  0.0235026486
##  [111] -0.3644586849 -1.1512703182 -0.6938205994  0.3510803334 -0.1543848360
##  [116] -0.7808177042  0.1120416813  0.5992664157 -0.3262151545 -0.3767753263
##  [121]  0.3754059969  0.3893279217  0.5323781554  0.7946727494  0.5934822441
##  [126] -0.3511820636  0.1070067554  0.1258580718  0.6631291820  0.0849654938
##  [131] -0.6207929852  0.6229881249 -0.3707006526  0.1545992119 -0.3784893451
##  [136]  0.3521564339  0.9095278390  0.0425776192  0.8481892267  0.6783999839
##  [141]  0.6718120329  0.8603207194 -0.2590298065  0.4665280357  0.0429807849
##  [146] -0.4684353462  0.3675191602  0.1129162100 -0.2105039546  0.4318424686
##  [151] -0.1193634907  0.4552329345  0.3268324537  0.8703118883  0.5501943483
##  [156]  0.4760654418  0.6678582236  0.6697370395  0.2344978724  0.8022792458
##  [161]  0.2204565610  0.1179432311 -0.1863134169  0.5059687494 -0.3534685333
##  [166]  0.1931578289  0.0755939266  0.4796947004  0.5425684847  0.8395071455
##  [171] -1.0045849049 -0.2785483512  0.0226701957 -0.2059468044 -0.6553705339
##  [176]  0.3806357058  0.3865815878  1.1323219664  0.3780677160 -1.1763896522
##  [181]  0.2144907945  0.4746599994  0.3849578367  0.1222449732 -0.7059696923
##  [186]  0.1622506317  0.5527235514  0.7172438160  0.8418303412  0.3261737863
##  [191]  0.6742451548  0.5012001631  0.3792047117  0.3745210158  0.3262578362
##  [196]  1.0003176123  0.7587447900  0.4115663707 -0.5020141022  0.9716595030
##  [201]  0.0094514874  0.2381812381 -0.8374518528  0.1108153548  0.3988550364
##  [206] -0.6780190479  0.2445586546  0.4822952266  0.8522585336 -0.3634611321
##  [211]  0.1637216289 -0.5791620119  0.1559242002  0.5805937577  0.2963217837
##  [216] -0.1055132572  0.5479247112  1.1541125111  0.1504063784 -0.1213952324
##  [221]  0.1676171476  0.7452831572  0.0177396808  0.9050606464  0.0732454231
##  [226] -0.3991380850 -0.2889658381  0.0981841760  0.2914085617  0.6448276419
##  [231]  0.6808718184  0.1844924955 -0.3629326660 -0.5412417504  0.3424682784
##  [236]  0.1789382997 -0.7155664044  0.5232747347 -0.8291080510  1.3375015309
##  [241] -0.0541431266  0.4996275470  0.0536382524  0.3062481633  0.2338601238
##  [246]  0.2925530778 -0.3710067947  0.6638366561  0.1264744273  0.5732349126
##  [251] -0.6896501700 -0.6465050031  0.1320294941  0.6951165051 -0.3134354286
##  [256]  0.4249527768 -0.6896501700  0.1522506204 -0.1067566344  0.6822440259
##  [261]  0.3092239133  0.1568987633  1.2397748897  0.6750496398 -0.3821646486
##  [266]  0.2010229015  0.1769963296  0.5078079441  0.3728545548 -0.0060215957
##  [271]  0.6815200619 -0.1424317175 -0.4750834115 -0.3158396241  0.6212027722
##  [276]  0.7728043073  0.0384869939  0.2747984640  0.3788991380 -1.1565572362
##  [281] -0.4612055240  0.0974182932  0.7250249910 -0.0200253157 -0.3379387206
##  [286] -0.3715228036  0.5668674578 -0.1717502227  0.0879934008  0.5584847772
##  [291] -0.0049076649  0.3139429061 -0.1463107177  0.6374273845 -0.6842784491
##  [296]  0.0406301903  0.6784825209 -0.4425822984 -0.7355975392 -0.0942623674
##  [301] -0.0285782198 -0.0551004341 -0.2411029373  0.3636915583  1.3654240861
##  [306]  0.7273560093  0.8228216061  0.4534461704 -0.4340963316  0.3772028818
##  [311]  0.7567239018 -0.7682168175  0.6001851902 -0.3732788306  0.5814917366
##  [316]  0.4306364585 -0.4496066017  0.6457626322  0.4958995124  0.7041020529
##  [321] -0.2401444406 -0.3160415738  0.2983443262 -0.0533889047 -0.3700893880
##  [326]  0.0982471636  0.2205223683  0.8054311293 -0.5427334952  0.7271663983
##  [331]  0.5134592887  0.5524971238  0.2529004805  0.7052302267  0.0060805636
##  [336] -0.0883803407  0.6681211007 -0.1569267983  0.3563451016  0.6092639101
##  [341] -0.2667822200  0.5097712150  0.2871524443  0.7105092183  0.5037150302
##  [346]  0.7138206486  0.2904416135  0.5451619184  0.1641770754  0.0777599535
##  [351]  0.7447109549  0.5842293461  0.6863682957  0.5038566557  0.3548302099
##  [356]  0.0383591888 -0.6561510046 -0.5366952843  0.2368945918 -0.9639537214
##  [361] -1.0429718237  0.2668664953  0.3173655799  0.7782597542  0.2870905488
##  [366]  0.0479857044  0.0358905965  0.6664742929  0.5789173031 -0.1129984328
##  [371] -0.1624807481  0.4158419472  0.5301144281  0.3626777177  0.0305827748
##  [376] -0.1520750170  0.3601703496  0.4242853701  0.7697172337 -0.2553497898
##  [381] -0.0150734326  0.3478474245  0.4064826006 -0.1796301888  0.1463397343
##  [386] -0.6025377623  0.6196309831  1.0412713995  0.7705640606 -0.4215752651
##  [391]  0.9062461489  0.1121100201  0.2906724756 -0.1385817183 -0.0246044795
##  [396]  0.0495651646  0.1640380631 -0.6663069370  0.2558413343  0.4588975588
##  [401]  0.5516600319  0.6854779088 -0.6654215987  0.1542624906  0.8154714783
##  [406]  0.1796338965 -0.3668215993  0.3867459698  0.9021474416  0.2279999928
##  [411]  0.3345504869  0.0475346565  0.4430156941  0.0352418281  0.2576929563
##  [416]  0.0809027845  0.8607387724 -0.4416124466  0.3171828441  0.3070958637
##  [421]  0.3693556340 -0.5877835422  0.4585594871  0.2743797772  0.7153402895
##  [426]  0.7124306142 -0.2726442711  0.1997663285  0.3248210414 -0.0065675679
##  [431]  0.4108914427  0.4812009091 -0.3016360731  0.5917594044 -0.1499040460
##  [436]  0.3586819343  0.2027134289  0.6716154139  0.5612023857 -1.9359338822
##  [441]  0.9023336659  0.4397432496  0.6497920019  0.7161535705  0.3060762572
##  [446]  0.0605363911  0.5621032241 -0.4961841986  0.6187643912 -0.5351378747
##  [451]  0.6638871598  0.5997942636 -0.6979037059  0.4419399320  0.0784710719
##  [456] -0.2055910772  0.4941180211  0.7594456540  1.1331542656 -0.2689440691
##  [461] -0.2214379654  0.0771330007 -0.0150381622  0.4051064717  0.5685670172
##  [466] -1.0100696752  1.0360543946 -0.0648157054  0.0034351393  0.1013852433
##  [471]  0.2008506886  0.4592901649 -0.0895132388  0.4806757831  0.9203315832
##  [476]  0.4508381268  1.5115045069  0.1890323643  0.0574608312 -0.5606273861
##  [481] -0.4226614844  0.3597904516  0.6636350406  0.8385934209 -0.4238889364
##  [486] -0.2262610114  0.2411180703  0.0368364308  0.1887406030 -0.1212092012
##  [491]  0.1010405506 -1.1900857033  0.0744301251  0.2945965138  0.4266081564
##  [496]  0.5065359002  0.7427442152 -0.1258860503  0.8317376882  0.3021203246
##  [501]  0.4050724872  0.2049859218 -0.8151909065  0.1801619100  0.4894833902
##  [506] -0.3201160665  0.4598081749  0.0138630833  0.9976108516 -0.1438431075
##  [511]  0.2973324131 -0.8838468610  0.2998150767  0.9709740234  0.7482827372
##  [516]  0.2176831788 -0.1206007142 -0.8446703471 -1.0784214686 -0.6437377354
##  [521]  0.3090012606  0.1024288795  0.4620130260  0.9680997734  1.0966033137
##  [526]  0.7963659276 -0.0254799425  0.3392694183  0.3393113357  0.3737332551
##  [531] -0.6703093225  0.7393356143 -0.0794971730  0.8212583112 -0.8070307703
##  [536]  0.6780136538  0.2018773538  0.0650538097 -0.3251252354 -0.0095715126
##  [541]  0.0575965131  0.1695102444  0.0151150251  0.3872318501 -0.2207299600
##  [546]  0.6851752949 -0.2471408418  0.7013413514  0.2672277081  0.8356720720
##  [551]  0.0586280243  0.1733982564  0.7281334129  0.4889987394 -0.3966434730
##  [556]  0.0979874985  0.2825365656  0.3140701103 -0.1432712744  0.1528943442
##  [561]  0.6780511035 -0.1599242085  0.2277873650  1.1669695081 -0.1577484527
##  [566]  0.1205520141  0.7989980322  0.4332875274  0.0500060595 -0.0683785028
##  [571] -0.0223165765 -0.1401656945  0.6923288600 -0.3546326882 -0.6977927930
##  [576]  0.7486263675  0.4205675456 -0.3242481422 -0.5695173484  0.5257257259
##  [581] -0.2708036737 -1.0837897418 -0.1465148587 -0.0524439193 -0.2421134015
##  [586]  0.0384869939 -0.2350453937  0.2394195633  0.0166165791 -0.0943256427
##  [591]  0.1481505416  0.3056687393 -0.1004306257  0.0541126091  0.6763843892
##  [596]  0.4833879021  0.3359639813 -0.0128684502  0.2047730550  0.5073473592
##  [601]  0.7210875048  0.5935512827 -0.2958767600  0.4124510472  0.3338196363
##  [606] -0.8521240329 -0.1366438367  0.0303673745  0.5008568666 -0.0118666558
##  [611]  0.2149832811  0.7060698141 -0.4253987808 -0.2654841444  0.2288006165
##  [616]  0.2111079878  1.4930400154  0.0454528260  1.2743956605  0.4058796593
##  [621] -0.0355097952  0.2626936407  0.3286273414 -0.1995402813  0.6669471274
##  [626]  0.7109106763  0.8770007575  0.3212981604 -0.0169959907 -0.0130169805
##  [631]  0.3705136663  0.8979855285  0.5305166851  0.4845827657  0.2156070675
##  [636] -1.1938861410  0.2516692139 -0.2938490956 -0.0637333573  0.6009304605
##  [641] -0.0335625169 -0.7095569677  0.9466962598  0.6176776703 -0.0202318813
##  [646] -0.0855329071  0.1176817813 -0.0274688092 -0.5826176581  0.2343487833
##  [651]  0.1474566601  0.8836924576 -0.4351882068  0.2684143048  0.3190220519
##  [656]  1.2010452509  0.7020228787  0.4148711086  0.0701894857  0.2240952731
##  [661] -0.2625166976  0.7650956254  0.9407551260 -0.0430055473 -0.2764772985
##  [666]  0.2771419239 -0.4099334884  0.0074987739  0.4829431307  0.4245402741
##  [671]  0.0265996535 -0.7943390487 -0.4433051695 -0.0284883703 -0.0016020950
##  [676]  0.5944889540  0.5146290932  0.6479593101  0.0151150251 -0.8263574246
##  [681] -0.4541700518  0.3439348641  0.8340743275  0.4800623834  0.6681211007
##  [686] -0.0765824230  0.8696536745 -0.2994707969 -0.3029745222  0.1586319612
##  [691]  0.2483207133  0.2285587740 -0.2583573996  0.3317639347 -0.3159360021
##  [696] -0.3403096352  0.2256248663  1.1124932945 -0.3439528848 -0.3385246818
##  [701]  0.8100522153  0.5491339475 -0.4051960198  0.1194815174 -0.6534013244
##  [706]  0.6195404634  0.1891889690 -0.0389590711  0.3176147927  1.2055754770
##  [711] -0.2411029373 -0.6280096241  0.3475737765  0.2350007737  0.3406418551
##  [716]  0.4231211383 -0.0678299519 -0.6344258446  0.4525751247 -0.3505263892
##  [721]  0.1045453764 -0.1833426814 -0.1372525196 -0.6606128728  0.3261477637
##  [726] -0.2550911486  0.5179601189  0.1196872253 -0.3744444562  0.5125777665
##  [731] -1.0285739802  0.7927971608  0.3504693852  0.3621184799  0.3479744401
##  [736]  0.0869956652 -0.4869235644  0.4026524544  0.3954037894 -0.3247565631
##  [741]  0.3734691064 -0.4685185432  0.2509810621  0.3666917499  0.3312988193
##  [746] -0.7335722388  1.0268315806  0.8435064997  0.4178203385 -0.1347919416
##  [751]  0.6796163886 -0.3276136708  0.2771685593 -0.5125625541 -0.0134264589
##  [756]  0.3500706133  1.0157521304  0.9007308815  0.5476207389  0.2526995746
##  [761]  0.0471676073 -0.1227113263 -0.1312981278  0.7133021558  0.4937621251
##  [766]  0.4598838728  0.1288777402  0.2434292813 -0.3720665442  0.2305426686
##  [771]  0.2658402287  0.3692979805  0.0546353019  0.4037371018 -0.5205472852
##  [776]  0.1919091919  0.7368975815  0.4689058098  0.3111147152  1.1911045749
##  [781]  0.5441091297  0.7845813745 -0.3920669477 -0.5329297755  0.3250414161
##  [786] -0.4635242014  1.1011794198  0.7730661048  0.2303828857  0.0711283136
##  [791]  0.6834044687  0.3358969475  0.3102827123  0.5245725780 -0.1226791399
##  [796] -0.0576863823  0.3315536051  0.9496516555  0.5868160846 -0.5606273861
##  [801]  0.6324924945 -0.2636756314  0.2433582999  0.5114644195  0.1949434898
##  [806]  0.7087284621 -0.1247701463 -0.1091222673  0.2738042259  0.3663822322
##  [811]  0.0311865027 -0.0016205548  1.1224385555  0.7316951063  0.7324018291
##  [816] -0.3411575989  0.1354480708 -0.1738333420 -0.2351693678  1.0779239067
##  [821]  2.0215624889  1.5435590272 -0.1103311721  0.0299337724 -1.2970653666
##  [826]  0.3657123185  0.2792973012  0.8084815298 -0.0004089186 -0.0362332254
##  [831]  0.7258133849  1.1146238769 -0.8441809404  0.7612263623  0.1273948962
##  [836] -0.7996737351 -0.3430573724  0.2004767285 -0.3696386495  0.3295827302
##  [841]  0.6088861988  0.2353692502  0.4417928622  1.2736775642 -0.5295841436
##  [846]  0.8284376573  0.2796848695  0.6021479918 -0.0934449072 -0.6438088179
##  [851] -0.8467282588  1.1523901695 -0.5989754909  0.1926016489  0.8154714783
##  [856]  0.3536801524  0.3394481928  0.1159521753  0.1368191712 -0.6118119575
##  [861]  0.6370450678  0.7922387530  0.9189418592  0.0895116127  0.0265383223
##  [866]  1.4506501936  0.7958480207 -0.1865592046 -0.6582188796  0.6949591195
##  [871]  0.3261642137  0.1047247449  0.0602772545 -0.2065978621  0.3592054309
##  [876] -0.0328763478  1.0078208996 -0.6732325319  0.2078806009  0.2076249231
##  [881] -0.5920914981 -0.3827313730  0.1223040267  0.3102827123 -0.6940324143
##  [886]  1.2282784920 -0.0054306900  0.4275112258  0.3903290229  1.1243453991
##  [891]  0.2364022766  0.2674364700  0.9997094662  0.7014789394  0.3542247912
##  [896] -0.8609096641 -0.3732788306  0.2214122372  0.4793209383  1.3812216272
##  [901]  0.3378448531  0.0250052834  1.5576264596 -0.2274460141  0.2849760328
##  [906]  0.3067887039  0.2888878047  1.0171608319  0.5590044479 -0.0900195633
##  [911]  0.0570494654 -0.1306779264  0.3134207988 -0.0222866959  0.6606809241
##  [916]  0.3927570592 -0.1197178551  1.1718608807  0.4726721970  0.4873643470
##  [921] -0.3854887406  0.0398175392  0.7093032850  0.3477405381  0.0046027619
##  [926]  0.3376924632  0.6672042390  0.8516816745  0.7210875048 -0.0932837967
##  [931]  0.0110336719  0.3965251895  0.5064828575  0.5103072201  0.2061928618
##  [936] -0.0495695148 -0.1109743583  0.1195841184 -0.5028068634  0.3611628539
##  [941] -1.4175698740 -0.0812621559 -0.3716737829 -0.3811924723 -0.1293588139
##  [946] -0.0515120020  0.0807819825  0.2698260345 -0.4508280523 -0.2411971769
##  [951] -0.6545111889  0.4400730623  0.2082301314  0.3604370405  0.5241829275
##  [956]  0.1523378750  0.1846202136 -0.6803909503  0.9238629275  1.0242234829
##  [961]  0.3856343776  1.4197821214  0.6238251188  0.5764246654  1.0193264459
##  [966] -0.4774245974  0.1178442390 -0.4800795539 -0.3674439841 -0.0004089186
##  [971]  0.2406329144  1.4291752401  0.5120499546 -0.0204116403 -0.2184986875
##  [976]  0.4920152420  0.6306422275  0.3574543704  0.7720481070  0.4902712022
##  [981] -0.2336381931  0.6689039470  0.1723940616 -0.3756188884  0.3806982725
##  [986]  0.0775897302  0.2136443110 -0.7625116171 -0.1568119711  1.3802501305
##  [991]  0.1603837414  0.0764463326  0.1196399126 -0.1028082984 -0.2415247256
##  [996]  0.3209019983 -0.6795072926  0.8721799107  0.1761605664  0.2115292799
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = original.data, nboot = 1000, theta = skewness)
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-23-1.png" width="672" />

What would have happened if we would have fit a normal distribution instead of a gamma distribution?


```r
fit2<-fitdistr(original.data,dnorm,start=list(mean=1,sd=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##      mean         sd    
##   0.7639134   0.3253039 
##  (0.1028701) (0.0727371)
```

```r
results.norm<-c()
for (i in 1:1000)
  {
  x.star<-rnorm(length(original.data),mean=fit2$estimate[1],sd=fit2$estimate[2])
  results.norm<-c(results.norm,skewness(x.star))
  }
head(results.norm)
```

```
## [1]  0.1827346 -0.3270486  0.3443147 -0.5032813  0.4128739  0.6903762
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results.norm,breaks=30,col="lightgreen",freq=F,add=T)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-24-1.png" width="672" />

All three methods (two parametric and one non-parametric) really do give different distributions for the bootstrapped statistic, so the choice of which method is best depends a lot on the situation, how much data you have, and what you might already know about the underlying distribution.

Jackknifing is just as easy at bootstrapping. Here we will do a trivial example for illustration. We will write a little function for the mean even though you could put the function in directly with 'jackknife(x,mean)'


```r
theta<-function(x)
  {
  mean(x)
  }
x<-seq(0,9,by=1)
results<-jackknife(x=x,theta=theta)
results
```

```
## $jack.se
## [1] 0.9574271
## 
## $jack.bias
## [1] 0
## 
## $jack.values
##  [1] 5.000000 4.888889 4.777778 4.666667 4.555556 4.444444 4.333333 4.222222
##  [9] 4.111111 4.000000
## 
## $call
## jackknife(x = x, theta = theta)
```

**<span style="color: green;">Checkpoint #7: Why do we not have to tell the 'jackknife' function how many replicates to do?</span>**

Let's compare this with what we would have obtained from bootstrapping


```r
results2<-bootstrap(x,1000,theta)
mean(results2$thetastar)-mean(x)  #this is the bias
```

```
## [1] -0.0668
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8874601
```


Everything we have done to this point used the R package 'bootstrap' - now lets compare that with the R package 'boot'. To avoid any confusion (a.k.a. masking) between the two packages, I recommend detaching the bootstrap package from the workspace with


```r
detach("package:bootstrap")
```


The 'boot' package is now recommended over the 'bootstrap' package, but they give the same answers and to some extent it is personal preference which one prefers to use.

We will still use the mean as the statistic of interest, but we will have to write a new function for it because the syntax of the 'boot' package is slightly different:


```r
library(boot)
theta<-function(x,index)
  {
  mean(x[index])
  }
boot(x,theta,R=999)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = x, statistic = theta, R = 999)
## 
## 
## Bootstrap Statistics :
##     original     bias    std. error
## t1*      4.5 0.01551552   0.9120928
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 6 7 8 9 
## 1 2 1 1 2 1 1 1
```

```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
mean(x)
```

```
## [1] 4.5
```

```r
bias<-mean(xmeans)-mean(x)
se.boot<-sd(xmeans)
bias
```

```
## [1] 0.0189
```

```r
se.boot
```

```
## [1] 0.8773954
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

