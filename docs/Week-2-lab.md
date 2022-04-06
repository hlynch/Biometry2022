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
## 0 3 4 5 7 9 
## 3 2 1 1 1 2
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
## [1] -0.0312
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
## [1] 2.699753
```

```r
UL.boot
```

```
## [1] 6.237847
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.2
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
##    [1] 5.6 4.4 4.6 2.0 4.1 4.2 4.9 4.7 5.2 5.7 5.9 4.6 2.5 6.0 4.1 4.2 3.5 4.0
##   [19] 4.6 2.3 5.5 5.6 4.2 3.9 5.0 3.6 4.9 4.6 5.9 4.7 5.0 4.6 3.4 4.0 3.6 4.2
##   [37] 5.5 6.4 5.4 3.2 5.2 3.8 2.8 5.4 2.8 5.0 4.9 5.0 4.8 5.0 4.1 2.8 4.9 5.0
##   [55] 4.8 6.1 4.0 3.4 4.7 3.2 5.6 3.9 5.9 4.4 2.9 5.0 3.8 3.8 6.0 3.4 3.5 4.5
##   [73] 4.4 4.9 6.3 5.8 4.1 4.5 3.4 5.3 6.3 4.9 3.1 5.0 3.6 3.5 3.4 4.8 4.0 4.8
##   [91] 5.0 5.3 4.8 5.5 4.8 3.6 4.8 3.7 4.7 5.0 4.0 5.1 3.2 3.6 3.7 4.0 4.5 3.9
##  [109] 5.5 5.1 3.5 3.8 3.1 5.4 5.2 4.0 6.0 4.3 4.6 4.7 4.6 2.7 3.8 4.1 5.1 4.2
##  [127] 4.6 3.7 4.9 4.1 4.6 3.8 3.8 5.0 5.0 4.2 4.1 3.5 2.9 3.6 3.3 4.8 4.6 5.2
##  [145] 4.1 5.0 3.6 6.0 3.9 4.9 3.7 6.2 4.6 2.7 4.1 4.5 4.6 3.7 4.6 4.1 4.5 5.0
##  [163] 5.3 4.5 3.5 3.8 4.0 4.5 5.5 5.0 5.7 5.2 3.9 4.5 5.0 5.3 3.7 3.8 2.2 6.1
##  [181] 3.9 4.4 4.9 3.2 5.3 5.2 4.3 4.1 4.8 4.6 3.4 3.9 4.8 5.2 4.4 4.9 5.1 4.6
##  [199] 4.6 5.2 4.4 6.0 3.4 4.4 3.9 2.1 3.7 4.7 3.4 3.8 5.4 4.9 4.5 5.6 4.9 3.8
##  [217] 5.3 4.0 5.5 3.7 6.4 2.9 5.7 4.3 4.3 3.8 4.3 4.1 4.0 4.8 3.4 3.4 4.6 6.4
##  [235] 5.4 2.9 4.5 2.5 3.4 5.5 5.1 6.0 3.0 4.0 4.1 5.0 4.0 5.2 3.5 3.0 4.8 5.1
##  [253] 2.7 3.5 4.6 2.5 4.9 5.6 5.1 4.8 5.0 4.1 4.4 3.7 3.9 4.4 3.0 4.3 4.9 4.3
##  [271] 3.7 5.1 4.7 4.4 5.1 4.5 5.3 4.2 5.3 4.7 5.5 5.0 4.9 3.6 5.3 5.0 4.0 4.2
##  [289] 4.6 4.9 4.9 3.0 3.7 4.3 6.3 2.5 5.4 5.3 4.9 5.8 5.0 3.9 4.6 4.1 4.4 4.3
##  [307] 2.9 5.7 4.3 5.5 5.1 5.4 4.6 3.4 3.9 3.7 4.4 4.8 5.3 4.7 5.9 4.3 3.8 5.3
##  [325] 3.0 3.9 3.9 4.4 4.3 3.8 3.0 5.0 4.0 4.9 5.4 3.7 4.2 4.7 4.6 5.8 5.0 4.0
##  [343] 4.8 5.4 5.5 4.1 3.9 3.3 2.4 3.1 3.5 4.9 4.2 4.1 4.4 4.5 5.2 6.4 4.3 4.5
##  [361] 5.1 5.2 5.0 5.7 4.2 5.2 6.0 4.7 4.8 3.5 4.2 3.4 5.1 4.2 5.2 4.9 2.8 2.9
##  [379] 4.3 5.8 4.1 3.8 4.4 4.5 4.7 5.3 4.6 3.4 4.3 4.0 3.8 2.7 4.1 5.8 5.2 5.5
##  [397] 4.6 4.4 4.2 5.4 5.1 5.4 5.6 4.1 4.9 4.5 5.5 3.8 5.3 4.3 5.0 4.8 4.5 2.7
##  [415] 4.2 4.2 3.8 2.9 4.7 5.0 5.0 4.2 5.2 4.7 4.6 4.3 5.4 4.2 3.6 2.4 5.1 4.4
##  [433] 3.3 4.2 5.2 4.9 5.5 3.7 5.0 4.9 3.5 5.3 4.1 4.5 6.0 4.9 4.7 4.1 4.6 4.7
##  [451] 3.4 5.6 4.5 3.5 5.8 5.2 5.4 3.3 4.2 3.6 5.5 5.2 4.1 6.3 4.2 4.3 5.6 5.3
##  [469] 5.0 3.1 4.6 4.8 6.4 3.5 3.4 5.2 4.6 2.8 5.6 4.1 5.7 2.8 5.8 6.0 3.7 7.4
##  [487] 4.6 6.1 4.7 4.8 5.3 3.3 5.1 2.8 2.8 3.7 5.2 4.0 4.0 3.2 6.6 6.2 6.1 5.1
##  [505] 5.7 5.5 4.2 5.1 4.0 4.0 4.9 5.0 4.5 2.5 4.4 3.6 4.8 4.2 3.4 5.3 3.9 4.2
##  [523] 4.3 4.8 4.5 3.6 5.4 4.3 4.4 5.9 5.4 4.0 4.7 5.1 4.5 4.7 3.7 4.4 4.1 4.5
##  [541] 5.5 3.9 3.1 3.6 3.5 5.2 5.0 4.3 3.9 5.3 4.2 4.7 3.7 5.0 4.3 3.4 4.1 5.7
##  [559] 5.0 4.1 3.5 3.2 3.8 3.7 4.8 3.8 4.4 5.4 4.9 3.2 5.9 2.9 6.9 3.6 5.3 2.8
##  [577] 5.6 4.1 4.0 5.4 3.6 5.1 4.0 3.8 5.0 4.8 3.8 4.4 3.9 4.3 4.1 4.4 3.4 4.3
##  [595] 4.3 5.6 4.2 4.5 5.5 3.5 5.0 5.3 5.3 4.8 5.0 5.3 3.9 4.6 3.8 2.9 4.9 4.4
##  [613] 5.5 4.0 5.4 4.9 5.4 3.4 4.5 4.1 6.0 3.1 5.5 3.6 5.4 4.4 5.2 4.2 4.6 4.2
##  [631] 4.9 4.0 5.7 4.9 2.8 5.0 4.3 4.6 5.3 4.2 5.4 3.4 3.8 6.2 4.8 3.5 3.4 5.2
##  [649] 6.0 5.5 3.2 3.3 4.8 3.5 5.3 3.6 6.3 2.6 5.5 5.0 3.0 5.3 3.4 4.9 4.0 4.9
##  [667] 4.8 4.9 4.2 5.1 4.9 5.3 4.2 4.4 6.1 3.5 4.1 4.0 4.3 4.1 3.9 4.1 4.8 5.2
##  [685] 5.5 4.9 4.0 6.2 2.9 3.5 5.1 5.1 5.1 4.2 2.7 3.7 5.6 6.8 3.9 5.0 3.6 5.5
##  [703] 4.1 4.4 4.4 3.9 2.8 3.5 5.3 5.0 4.8 3.3 3.9 3.3 3.7 3.1 5.4 4.3 4.0 5.6
##  [721] 3.9 3.5 4.6 6.2 5.6 4.4 4.9 4.1 5.1 4.5 4.7 4.0 4.9 4.3 4.8 5.9 4.6 3.2
##  [739] 3.8 4.4 4.5 3.7 4.6 4.9 4.2 4.6 3.9 3.8 5.2 5.1 5.3 3.7 5.7 4.5 3.3 4.7
##  [757] 5.1 4.8 3.6 4.9 4.6 5.6 3.6 4.6 6.3 6.2 3.8 3.4 3.1 3.5 3.8 5.6 5.3 5.5
##  [775] 4.6 5.0 5.0 4.7 3.6 3.8 1.6 5.0 4.9 5.2 4.1 5.7 3.5 3.9 4.6 5.4 4.8 5.1
##  [793] 4.3 4.7 3.8 4.7 5.9 4.6 4.5 2.9 4.1 4.4 5.3 3.4 4.8 4.6 5.4 3.4 6.3 4.3
##  [811] 4.5 4.7 5.1 4.9 5.2 5.0 5.3 4.2 4.2 3.4 4.4 3.0 3.6 3.5 4.3 4.0 3.2 5.9
##  [829] 4.9 6.3 3.5 4.3 4.7 5.2 5.5 4.9 5.0 4.7 5.1 4.8 4.0 5.6 3.5 4.8 3.9 6.4
##  [847] 5.2 5.2 5.2 3.8 4.2 4.8 4.1 5.1 5.4 4.1 3.3 5.5 4.3 5.0 4.0 5.0 3.3 2.4
##  [865] 4.1 5.4 3.6 6.1 3.7 5.1 6.0 5.0 4.2 3.6 3.0 5.4 4.2 5.1 5.0 3.5 5.9 4.1
##  [883] 5.1 4.9 4.6 2.9 4.6 4.9 4.5 3.3 5.4 3.3 3.5 4.0 4.8 5.0 5.8 5.7 5.5 5.2
##  [901] 7.5 2.9 4.5 4.9 4.3 4.5 3.5 4.5 4.0 3.4 5.5 4.4 6.3 4.5 4.8 5.3 4.9 3.8
##  [919] 4.5 5.3 4.5 5.6 3.8 3.7 4.9 5.0 3.8 5.5 4.8 3.6 5.4 4.5 3.4 2.8 5.1 4.6
##  [937] 3.3 3.4 3.7 5.8 7.1 3.9 4.4 5.3 5.3 4.3 3.6 3.5 5.0 4.4 3.1 4.3 4.3 5.5
##  [955] 2.8 3.9 5.1 4.9 4.8 5.4 5.4 3.7 6.1 6.4 4.1 3.0 3.1 3.9 4.4 4.5 4.5 6.2
##  [973] 3.1 5.6 4.0 3.2 5.4 4.6 5.0 3.3 5.1 6.2 4.3 5.2 4.6 2.9 3.6 4.8 4.8 6.1
##  [991] 5.5 3.0 5.8 5.7 5.5 3.1 2.9 4.7 4.3 5.7
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
##   2.8   6.2
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
##    [1] 3.3 2.3 2.7 5.4 3.3 5.6 3.0 4.4 4.4 4.0 3.9 4.0 4.0 3.4 5.6 5.1 4.6 4.0
##   [19] 4.7 5.3 4.1 5.5 4.0 3.3 3.9 4.9 4.2 4.1 2.5 5.2 4.8 4.8 5.7 3.8 5.1 5.2
##   [37] 5.3 5.3 5.8 4.7 3.8 3.9 4.1 5.4 3.9 4.1 2.8 5.6 6.5 7.2 5.2 4.1 4.4 4.2
##   [55] 4.2 4.6 5.1 3.0 3.6 5.6 3.3 5.8 5.6 4.4 4.3 4.0 4.4 5.6 4.2 7.0 4.8 4.0
##   [73] 4.5 3.7 4.9 4.4 5.8 4.0 5.5 3.4 4.1 4.0 4.3 3.0 5.8 4.1 3.6 4.5 4.3 4.9
##   [91] 3.9 3.3 5.8 3.6 3.5 4.1 3.3 4.3 3.7 4.0 3.6 3.9 4.4 6.3 3.9 3.3 4.8 3.2
##  [109] 6.7 4.6 5.4 4.9 4.5 5.9 5.1 5.2 4.6 3.5 4.0 4.6 3.8 4.3 5.9 3.2 3.4 3.0
##  [127] 3.2 5.2 3.9 5.2 4.8 5.7 3.4 3.9 5.0 4.0 3.0 3.8 6.0 5.1 4.2 2.5 3.7 4.7
##  [145] 5.4 3.5 5.3 4.3 2.6 5.1 3.4 3.6 4.1 4.1 4.8 4.4 4.7 3.9 4.5 2.7 3.9 3.1
##  [163] 3.9 4.4 4.2 4.6 4.9 4.3 6.3 3.5 5.5 3.7 3.7 5.7 4.5 5.5 5.7 4.8 3.9 3.0
##  [181] 5.6 5.2 6.4 4.8 4.9 4.4 4.7 6.0 6.8 4.1 2.9 5.8 5.6 4.2 4.4 4.1 4.5 5.9
##  [199] 3.7 4.2 4.4 4.3 4.6 4.8 4.4 3.7 4.4 5.9 3.3 5.0 5.0 3.2 5.3 5.4 4.6 6.0
##  [217] 5.5 4.0 5.3 4.5 4.2 3.8 6.4 5.4 4.2 5.5 4.1 4.4 4.1 4.9 5.7 4.1 3.2 5.4
##  [235] 4.1 4.7 4.9 5.7 5.1 5.8 3.5 4.7 3.7 3.8 3.8 5.1 4.0 5.8 5.2 3.9 4.5 4.7
##  [253] 3.9 2.2 4.7 5.4 4.6 5.4 4.6 4.4 5.9 2.9 4.6 5.1 5.3 5.3 3.2 4.7 4.8 4.3
##  [271] 3.2 2.4 6.0 2.9 4.5 4.2 4.7 5.9 5.0 5.5 4.7 3.7 4.5 3.0 3.4 5.5 4.1 4.8
##  [289] 3.6 5.1 5.9 4.2 4.2 3.2 4.7 4.9 5.2 4.1 3.8 3.5 4.3 4.4 5.8 4.2 4.1 3.3
##  [307] 4.5 4.6 5.5 6.3 3.2 3.3 5.0 4.5 5.6 3.7 4.0 3.8 4.2 6.0 3.2 5.4 4.9 4.0
##  [325] 2.8 4.8 4.3 2.5 5.5 5.3 6.3 2.4 5.7 2.3 4.8 5.5 3.6 5.8 4.0 6.4 4.9 4.4
##  [343] 3.5 4.3 3.5 4.1 4.0 3.3 3.6 5.6 4.0 4.1 3.4 3.0 4.1 4.5 4.5 4.6 4.2 4.4
##  [361] 5.4 4.3 4.9 4.0 5.9 5.5 5.5 5.3 4.6 4.4 4.4 5.5 4.7 5.0 4.6 5.1 2.3 4.6
##  [379] 4.0 3.6 4.5 3.1 5.0 4.9 4.0 4.1 2.7 4.5 4.6 6.6 5.3 3.3 4.7 5.4 4.5 5.3
##  [397] 3.4 3.6 4.7 4.7 3.6 6.4 4.1 4.6 5.1 3.7 4.5 4.8 6.4 4.4 3.9 4.9 3.9 5.3
##  [415] 4.7 3.3 4.2 4.0 3.4 3.5 4.4 4.3 5.2 4.4 4.7 3.3 4.7 4.1 3.7 4.5 4.9 3.7
##  [433] 4.4 4.6 4.1 3.8 6.5 4.7 4.6 3.8 4.8 5.1 3.6 1.9 6.4 4.6 4.8 3.9 3.4 3.4
##  [451] 2.8 3.7 3.3 4.7 6.6 4.7 3.9 4.5 5.3 4.5 3.2 4.0 3.2 3.5 3.9 4.5 3.6 3.9
##  [469] 4.1 4.0 4.2 4.0 3.6 4.9 4.4 5.5 6.8 5.1 4.2 4.5 5.5 4.5 4.3 5.1 5.5 4.8
##  [487] 4.4 4.9 5.2 5.4 4.3 4.3 3.9 4.4 4.8 5.6 4.9 5.0 5.0 4.7 4.7 5.3 5.7 3.0
##  [505] 4.9 6.0 5.4 4.1 5.3 4.1 5.3 4.8 4.2 5.1 5.2 6.1 3.6 4.0 3.6 6.2 3.5 4.0
##  [523] 3.9 5.1 3.9 4.9 4.8 5.6 5.5 4.8 4.1 4.1 3.6 4.1 3.4 4.6 4.3 3.9 5.7 3.6
##  [541] 4.2 5.5 4.3 3.5 4.3 3.9 5.0 4.4 5.0 5.7 4.1 4.9 4.5 4.1 3.3 4.2 4.5 2.4
##  [559] 5.1 5.6 4.2 4.7 3.9 3.8 5.3 3.6 4.7 4.0 3.7 5.4 3.6 3.8 3.8 5.6 3.0 3.2
##  [577] 4.7 4.2 5.3 4.6 5.2 5.0 3.8 5.5 3.3 5.5 4.7 4.9 4.6 5.0 5.6 5.4 3.8 4.4
##  [595] 3.2 4.6 4.5 5.3 3.5 3.4 3.6 6.1 2.9 3.2 4.4 4.6 6.6 5.4 5.9 5.3 4.4 6.3
##  [613] 4.8 3.5 4.9 4.8 4.2 4.8 5.2 5.2 4.6 4.1 4.6 4.5 4.2 4.4 3.9 3.6 5.9 5.4
##  [631] 3.2 4.5 4.3 4.4 6.1 3.0 4.3 3.3 4.4 4.2 4.0 3.7 4.6 5.0 4.2 3.3 3.5 4.8
##  [649] 4.7 2.0 5.2 2.4 5.7 4.9 3.8 2.9 6.0 5.1 4.4 3.8 4.7 3.3 3.2 4.1 4.8 4.0
##  [667] 3.5 4.8 4.3 4.0 3.6 3.4 4.6 4.1 4.3 4.4 5.3 3.5 5.0 4.6 2.6 3.6 5.1 6.5
##  [685] 3.5 1.5 3.2 4.4 4.2 3.9 5.4 6.2 4.0 3.6 5.2 3.5 7.1 3.8 3.4 5.0 3.6 5.2
##  [703] 4.7 3.7 6.3 4.8 4.2 4.6 5.1 3.1 4.7 5.4 3.4 4.6 4.1 3.2 3.3 3.6 5.1 2.8
##  [721] 4.6 5.8 3.6 5.0 4.8 5.0 2.9 5.1 5.3 5.8 4.2 4.9 2.7 5.0 6.8 4.2 4.6 3.7
##  [739] 3.7 4.2 5.0 4.8 3.8 4.6 3.7 2.6 3.1 5.2 4.6 6.0 4.4 6.0 4.9 4.8 5.4 3.6
##  [757] 3.4 5.4 4.8 2.1 3.8 4.1 3.7 4.6 5.6 4.0 6.1 4.7 3.7 4.9 5.0 4.2 3.5 2.5
##  [775] 3.0 4.1 3.5 2.0 4.9 4.4 6.2 4.8 4.3 5.4 5.3 5.7 5.4 3.2 3.4 4.6 3.3 4.1
##  [793] 1.8 2.6 5.2 4.3 4.0 4.1 5.2 3.9 3.4 4.2 3.2 5.2 5.8 4.8 4.8 4.0 6.0 5.1
##  [811] 3.6 5.6 3.9 4.6 5.2 5.4 3.9 4.6 3.9 3.3 4.1 5.7 3.9 3.8 5.1 2.8 4.0 4.5
##  [829] 4.5 4.3 2.7 3.8 4.5 4.0 4.6 5.8 3.6 4.6 3.2 4.6 3.0 5.3 5.2 4.5 5.2 2.5
##  [847] 2.1 4.3 5.6 3.5 3.1 4.5 6.4 5.0 5.1 7.0 5.1 3.4 3.6 4.2 5.1 4.8 5.7 3.5
##  [865] 5.3 5.7 3.9 3.7 5.4 4.5 4.2 5.3 4.0 5.3 3.9 4.2 4.0 2.3 4.2 3.3 2.9 3.4
##  [883] 6.7 4.3 4.6 4.7 2.7 5.5 5.1 4.3 4.0 4.2 4.3 4.3 5.0 5.5 6.3 3.6 5.1 4.1
##  [901] 3.7 4.4 3.5 5.6 4.8 3.8 5.2 4.2 4.5 4.7 5.3 3.9 4.1 4.4 3.7 4.9 5.2 5.2
##  [919] 4.1 5.4 5.2 4.4 3.6 4.9 4.4 4.5 2.1 5.1 5.1 3.3 6.0 6.1 3.1 4.3 4.9 4.6
##  [937] 4.4 3.9 4.5 3.4 5.0 6.5 3.9 4.7 3.6 4.7 6.0 4.8 4.1 5.1 5.0 3.3 5.0 5.5
##  [955] 3.1 3.7 3.6 5.0 5.0 5.4 4.4 3.7 4.1 5.7 4.4 4.4 3.1 5.2 3.4 4.5 4.3 4.4
##  [973] 5.2 4.7 4.5 3.5 5.3 2.5 2.7 4.7 6.0 5.2 3.8 3.3 5.3 5.8 4.6 3.6 5.5 3.3
##  [991] 4.3 5.6 5.5 3.5 4.3 3.7 4.5 3.3 6.0 4.8
## 
## $func.thetastar
## [1] -0.0584
## 
## $jack.boot.val
##  [1]  0.524068768  0.309467456  0.206705539  0.105604720 -0.001497006
##  [6] -0.124216524 -0.220105820 -0.370231214 -0.442385787 -0.583707865
## 
## $jack.boot.se
## [1] 1.006727
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
##    [1] 5.2 5.4 4.1 5.6 4.3 4.9 5.4 3.5 4.2 5.1 6.1 5.4 4.5 6.2 4.4 2.9 6.2 5.5
##   [19] 4.5 3.3 4.9 3.9 4.0 4.7 2.8 4.2 6.5 4.6 5.3 5.5 4.8 3.6 3.4 4.0 4.4 3.9
##   [37] 5.6 4.6 4.1 5.7 4.2 5.4 5.6 3.9 3.4 4.8 5.0 3.8 5.5 5.7 3.9 4.0 3.8 3.3
##   [55] 4.4 3.9 4.0 3.5 4.3 4.5 4.3 5.5 4.9 4.5 5.9 5.8 3.0 7.2 5.5 3.8 4.2 2.5
##   [73] 4.0 2.3 3.9 4.6 4.5 3.5 3.8 3.7 4.9 4.3 4.1 3.3 4.5 3.1 3.2 5.2 3.3 4.1
##   [91] 4.6 4.3 4.2 3.0 4.7 5.1 4.6 5.0 4.5 3.1 4.6 5.0 2.0 4.8 5.3 3.5 5.6 4.0
##  [109] 5.4 5.8 4.4 4.2 3.4 5.2 6.2 3.6 4.1 4.0 4.7 3.7 3.1 4.0 7.2 4.6 1.6 3.1
##  [127] 3.6 3.1 5.2 5.1 4.8 2.0 4.0 4.7 5.0 3.7 3.2 3.2 5.8 3.1 4.9 4.1 3.3 5.1
##  [145] 4.5 3.6 4.2 4.3 4.9 4.3 3.9 5.2 3.5 4.3 4.3 4.8 6.1 5.1 3.8 3.9 4.3 4.1
##  [163] 5.3 3.0 5.2 4.2 4.1 5.0 5.7 5.4 5.0 6.0 4.3 6.9 4.3 3.4 3.2 5.0 3.9 5.5
##  [181] 3.5 3.7 4.7 4.1 5.6 5.5 4.7 3.4 5.1 5.8 3.6 6.6 6.0 4.5 4.1 5.4 6.5 4.8
##  [199] 4.1 4.8 4.9 4.3 4.5 3.1 5.0 5.1 4.9 5.4 5.1 4.3 4.1 1.8 3.7 4.9 4.5 5.2
##  [217] 3.7 3.4 3.1 6.1 3.1 4.1 3.2 4.9 4.0 4.7 4.1 4.9 3.5 5.7 4.0 2.9 3.8 3.9
##  [235] 4.8 5.2 5.5 4.2 6.0 5.4 5.8 5.6 3.6 3.9 4.9 6.1 6.1 3.9 5.1 5.1 4.2 5.1
##  [253] 4.6 5.0 4.4 4.5 5.4 4.8 4.6 4.3 5.0 3.6 4.6 4.7 4.9 3.6 2.5 5.5 3.9 5.5
##  [271] 5.1 3.7 4.8 3.5 5.8 3.2 4.7 4.5 4.8 3.7 4.1 5.9 4.3 5.5 3.1 5.4 4.7 5.1
##  [289] 3.8 4.1 5.9 3.7 4.8 4.7 5.2 4.4 4.9 3.9 4.8 5.4 4.3 3.7 4.7 3.2 4.6 4.7
##  [307] 4.5 4.5 5.0 5.3 4.1 4.2 4.8 5.3 4.3 6.2 3.8 5.6 3.9 3.2 5.2 4.3 4.2 4.0
##  [325] 5.6 5.9 5.1 3.8 3.8 4.8 5.1 4.9 5.6 5.2 3.0 5.9 4.3 5.1 4.5 5.8 5.8 3.6
##  [343] 4.9 5.3 4.0 3.8 5.1 3.6 3.8 5.3 4.9 6.2 5.4 4.6 5.2 4.6 5.0 3.9 3.8 5.6
##  [361] 4.5 5.1 6.5 5.5 4.3 2.7 5.5 5.1 3.9 4.2 3.7 5.1 4.1 3.7 5.0 5.6 6.3 2.1
##  [379] 3.6 4.5 3.6 6.0 4.0 5.0 4.8 4.2 4.6 6.4 5.1 3.8 5.0 5.1 4.4 2.6 5.7 3.7
##  [397] 4.6 3.9 5.5 4.6 4.0 3.7 3.9 4.8 5.8 3.7 3.8 5.6 5.5 3.6 3.6 5.7 4.7 3.8
##  [415] 5.0 3.5 5.4 3.3 5.4 5.6 4.5 3.1 3.1 2.7 4.7 4.2 5.1 4.7 4.5 5.2 4.0 4.3
##  [433] 5.1 3.2 5.1 5.3 3.3 4.4 5.3 4.8 6.3 3.8 5.0 4.3 5.4 3.6 6.1 5.0 3.2 2.6
##  [451] 4.3 3.7 2.6 4.2 3.8 3.8 6.0 4.9 4.0 6.3 4.9 4.3 3.7 5.3 4.4 3.6 3.3 3.1
##  [469] 5.4 5.4 5.2 5.0 3.3 4.0 4.1 3.9 4.2 5.1 4.4 5.1 4.7 3.7 5.0 5.1 5.2 4.8
##  [487] 5.8 4.7 4.7 4.5 4.9 5.7 4.8 5.5 5.0 4.1 4.5 3.3 3.0 4.7 3.7 4.8 4.8 5.3
##  [505] 3.8 4.7 3.1 5.8 5.0 4.3 3.2 4.9 4.4 3.1 2.8 3.9 5.4 5.4 2.6 4.6 3.6 4.7
##  [523] 4.4 3.8 4.2 5.3 4.5 3.9 4.8 4.6 5.8 4.5 4.5 3.3 4.9 4.6 3.8 5.7 4.6 4.4
##  [541] 5.0 4.7 4.2 5.9 5.3 5.4 4.8 3.5 3.3 5.0 4.9 5.0 5.4 5.5 5.7 5.1 4.5 4.3
##  [559] 4.4 4.1 2.9 4.7 1.9 5.2 4.3 4.7 5.2 5.1 3.9 3.7 4.3 2.6 4.8 3.7 6.1 3.6
##  [577] 3.8 5.5 5.1 4.0 4.9 4.1 3.8 4.1 4.4 4.5 3.8 4.8 2.6 3.7 3.2 3.0 2.8 4.2
##  [595] 3.9 3.1 2.9 4.5 4.3 4.2 3.1 3.6 3.4 5.4 4.8 5.4 4.0 4.1 5.0 4.7 4.8 4.5
##  [613] 3.4 4.0 6.1 4.4 4.0 4.7 4.8 4.1 3.8 4.5 4.4 6.1 3.7 3.9 3.6 5.4 5.3 4.7
##  [631] 2.6 4.1 6.7 5.4 3.3 4.7 4.7 5.4 2.7 3.4 5.3 4.6 4.9 4.8 5.4 4.8 4.3 5.5
##  [649] 5.4 4.2 3.7 4.3 4.4 3.9 4.1 3.6 4.5 4.4 5.6 4.1 4.1 5.8 4.4 4.5 5.0 3.2
##  [667] 4.6 3.7 2.6 5.1 4.7 4.4 4.3 5.2 2.1 5.4 4.2 5.2 5.6 3.8 5.4 4.5 4.3 5.0
##  [685] 4.8 3.4 2.8 4.3 4.5 4.7 3.0 3.4 4.2 4.4 5.4 5.0 5.1 4.7 3.3 4.2 3.2 4.4
##  [703] 4.9 3.2 4.3 4.2 4.9 4.6 3.4 6.1 4.3 3.4 4.9 3.3 4.5 5.6 5.2 5.6 4.0 3.9
##  [721] 4.3 5.8 5.0 3.5 3.3 4.8 4.8 4.5 4.2 4.8 4.6 4.1 3.5 4.5 3.0 4.8 5.0 4.9
##  [739] 5.1 4.0 4.0 5.0 4.9 6.3 4.4 4.7 4.9 5.9 5.5 5.6 3.7 4.0 5.3 1.6 5.7 5.6
##  [757] 5.4 3.6 5.0 3.0 3.5 4.7 5.3 4.1 4.7 6.1 4.6 4.4 2.8 6.0 3.3 5.4 3.5 2.9
##  [775] 4.6 5.2 4.4 4.2 3.9 4.6 4.7 5.9 4.3 4.0 5.7 4.4 4.1 5.2 4.7 4.0 3.7 6.4
##  [793] 6.9 3.1 5.8 4.6 4.5 2.5 2.7 4.4 6.8 5.6 5.0 4.6 5.7 5.0 4.4 4.0 5.3 4.6
##  [811] 6.8 4.9 5.0 4.5 6.8 4.8 4.6 3.3 4.1 4.7 3.9 4.5 4.7 5.5 4.1 3.9 3.6 4.1
##  [829] 6.2 4.6 5.0 4.8 3.2 5.8 4.7 3.9 4.1 4.5 3.6 6.1 5.7 4.2 4.1 4.8 4.9 5.1
##  [847] 5.2 3.6 4.7 6.0 4.1 5.7 5.5 4.3 5.5 5.3 4.4 4.8 4.6 3.9 3.9 4.1 4.8 4.3
##  [865] 3.7 3.8 4.9 4.4 3.5 2.5 3.5 5.6 3.8 5.4 5.4 3.3 4.8 3.7 6.3 4.2 5.9 4.7
##  [883] 5.4 3.5 4.1 3.9 3.2 4.3 7.2 5.0 4.9 4.1 3.8 4.9 5.0 3.8 5.2 3.6 3.8 4.5
##  [901] 5.0 4.7 2.0 4.6 3.5 4.0 3.7 3.3 3.5 5.0 4.4 4.7 5.1 4.0 3.8 4.0 4.3 4.8
##  [919] 4.6 5.0 5.1 4.3 3.5 5.3 4.7 6.0 5.2 4.3 5.1 5.4 5.2 4.5 2.8 5.0 3.0 5.9
##  [937] 5.7 4.9 4.5 3.8 5.2 2.9 4.0 5.6 4.0 4.4 3.7 4.9 5.5 2.7 3.4 2.6 2.6 3.7
##  [955] 5.5 5.2 4.3 4.9 4.0 4.5 4.3 4.4 5.2 5.3 4.1 4.2 4.3 4.4 6.1 4.2 5.0 4.4
##  [973] 5.5 5.6 4.1 4.2 4.0 3.3 4.3 4.5 5.5 5.9 4.2 4.1 4.6 3.7 3.7 3.3 4.0 4.7
##  [991] 3.9 4.9 5.5 4.1 4.8 5.3 5.6 3.3 3.3 4.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.200 5.400 5.208 5.100 5.000 4.900 4.700 4.600 4.500
## 
## $jack.boot.se
## [1] 0.9115546
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
## [1] 1.82101
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
##   3.916650   6.137743 
##  (1.682310) (2.812842)
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
## [1]  1.2202629  0.5454262  0.4281764 -0.3939229  1.1760736  1.4461948
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
##    [1] -0.0092953909  2.1140032143  1.7938037387 -0.1159766263 -0.5580285767
##    [6]  1.3523581648  1.9838056978  0.9198873348  1.8975349535  0.1256083784
##   [11]  1.9716975433  2.1078883917  1.9671486141  2.1008134921  1.9712216712
##   [16]  1.2337915110  1.8445706403  1.6596724139  0.8344142928  0.2843258803
##   [21]  1.3678370115  1.1651626217  1.7742048848  0.2378251331  2.0398675025
##   [26]  0.7715155048  1.8324466305  2.1760689850  1.8068005631  1.1751772165
##   [31]  1.1323588894  0.2707260448  0.7073475580  0.3305819418  0.0791443034
##   [36]  1.7358789630  0.0380564225  1.9082549861 -0.2379111673  1.6873739787
##   [41]  2.0824555176 -0.2799528667 -0.7826198910 -0.1010518118  1.2046679046
##   [46] -1.0142825586 -0.1464882257  0.5464336462  0.6417567281  1.7261146420
##   [51]  0.9134956733  2.1222908342 -0.0931463533  0.3528220309 -0.2575780870
##   [56] -0.1218544555  0.4058551500 -0.5962349178  1.5912372861  0.0029392697
##   [61]  1.3368106713  1.0489958027  1.8809254802  0.2581241241  0.4896366353
##   [66]  2.1014468591  1.1435804057  2.0669300898  1.3315695853  0.3330034029
##   [71]  1.2057223737  0.5572072947  1.2194585277  1.1661806383  1.2342418825
##   [76]  1.4134318441  0.2710195327 -0.3747622703  0.4504817870  1.4141185698
##   [81]  2.4450968304  0.4171397835  2.4077098972  2.2530814487  0.4378002806
##   [86]  0.1294878331  0.0679834645  0.7161684975  1.0253086797  2.1588659877
##   [91] -0.0183680367 -0.0100764873  0.3698188259  2.0010118204  1.2173211538
##   [96]  1.0893556294  0.2233561856  2.1469231919  1.9712216712  1.9989267668
##  [101]  1.5161625158  2.2680990409  1.7415007098  1.4398664920  1.0446754493
##  [106]  0.3005976184  1.1107027375  1.9562329005  0.9713610651 -0.1320424611
##  [111]  0.4253404133  2.3200747439  1.2389841645  0.7711859591  2.2284175226
##  [116]  0.6630768480  0.3437505782  1.9435582696 -0.2004909955  1.7632711427
##  [121]  1.7233609199  1.6171747053  1.8298775618  2.3120891202  0.8184840132
##  [126]  1.3619628715  0.0736477977  2.1768833129  2.0944557381  2.2839944072
##  [131]  2.4410336167  2.1227147211  1.8398244227 -0.0971682447  0.0241716942
##  [136]  1.6909785258 -0.1800060707  2.3185870027  1.4057417086 -0.0983816407
##  [141]  1.8295502111  0.7097629132  2.2083224223  0.3203524001 -0.3562036176
##  [146]  1.1508678061  1.1629357115  0.7243241064  1.0743872250  1.0971195046
##  [151]  0.3073281304  1.8500285660  0.3161850996 -0.7343861891 -0.3903343889
##  [156]  1.1087560343  1.9364119591  0.3515325301  1.1422744772  1.9315042721
##  [161]  1.7675472619 -0.0157020040  1.2621793904  2.0540795648  1.0984152558
##  [166]  1.2767501020  0.0118800721  0.4217287078  2.1591366195  0.7685776354
##  [171]  0.3838007834  1.5292004160 -0.5462868429  0.3088582990  2.4106680248
##  [176]  0.8854614481  2.0922681458 -0.5803932343  2.0259137891  1.8627823608
##  [181] -0.1661002183  1.5774192776  1.2323278639  1.6488528625  0.8919196218
##  [186]  2.0802064049  0.2053112546 -0.0909886554 -0.0967081641  1.2993138276
##  [191]  1.9035758911  1.7634767601  0.3655711525  1.3766378214  2.3787640081
##  [196] -0.5690045291  0.4391995000  2.0992321692  1.2385882577  0.9135082855
##  [201] -0.2404465690  1.7871157564  1.8211271914  1.7114424372  1.6794688415
##  [206]  1.1643641963  1.1923059143 -0.1401648800  0.7033046782  0.1004615976
##  [211]  0.0599557046  1.0179963990  2.0649099088  0.5928791856 -0.5165883624
##  [216]  1.3481852515  1.5208553724  1.1801468748  1.3390553273 -0.3579703250
##  [221]  0.7562644150  0.5997890488  1.4182681356  1.1153930374  0.5071507086
##  [226]  1.8105145259  0.2593385317  2.1827144767  0.4431339502  0.3171314631
##  [231] -0.1577123525  0.6826262046  1.6817853090  0.2817652385  0.2059503385
##  [236]  0.2714934480  0.0951958490  0.7217877423  2.0038093944  0.1584585298
##  [241]  1.1040449838  1.3363174183  1.7237527420  0.1891905768  0.1612176927
##  [246]  0.5081057807  1.5000844660  1.5680823306  0.4705396798  1.6574156286
##  [251]  0.2723207825  1.9308536556  2.0597671534  1.5610541892  1.3600189853
##  [256]  2.0594415967  0.3976665029  1.1158097899  2.0839049226  0.1523762079
##  [261]  1.6961680076  0.1426293648  1.8700584057  0.1629856494  1.0911567026
##  [266]  1.0263133909  2.1783399606  1.6670817501  1.1208564273  1.5521636274
##  [271] -0.6593717823 -0.0832205124  2.2356428335  2.0690995588  2.1295083707
##  [276]  0.3695658502  0.6025432624  0.1789757067  0.9868854353  1.3690506536
##  [281]  1.9509040472  1.3106287662 -0.2795446875  0.3844819726  0.1484697577
##  [286]  1.6193166725  1.8372956980  1.0490847304  1.1058741765  1.5564313324
##  [291]  2.2044261970  1.3068516761 -0.4720998701  0.7551459029  2.0121231266
##  [296]  0.2087761183  1.7067545515  1.1227856047  0.4499995444  1.2571183075
##  [301]  2.0670990134  0.7590340606  2.0584606056  2.4945413920  2.0087840682
##  [306]  0.2104500141  0.2530966667  1.6472287237  2.2942607958 -2.0336066154
##  [311]  0.2228533777 -0.3420804220  1.8382163650  0.0362464152  1.9470232471
##  [316]  1.2474730885  0.1652890095 -0.1811728904  1.4237087671 -0.0762290573
##  [321]  0.5828142470  2.2729223043  1.8415245267  1.2187443279  0.3823416186
##  [326]  1.5202338629  0.0982034951  0.2026664463 -0.5400966909  1.0928589733
##  [331] -0.0585015557  0.1215926431 -0.6900217009  2.1277618358  0.6618168339
##  [336]  1.3240325257 -0.1216126501  1.0302748666  1.0930324538  0.9723954301
##  [341]  2.2872216301  1.3282496228 -0.3279018786  1.8595746977  0.8045866946
##  [346]  1.1551816802  1.6420308000  1.9998125598 -0.1485371081  1.9160097940
##  [351]  2.0257820960  0.0488179634 -0.6370459283  0.9423348336  1.3230615623
##  [356] -0.3010866450  1.8115047535  1.2101924038  0.6707446563  0.0773584982
##  [361]  0.2353953457  2.1080206212  1.9174917799 -0.2521231389 -0.9051871886
##  [366]  1.9253803653  1.9609255046  0.2118086359  0.6392377022  0.7348327192
##  [371]  0.4007155707  1.5919684542  0.2991762811  2.0669300898  1.7359989269
##  [376]  1.9279744169  1.8645274910  1.9415275997  0.3200836251  2.4106680248
##  [381]  1.9183996976 -0.1542328770  0.5678821870  0.2019171130 -0.3139787550
##  [386] -0.2799528667 -0.1306076764  1.8297784406 -0.2943884646  1.8215929552
##  [391]  2.2803632093  1.7738812130  1.8389565601  0.1063830184  2.1633219597
##  [396]  0.1185625877  0.1073802474  1.0829092032  2.3311216225  1.1338214619
##  [401]  1.9402573407  2.1045931856  1.6591640984 -0.1942458195  1.0559471837
##  [406]  0.3673110826  0.6074802082 -0.1564264711 -0.0259788533  0.2699762620
##  [411]  0.6933755288  1.2293573079  0.7756947603  1.2949632169  1.0335773177
##  [416]  1.3809974416  1.3472589888  0.4721287477 -0.3178319762  0.5888085212
##  [421]  0.4119610327  2.2780841439  0.2172251395  1.9503993032  1.0848176069
##  [426]  0.6331500785  1.6568196551  1.7424326001  1.2917242276 -0.5877552774
##  [431]  1.2229718657  1.8518187918  1.2740808328 -0.1109961000  2.2991484872
##  [436]  1.9954598268  1.5852571945  0.7237969381  2.1152475170  1.2077969565
##  [441]  0.5465902327 -0.2465157371  1.9148318520 -0.3121645810  1.9579513454
##  [446]  1.0740633355  1.6996924028  1.7307621822  1.2162365894  1.0594152534
##  [451]  1.0383628322 -0.2324873710  0.4954717780  1.9882230940  1.5051207726
##  [456]  1.2105637144 -0.1276440382  1.9866402436  1.3070476417  0.1171050244
##  [461]  1.1575618424 -0.1498648234  1.8609215308  1.9909646789  1.8950115967
##  [466]  2.4740418143  1.9604714544  2.1389019404  0.4589532299  0.1546370804
##  [471]  1.8085655747  0.4330260483  1.8149747021  0.5602003531  0.6863791541
##  [476]  0.2462758674 -0.0106041086  2.1751722227  2.2394229614  0.3356719853
##  [481] -0.0941187641  1.2261220676  1.7046147518  1.8297784406  0.6843058734
##  [486]  1.8760610004  1.1373764437  1.9038091838  0.9350888349  1.9974858339
##  [491]  1.8621899105  0.6884209762  2.2499188602 -0.4753117922  2.3420630643
##  [496]  2.2422946687  1.0490847304  0.3611142389  2.2570118105  2.3185904136
##  [501] -0.4868744047  2.0516037354 -0.4323829586 -0.0449661143  0.2090221510
##  [506]  2.0556983719  0.5399717647  1.8085655747  1.1052578625  1.9156465543
##  [511]  1.7412655907  0.7133036081 -0.6849061946  1.0509053802  1.3112918794
##  [516]  1.1285133259  1.1913447998  0.2882342999  1.9189116317  0.0020574315
##  [521]  0.5434644314 -0.5778794846  2.0643185712  1.9155488017  2.1729835845
##  [526]  1.9392902018  1.2361938471  1.1137519283  1.1175616245  1.5362377958
##  [531]  0.0147054058  1.9909646789  1.8960604652  0.3012411128  1.1387051264
##  [536]  1.9685331241  2.2536286636  2.2321210690  1.6344881374  1.0681771369
##  [541]  1.9515603608  2.0811241994  1.5861050655  0.4977314609  1.9309671240
##  [546]  0.2179593829  0.8360251724  1.3818876095  1.8135487236  1.9814706633
##  [551]  0.0546676005  2.0596267669  0.5769649959  1.6202038601  1.2820461219
##  [556] -0.2021144861  1.3652359662  2.1297656954  0.0498998003  1.7834370450
##  [561]  1.2876091689  1.1033686486  1.3553503421  0.2322327716  0.7088067202
##  [566]  0.0796197043  2.1657744222  2.0032666245  1.9211408163  0.4696319634
##  [571]  2.0535844436 -0.1042233582  0.5132144293  0.0871055102  1.8764712621
##  [576] -0.5251379190  2.0622382682  1.2809173133  1.8691517493  1.5892202274
##  [581]  1.5601218611  0.9344310678  0.8009119003  2.1138861412  1.2106120334
##  [586] -0.0449661143  1.3948227783  1.1091366680  1.1339720486  1.4493633043
##  [591]  1.0683664434  2.0075209809  1.7811032190  0.9895283460  2.3147221724
##  [596]  1.1412188488  0.4840594938  0.7583594968  0.3594855461  1.7149286853
##  [601]  0.1448118515  1.1087701144 -0.2381191267  1.1481057338  2.0041961219
##  [606]  1.1616459158  1.5663716396  1.0382593112  2.0958168953  0.8225330072
##  [611]  1.7817294875  1.8099952039  1.0030038145  1.2010261452  0.6772928688
##  [616] -1.8095610729  1.1615971439  1.3095826803 -0.2401282426  0.0974994858
##  [621]  1.2277233089  2.0859780713  2.1685925622  1.7199596851  2.2861415308
##  [626]  2.0262879567  2.1739119049  2.0801607042  1.0289059261  1.6525655109
##  [631]  1.1938313694  1.7556217972  1.2488837894  1.2675848049  2.1291009630
##  [636]  0.7108722790  0.4004143330  1.3341797773  1.9698547470  0.8005567727
##  [641]  2.1008134921  1.7406879496  1.3095826803  1.1347279517  2.1163272016
##  [646]  2.0522924284  0.6859223998  1.0281699599  1.8909114877 -0.5406559182
##  [651]  1.9285747897 -0.0704981947  1.9405910343  1.2186730091  1.8901221741
##  [656]  0.4587354401  0.0064070212  1.0639739270  2.1168066647  1.2205098181
##  [661]  0.8390165988  1.6745289508  1.7286575921  0.7444105848  1.1156402458
##  [666]  0.3954882816  1.5924649363 -0.3667655982  0.6808393064  1.9028979474
##  [671]  2.1057517338  1.5633932425  1.8698297325  2.0538855389  0.6605741971
##  [676]  1.7728971884 -0.7840510570  2.0769232900  1.2820461219  2.0281017163
##  [681]  0.1067291863  1.7559268944  1.0169080711  0.9366749536  1.6463759219
##  [686]  0.5045426784  0.6320854784  0.6925104142  1.4458095855 -0.2186729785
##  [691]  1.3281260735  1.9160638261  1.1495578274 -0.1037293025  1.3802426543
##  [696]  1.2421274727  0.2384022682  1.6921825780  1.8271490419  2.0003170185
##  [701]  1.1082082675  2.2375682230  1.8905387509  2.2019378233 -0.0702428695
##  [706]  0.2873404238  1.3393999561  1.5406066280 -0.6132349425  1.8753942766
##  [711]  0.3731208282  1.3270448607  0.1697168394  1.8418887467 -0.0032550959
##  [716]  1.9541088035  0.2844953562  1.8658896835  0.1580182485  2.2144738740
##  [721]  0.3566874936  2.3290833252  0.0118538300 -0.5357486224  1.7942253718
##  [726]  1.7527293981  2.1591878168  2.1583630397  0.7977167393  1.3933218860
##  [731]  2.1857372871  2.0456722603  1.2175581265  1.6882022401  2.0158546236
##  [736]  2.1356106715  1.1443150058  2.0657179857  0.0310712918  0.4369635525
##  [741]  0.2784953367  1.2998480345  2.4235010892  1.1801518815  0.0396589026
##  [746]  1.1763608571  1.9483005150  2.1305594172  0.4626900568  1.3937566789
##  [751]  1.7253498826  1.9675230915  0.2048745347  0.1302845412  2.0684721580
##  [756]  0.7018527554  1.6169621772  1.4082030898  1.9221250166  0.0576927379
##  [761]  1.8590089468  1.1264635607  1.6780677662  1.9595510055  1.8241600882
##  [766]  1.0403293295  1.2337165860  1.6650099602  1.1605773904  0.1481187900
##  [771]  2.0716675265  1.0108343278  1.6522049851  0.8033581897  1.1715913749
##  [776] -0.2401282426  0.1885251170  2.1936693567 -0.0702579589  0.6199693275
##  [781]  1.1371876302  1.7915874673  0.2094922393  2.0859586624 -0.1228998421
##  [786]  1.1009539480  1.0518746481  1.8561992076  1.0646278356 -0.3833007065
##  [791]  1.6959150884  1.6670821792  1.1901090096  0.6630023370  1.2243868857
##  [796]  0.3049378126  1.5932436213  0.3854722250  1.3177662224 -1.0538523903
##  [801]  0.6383776911  2.3830973223  2.1370284145  1.9387353744  1.3822114427
##  [806]  1.9677411236  0.9849627200 -0.0008911046 -0.4156980004  1.0536320898
##  [811]  0.5998367450  0.5722761604  2.0841439109 -0.1194579461  0.0225073198
##  [816]  2.1184924294  2.1326397694  2.1782874677  1.9591232957 -0.0097218669
##  [821] -0.2699035125  1.1839727583  0.0683906139  1.1424692664  1.6937176152
##  [826]  2.0040752823  1.2206928224  1.1173040351  0.6433692066  1.1761350984
##  [831]  1.9883043050  1.5436490435  1.8880917505  0.6403521284  1.3206702804
##  [836]  1.9522249800  0.7305312697 -0.3660767116 -0.1288485302  1.7449158382
##  [841]  0.0015135392  2.3321190894  2.2621716653  0.5654887286  1.0535857106
##  [846] -0.4821668308  1.8277161395  2.1839752849  2.0836403559  0.6487092436
##  [851]  2.0204972212  1.2182070611 -0.7452793344  0.0643725323  1.3133793363
##  [856]  1.2701802271  1.2358213912 -0.4688640073  1.9658162830  0.4144547950
##  [861]  1.5655171617  0.7980517031  1.3955367071  0.1756515523  0.2790542825
##  [866]  1.9220402474  0.0959335015  1.6047807728  0.2807409463  1.4213414018
##  [871]  1.5245195219  1.0597740378  1.4182681356  1.0030888500  1.8038788948
##  [876]  0.4430498817  0.0649201152  2.3487479113  2.0078190196  1.1080740768
##  [881] -0.8319637956  1.1703108621  1.8583055846  0.7398381958  2.1419327195
##  [886]  2.1112948808  1.1320907092  0.7028549915  1.1403208588  2.0900028306
##  [891]  0.2677358976 -1.4266717402  2.4129507690  2.0816828440  1.7794679036
##  [896]  0.5176686302  1.7878460651  1.6807127129  2.0575160221 -0.0887765822
##  [901]  0.0508232775  0.4162315503  1.3288737567  1.2561442802  0.2535437870
##  [906]  1.2486098207 -0.0670604366  2.1271404469  1.3525654494  1.1378786454
##  [911]  0.8079420861  1.0547025319  0.5495073512  0.5090091923  1.7789986085
##  [916]  0.4059343080  0.2570228848  0.0084497484 -0.1591117959  0.2151588578
##  [921]  1.1365208975  1.0816675551  1.2317280486  0.1637889058 -0.3446298508
##  [926]  1.2017091357  1.2297965231  1.6399136066 -0.0557470661 -0.4105635887
##  [931]  1.3208922509 -0.3850885843  2.1360474957 -0.8064807615 -0.6998686126
##  [936]  1.1597993684  2.1649650066  2.1473745607  0.1745394849  0.7631010490
##  [941]  0.3326671175  1.7190501956  0.7133411528  1.4118698283  1.3341829171
##  [946]  0.8162201018 -0.1419550306  0.7523955081  0.6512345708  2.0016426436
##  [951]  1.0735406338  1.2560647658  2.1539882500  2.2284544550  1.2735944362
##  [956]  1.7343262698  0.4685065385  0.5212497553  0.1556727861  1.3803416681
##  [961]  2.2043123180  1.8792904806  1.3726019769 -0.6308096137  2.1237621708
##  [966]  1.6786443736  0.7338743219  1.7315247736  2.1576681957  0.8389315143
##  [971]  1.5420524653  0.4001681181  2.1501736478  0.7647171714  1.2229718657
##  [976]  1.6748072895  1.6492640918  1.1528627625  1.0481082633  1.7674831982
##  [981]  0.0729401517  1.7457381054 -0.2599477290 -0.4843896069  0.1524336271
##  [986]  1.0181722509  0.9152854491  2.0882861634 -0.0847800702 -0.1532210622
##  [991]  1.8698597034  0.6967487198  0.7901458711  2.3018291132 -0.4099564233
##  [996]  0.3148860047  1.2668363163 -0.6795086362  0.8227725622 -0.2805551690
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
```

```r
fit2
```

```
##      mean         sd    
##   0.6381249   0.3782330 
##  (0.1196078) (0.0845733)
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
## [1] -0.039233807  0.032750565 -0.001805298  0.171565847  0.455525452
## [6] -0.631789035
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
## [1] 0.0056
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8996433
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
##     original      bias    std. error
## t1*      4.5 0.009209209   0.9148493
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 4 6 7 8 
## 2 3 1 1 1 1 1
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
## [1] -0.0017
```

```r
se.boot
```

```
## [1] 0.8931544
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

