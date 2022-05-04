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
## 2 3 4 6 8 
## 2 2 1 2 3
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
## [1] -0.0725
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
## [1] 2.601961
```

```r
UL.boot
```

```
## [1] 6.253039
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.6975 6.3000
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
##    [1] 4.7 5.3 3.3 4.2 4.0 5.7 5.5 4.1 5.6 4.4 5.0 4.0 4.7 3.1 4.5 4.8 2.9 5.0
##   [19] 4.7 3.2 4.3 2.9 5.4 5.1 6.5 3.9 4.0 4.2 4.3 4.8 4.4 4.9 3.4 4.3 2.5 3.1
##   [37] 4.3 5.2 4.4 4.9 3.6 6.2 4.2 3.9 4.0 6.5 5.5 6.5 3.7 4.9 3.9 3.7 5.2 4.8
##   [55] 4.2 4.5 5.3 3.5 5.7 5.1 4.7 4.6 3.2 6.8 3.5 5.1 4.1 5.1 5.2 2.7 4.8 4.3
##   [73] 5.6 2.3 5.1 4.7 4.9 5.7 5.0 4.5 4.5 5.7 3.8 4.1 7.1 3.1 5.6 3.4 4.2 4.4
##   [91] 4.6 3.6 4.2 5.7 4.3 3.8 4.4 2.0 4.3 5.5 4.3 4.4 3.8 4.3 3.5 4.0 4.8 3.0
##  [109] 3.7 3.8 5.5 4.2 2.6 5.5 3.6 3.8 4.8 6.4 5.1 5.3 2.9 4.7 3.7 5.1 4.0 2.4
##  [127] 5.0 3.9 5.8 4.9 3.7 4.6 4.8 2.3 3.7 6.2 4.7 5.0 4.5 3.9 5.2 5.6 3.6 5.5
##  [145] 4.4 5.0 3.7 3.9 5.3 3.0 5.4 4.7 5.6 6.0 4.7 4.2 4.4 4.3 3.5 4.1 4.4 4.9
##  [163] 4.6 5.4 4.7 3.4 5.4 2.3 4.0 4.1 4.8 3.5 2.8 2.6 6.0 3.1 2.5 5.3 5.1 3.8
##  [181] 6.0 5.1 5.8 3.2 7.3 3.8 5.3 5.1 4.2 5.8 3.9 3.7 4.1 5.0 4.4 3.4 4.7 5.4
##  [199] 4.3 4.3 5.5 4.0 4.8 4.9 5.3 3.2 4.0 6.4 3.9 3.5 4.5 4.1 5.3 4.1 4.6 5.3
##  [217] 5.4 3.7 6.0 3.7 4.8 6.0 4.1 4.6 3.1 4.3 4.3 4.4 4.9 5.6 4.3 5.1 5.2 3.4
##  [235] 4.3 2.8 3.1 2.5 6.1 6.2 5.8 5.8 4.8 4.5 2.9 5.8 4.9 5.7 5.4 3.0 3.9 3.6
##  [253] 4.9 4.7 3.3 3.6 4.3 5.8 5.1 3.9 5.6 5.0 3.4 3.3 4.3 5.4 4.6 4.9 4.2 3.6
##  [271] 4.4 4.2 4.0 3.9 4.0 2.8 6.3 4.1 3.4 5.2 4.0 4.2 2.2 4.7 5.3 4.6 4.2 2.9
##  [289] 3.8 3.3 3.0 3.4 5.0 2.5 4.7 4.3 4.5 5.4 4.5 5.4 5.5 3.7 3.2 3.8 3.2 4.0
##  [307] 3.7 5.3 5.5 3.3 3.8 3.8 4.2 5.5 4.7 3.9 4.5 3.7 4.9 3.9 4.7 4.4 2.7 4.4
##  [325] 4.9 3.7 5.1 4.0 3.5 5.5 3.8 4.0 4.7 3.0 5.1 5.0 4.9 4.1 4.2 5.8 6.2 3.5
##  [343] 5.4 5.1 4.5 6.4 3.3 5.4 4.9 5.4 3.9 6.0 5.1 5.4 4.7 5.1 3.5 2.7 3.6 4.6
##  [361] 5.9 3.4 2.6 4.9 3.7 5.1 3.1 5.3 3.8 3.1 5.9 4.5 5.1 3.3 4.1 4.6 4.9 5.8
##  [379] 4.6 3.9 4.6 4.6 4.8 3.5 4.2 4.8 4.5 2.9 3.1 4.0 5.4 5.3 4.3 4.4 3.9 5.0
##  [397] 5.5 4.1 4.2 4.5 2.7 4.1 4.7 5.1 5.2 3.9 4.5 5.3 3.5 4.6 5.2 4.5 4.2 5.1
##  [415] 6.0 4.2 5.3 4.5 3.4 4.3 4.7 3.1 4.0 6.0 5.1 4.2 4.8 5.9 4.2 4.4 4.7 3.9
##  [433] 4.1 5.5 4.4 5.2 5.4 3.3 5.5 5.0 4.7 5.1 4.8 4.2 3.3 4.0 4.1 4.3 3.7 4.2
##  [451] 4.6 2.6 4.8 5.5 3.8 4.8 6.0 4.7 5.2 4.7 5.4 5.0 5.0 3.7 4.4 4.8 6.0 4.0
##  [469] 4.0 4.8 4.4 5.1 4.0 3.8 5.1 4.2 3.1 5.2 4.6 3.7 5.8 5.1 4.4 5.4 3.5 3.8
##  [487] 5.3 4.9 5.1 3.3 3.2 6.1 4.5 4.1 4.3 6.1 5.6 3.5 4.7 4.8 4.8 5.4 3.5 4.2
##  [505] 5.8 2.5 5.6 5.7 7.1 2.6 6.5 5.2 4.4 5.1 4.7 5.4 5.7 2.9 4.6 3.7 5.3 5.2
##  [523] 3.6 3.3 6.5 4.1 4.4 5.7 5.7 4.2 5.5 3.1 4.2 5.7 3.4 6.5 5.0 5.3 4.5 4.3
##  [541] 4.9 3.9 3.9 6.2 4.9 5.3 4.7 4.3 5.6 4.9 3.8 3.7 4.0 4.9 4.2 5.0 4.9 3.8
##  [559] 5.1 5.0 4.7 3.5 5.1 3.8 5.1 3.9 5.4 3.9 4.0 3.5 4.6 4.6 3.4 4.9 4.2 3.6
##  [577] 4.3 4.7 6.3 5.2 4.4 4.2 5.3 4.1 5.0 4.3 5.1 4.0 3.6 4.3 5.2 3.6 4.8 4.4
##  [595] 4.8 5.5 4.8 3.9 5.2 4.7 4.4 5.4 3.7 4.2 4.5 4.2 5.0 4.1 4.8 3.4 5.4 4.5
##  [613] 6.2 4.0 3.9 3.9 4.6 4.2 4.1 3.8 3.6 3.2 6.8 3.6 5.7 5.4 3.6 4.0 2.6 5.4
##  [631] 5.1 4.8 5.0 4.7 4.7 4.6 4.4 4.6 4.4 3.2 4.3 4.0 4.5 5.8 5.7 3.7 5.2 7.2
##  [649] 6.2 5.6 3.6 4.3 5.3 4.4 4.6 5.9 3.6 4.6 5.1 5.5 4.0 3.8 2.4 5.6 4.6 4.7
##  [667] 5.8 3.8 4.8 5.2 4.8 4.2 5.0 5.9 4.6 3.5 5.4 4.6 4.9 3.4 5.0 4.9 4.8 6.0
##  [685] 4.3 2.5 5.7 6.1 3.4 4.6 5.1 6.2 4.4 4.0 5.4 4.8 5.3 5.1 3.7 3.0 5.3 3.7
##  [703] 4.4 5.4 3.5 4.6 4.4 3.6 5.6 4.4 4.4 6.3 5.7 4.5 5.6 4.8 3.6 5.6 4.8 5.9
##  [721] 4.0 3.9 3.7 5.4 6.8 3.8 4.9 4.9 4.8 5.7 3.9 4.6 3.7 5.3 3.4 3.3 5.3 4.7
##  [739] 4.4 4.3 3.9 4.5 3.9 5.4 4.1 5.4 5.1 4.4 3.7 4.5 4.9 4.2 5.2 5.3 4.3 5.8
##  [757] 3.5 3.0 4.8 2.9 5.3 4.5 4.5 4.3 4.3 6.6 6.0 4.5 3.8 3.3 4.1 5.3 4.7 5.3
##  [775] 4.1 3.2 3.2 3.1 4.7 4.3 3.7 4.5 5.6 6.0 4.8 5.1 4.5 7.0 7.1 5.6 3.5 5.0
##  [793] 5.1 4.8 4.4 4.2 5.7 4.3 6.7 3.4 4.9 5.1 5.6 5.4 5.7 5.5 4.3 3.1 4.8 4.9
##  [811] 4.2 3.6 4.9 3.3 5.4 5.0 3.5 4.5 5.3 3.9 4.9 3.0 4.5 2.4 5.0 4.0 4.6 4.5
##  [829] 4.6 4.3 4.4 4.5 4.4 3.3 4.3 4.3 6.2 4.7 6.8 4.4 2.2 5.7 5.2 4.2 3.4 3.3
##  [847] 4.8 5.0 6.9 4.6 3.5 4.4 4.1 3.5 3.7 4.4 4.4 4.3 6.5 5.1 4.1 4.7 4.2 4.7
##  [865] 4.6 4.8 5.4 4.9 3.2 4.4 4.1 5.0 3.3 6.3 4.7 6.7 3.2 4.8 4.6 3.9 5.8 5.0
##  [883] 4.2 4.4 4.7 4.4 4.3 5.0 4.3 5.7 3.6 3.5 2.5 4.4 4.4 4.5 3.3 4.6 4.8 4.6
##  [901] 4.7 3.2 5.9 5.4 3.8 3.8 5.3 4.3 4.0 4.1 3.9 3.9 5.6 4.4 4.9 3.8 3.1 4.3
##  [919] 6.4 3.6 6.3 4.7 4.6 6.2 4.9 3.1 5.4 4.5 3.5 4.5 4.4 2.6 4.1 4.6 3.7 4.3
##  [937] 4.1 5.0 5.1 4.3 5.4 4.7 4.7 3.2 3.7 3.7 4.1 5.7 5.0 4.2 4.8 4.5 5.1 3.8
##  [955] 4.0 3.3 4.8 3.2 3.7 3.1 6.4 5.1 4.3 4.3 4.7 5.3 5.4 6.0 4.9 4.0 5.1 3.8
##  [973] 5.1 4.7 4.2 4.3 2.5 4.5 5.9 3.3 5.2 5.3 4.2 4.4 4.5 3.2 4.7 4.3 4.6 3.2
##  [991] 5.2 4.0 4.9 3.9 3.6 3.4 3.7 6.6 4.6 4.9
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
##   2.7   6.4
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
##    [1] 4.6 5.1 5.3 3.7 4.4 4.2 4.2 5.1 4.8 4.9 4.6 4.1 5.3 5.1 3.7 3.9 4.7 6.7
##   [19] 4.9 5.2 5.6 4.4 5.2 4.1 4.8 5.2 5.1 5.6 3.3 4.1 3.7 5.2 5.3 3.7 3.6 3.9
##   [37] 4.3 3.3 3.9 5.2 6.5 3.1 4.4 6.3 4.1 4.0 4.3 4.2 6.0 6.1 5.2 4.1 4.4 3.4
##   [55] 3.8 5.1 5.1 3.4 3.8 4.1 5.4 2.8 4.8 4.8 5.2 4.5 4.6 4.9 5.0 4.8 4.2 5.6
##   [73] 4.0 1.9 4.9 3.8 3.5 4.5 5.8 5.4 4.8 4.6 4.0 4.6 3.7 4.6 4.4 5.0 4.5 5.3
##   [91] 3.7 3.1 5.8 4.3 5.1 2.6 5.2 3.6 4.8 4.2 6.0 4.8 4.7 4.2 5.2 4.9 1.6 5.5
##  [109] 4.2 6.5 3.2 3.4 4.9 4.0 3.8 3.8 5.3 3.3 4.5 3.9 5.7 4.3 2.6 2.4 3.4 4.2
##  [127] 4.8 3.4 5.9 2.5 3.0 3.6 4.9 2.8 2.9 4.2 4.9 3.9 5.8 4.2 3.6 5.1 4.1 4.3
##  [145] 4.1 4.4 4.0 5.2 3.7 6.2 1.9 4.7 3.6 5.2 4.7 2.1 2.9 5.0 3.6 5.3 5.5 4.5
##  [163] 5.2 3.4 4.7 3.5 4.5 4.0 4.4 5.1 4.7 4.2 4.9 4.8 4.8 5.9 4.4 4.5 3.9 3.2
##  [181] 3.9 5.7 4.4 4.3 5.4 4.1 4.7 4.4 3.7 4.7 4.2 5.3 3.6 5.3 2.6 4.2 2.7 3.8
##  [199] 5.1 4.8 3.7 5.3 3.6 5.1 5.4 2.7 3.9 4.0 2.5 3.4 5.5 5.2 2.3 5.2 1.6 3.9
##  [217] 5.8 2.8 4.4 4.7 5.8 6.2 5.1 5.4 3.1 5.6 3.9 4.0 4.4 4.1 5.6 4.1 5.3 3.2
##  [235] 2.7 4.5 5.1 4.8 4.1 3.2 5.5 3.4 5.3 4.1 6.5 3.8 4.2 4.2 6.3 4.6 4.1 4.5
##  [253] 4.1 4.3 3.5 2.2 4.7 4.5 6.5 4.4 2.8 4.7 4.2 4.2 4.3 4.6 5.3 3.7 5.4 3.5
##  [271] 4.6 4.0 5.4 5.3 4.7 4.2 5.0 4.3 5.3 5.7 4.8 5.1 4.4 4.6 4.3 3.8 4.7 4.2
##  [289] 4.6 4.6 3.7 4.4 4.8 4.9 4.4 4.7 3.3 4.1 3.2 6.3 5.3 5.0 2.8 6.1 4.5 3.9
##  [307] 4.7 4.0 4.0 5.4 7.1 3.5 5.5 3.5 3.1 4.3 5.8 5.1 3.8 3.2 5.2 5.6 5.0 4.8
##  [325] 3.0 4.0 4.7 3.3 4.2 4.9 5.3 5.3 5.4 2.5 4.7 4.0 6.3 5.0 3.4 2.9 3.7 6.0
##  [343] 4.8 3.6 4.8 4.5 5.2 3.6 5.3 2.7 6.1 3.9 4.2 4.8 4.2 6.7 6.0 3.8 6.4 4.2
##  [361] 3.3 4.9 5.4 4.8 4.2 3.3 5.4 3.4 5.3 5.7 6.3 4.8 3.9 6.5 5.4 4.6 4.0 2.6
##  [379] 5.0 3.6 3.3 4.2 3.5 4.3 4.4 5.0 5.3 3.0 5.7 5.6 4.1 4.6 3.3 5.0 4.7 3.8
##  [397] 4.7 3.3 3.4 4.9 5.7 3.6 4.6 4.0 2.9 5.0 4.1 4.3 3.7 4.7 4.4 6.3 4.7 4.0
##  [415] 4.7 3.0 5.8 3.8 3.0 4.8 3.8 3.4 4.7 6.3 5.5 4.8 5.9 6.0 4.2 4.4 3.4 3.4
##  [433] 4.4 4.2 4.9 3.8 4.3 5.1 4.5 4.3 6.1 4.0 5.4 6.2 4.4 5.1 4.8 6.1 4.4 3.7
##  [451] 6.0 5.3 3.6 3.4 5.5 4.3 3.7 4.9 4.7 5.1 4.9 5.4 3.5 6.0 4.0 3.7 3.4 5.3
##  [469] 3.5 3.4 4.8 4.2 5.6 4.4 2.9 5.2 4.4 3.9 3.2 4.7 5.2 3.1 4.6 4.6 4.9 5.0
##  [487] 5.8 3.9 4.9 3.4 4.5 4.7 4.9 5.3 3.6 5.6 4.3 5.2 4.2 5.6 3.0 5.1 5.2 3.6
##  [505] 6.4 6.9 2.7 5.2 3.9 3.2 2.4 4.9 3.3 4.1 4.0 4.5 4.5 3.9 3.4 4.7 3.6 5.5
##  [523] 4.7 4.5 3.5 3.4 5.1 3.8 5.8 6.3 2.6 4.4 5.0 4.0 3.4 4.0 4.9 4.9 3.9 4.8
##  [541] 4.6 4.9 4.0 3.9 5.9 4.3 3.5 4.1 2.7 4.9 4.2 6.5 5.1 3.2 5.6 6.7 5.7 3.9
##  [559] 5.0 3.6 5.3 3.4 4.5 2.9 5.2 3.9 4.8 4.2 3.5 4.3 5.2 5.2 5.0 3.0 4.1 5.3
##  [577] 4.8 3.0 4.1 4.3 3.1 3.6 3.9 5.0 1.8 4.8 3.1 6.1 5.7 5.3 3.2 4.3 4.5 3.7
##  [595] 4.9 2.5 5.5 6.7 4.9 5.0 5.4 2.6 3.2 3.8 4.9 3.3 5.6 5.0 3.9 3.8 2.9 4.3
##  [613] 4.6 5.5 2.6 4.5 3.7 5.4 7.0 4.8 4.6 4.6 3.5 3.7 4.7 5.8 3.6 4.4 4.1 5.2
##  [631] 4.2 3.3 4.6 5.1 4.3 4.7 3.9 3.6 2.9 4.5 3.6 4.5 3.8 4.7 3.7 6.0 5.1 4.9
##  [649] 5.1 4.6 3.9 4.3 4.5 5.8 3.3 5.3 3.3 5.5 4.7 4.1 3.0 3.5 5.5 4.6 4.5 6.4
##  [667] 4.1 4.5 5.8 5.5 3.6 3.4 2.7 4.6 4.4 3.8 5.1 4.0 3.3 2.5 6.4 4.3 4.9 4.9
##  [685] 5.5 4.2 3.6 6.3 3.9 5.0 4.2 6.1 4.1 5.4 5.6 4.0 5.3 4.8 3.7 4.1 5.8 5.5
##  [703] 3.5 5.1 4.2 4.0 5.0 4.1 3.5 5.3 5.6 5.4 3.9 4.1 4.2 3.5 3.4 3.9 4.3 4.2
##  [721] 4.8 4.6 5.6 4.6 5.3 5.3 3.9 5.2 4.5 5.7 4.5 3.9 3.0 3.5 5.2 6.5 5.1 5.5
##  [739] 6.1 5.4 4.4 4.2 4.1 3.0 2.5 4.5 5.1 3.6 5.8 5.7 2.8 4.2 2.0 5.7 4.5 3.8
##  [757] 5.3 5.0 4.7 4.0 4.7 5.9 5.1 3.9 5.4 4.4 4.7 2.6 3.3 4.5 3.7 4.4 4.6 4.3
##  [775] 3.8 5.5 4.5 5.4 5.6 3.6 4.4 4.6 4.3 5.9 3.7 5.4 5.4 4.6 3.8 5.4 5.0 5.0
##  [793] 5.3 4.3 4.0 4.7 4.0 4.1 6.5 4.5 4.7 5.6 5.3 5.5 4.6 4.3 4.1 4.6 4.5 5.3
##  [811] 5.0 4.7 5.8 4.1 5.5 4.4 3.9 4.1 4.2 5.1 4.9 3.9 5.3 5.6 5.1 4.7 3.7 4.1
##  [829] 4.5 5.5 4.5 3.3 5.7 4.1 4.0 4.6 3.2 5.9 4.9 4.2 5.9 3.3 4.7 5.3 5.0 4.3
##  [847] 5.4 5.1 3.3 2.4 2.2 5.4 4.2 5.2 4.1 5.6 4.9 3.8 3.3 6.5 3.7 5.6 4.3 5.1
##  [865] 4.2 5.0 4.3 6.0 5.4 4.3 3.2 5.3 4.7 3.2 4.0 4.2 5.6 5.4 4.0 4.7 5.1 4.1
##  [883] 4.1 5.5 3.8 4.3 6.9 4.2 4.5 4.5 3.7 4.9 3.5 5.9 5.7 2.9 5.0 3.8 4.1 4.9
##  [901] 4.7 4.3 4.0 3.9 3.9 3.6 4.1 3.2 4.8 5.3 4.5 4.4 5.8 4.1 3.6 2.3 5.7 4.0
##  [919] 4.0 5.5 4.2 4.4 5.1 4.6 3.9 4.0 3.1 5.8 5.1 4.3 3.3 3.0 3.4 4.2 4.8 3.2
##  [937] 5.4 3.9 3.8 4.0 5.0 4.4 4.4 4.2 4.1 3.5 4.2 5.3 4.6 6.0 5.2 3.6 3.2 4.2
##  [955] 4.3 4.5 5.7 4.1 4.8 3.7 4.7 3.3 5.0 4.3 4.8 5.4 4.1 4.6 5.1 4.3 4.6 4.7
##  [973] 3.8 5.0 6.1 2.8 4.5 2.8 3.9 3.6 3.6 3.0 5.6 4.4 3.9 4.7 5.9 4.7 4.5 2.9
##  [991] 6.3 5.7 4.9 5.4 6.3 4.8 4.1 5.2 4.2 4.8
## 
## $func.thetastar
## [1] -0.0284
## 
## $jack.boot.val
##  [1]  0.534069401  0.443712575  0.232011331  0.121556886 -0.001719198
##  [6] -0.121635884 -0.167428571 -0.252840909 -0.454032258 -0.591591592
## 
## $jack.boot.se
## [1] 1.042236
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
##    [1] 5.3 5.8 5.2 5.1 5.7 5.3 2.8 3.8 5.4 4.7 5.8 6.4 4.2 5.0 3.7 4.0 4.7 3.9
##   [19] 3.2 4.4 4.3 4.0 5.1 5.1 5.0 5.1 4.3 3.9 4.5 4.8 4.6 3.0 2.5 5.4 3.5 5.0
##   [37] 5.4 4.6 5.2 4.4 5.8 3.7 5.2 6.1 3.6 4.1 4.6 4.9 3.4 4.8 4.6 4.6 3.8 5.6
##   [55] 3.3 5.3 2.9 4.0 3.7 4.0 6.4 4.0 5.3 5.8 5.0 5.1 3.8 5.1 3.2 4.4 4.3 5.6
##   [73] 3.6 4.3 5.7 2.6 3.5 4.3 7.1 5.0 5.1 3.9 5.7 3.8 2.0 4.2 4.1 4.6 4.3 2.9
##   [91] 2.8 3.5 4.3 3.3 4.5 6.8 3.6 3.8 5.0 3.6 4.3 2.8 5.0 4.4 4.2 4.4 4.6 3.4
##  [109] 3.9 4.3 5.7 5.4 4.9 3.8 3.9 5.1 6.2 3.8 3.6 4.4 5.5 4.1 3.9 3.2 4.3 3.2
##  [127] 6.3 4.4 3.4 3.9 5.5 5.7 3.3 4.0 5.0 4.3 3.9 3.9 5.5 5.8 1.9 3.2 3.6 3.9
##  [145] 4.9 3.5 4.6 3.9 3.2 4.7 3.3 3.2 4.0 5.1 4.8 3.3 3.1 4.7 5.0 4.9 3.2 5.8
##  [163] 2.9 6.4 5.1 5.6 2.7 3.4 4.3 4.3 5.1 4.1 5.6 4.5 5.3 4.2 4.6 3.8 4.4 3.0
##  [181] 4.3 6.4 5.2 5.8 4.7 5.0 5.2 5.1 3.4 4.8 3.3 4.9 3.1 4.1 2.9 4.7 5.1 3.2
##  [199] 4.3 4.5 4.4 4.7 5.3 5.1 5.4 4.7 5.9 4.2 3.5 2.4 4.0 4.6 3.8 5.4 3.9 4.4
##  [217] 5.2 4.3 3.9 4.9 3.4 5.7 4.5 2.8 3.3 4.7 5.6 4.4 4.2 3.5 5.7 4.2 3.4 3.5
##  [235] 6.0 3.8 2.9 3.6 4.6 3.8 3.4 5.0 5.5 6.2 3.0 3.7 5.7 3.6 4.7 5.7 3.4 4.9
##  [253] 4.5 4.1 3.7 5.6 3.6 4.8 5.0 4.6 5.7 6.3 4.1 4.5 4.1 5.0 4.5 4.5 4.5 4.3
##  [271] 4.8 5.7 4.8 4.3 4.8 5.8 3.6 4.2 4.4 5.4 5.7 3.2 4.6 3.9 5.1 3.3 3.7 4.3
##  [289] 4.5 4.6 5.7 5.0 3.8 3.8 3.3 6.3 4.6 5.0 5.6 4.1 3.1 4.5 5.7 2.8 4.4 4.6
##  [307] 3.9 5.8 5.5 4.1 2.9 6.2 4.1 3.7 5.1 5.6 2.0 3.2 5.1 3.7 5.8 4.3 3.4 2.8
##  [325] 5.6 5.3 3.9 3.5 5.5 4.1 4.4 5.2 5.2 5.3 3.7 3.8 6.4 5.6 5.3 4.2 3.2 4.8
##  [343] 4.0 3.2 4.7 4.8 3.8 4.9 3.2 5.0 4.0 5.8 3.8 6.9 5.2 3.4 4.8 5.0 5.4 6.6
##  [361] 5.6 4.4 4.6 4.3 4.5 3.9 3.1 3.4 5.3 3.0 4.1 4.1 4.7 3.2 5.7 3.2 4.1 5.7
##  [379] 4.0 6.0 6.2 4.6 4.3 4.4 4.8 3.9 5.1 5.9 5.3 4.7 3.0 5.6 3.5 5.7 4.4 5.4
##  [397] 4.8 3.9 4.4 5.4 2.9 5.2 4.0 3.9 4.2 5.0 3.6 4.9 3.5 6.6 5.4 5.2 3.9 5.6
##  [415] 4.7 4.8 4.0 4.1 4.8 5.2 4.7 4.3 3.5 5.8 6.2 4.8 4.7 3.8 4.8 5.2 3.6 7.3
##  [433] 5.2 3.1 4.2 3.7 4.0 4.4 4.1 6.3 4.4 3.4 3.5 4.6 3.4 3.6 5.9 5.7 4.0 4.7
##  [451] 4.8 3.5 4.3 4.4 3.0 3.7 4.5 5.0 4.8 4.1 5.6 5.5 3.9 5.3 5.5 4.7 4.7 4.2
##  [469] 3.0 6.2 4.7 7.1 5.1 5.0 4.9 4.6 5.1 3.1 4.2 4.6 5.2 4.8 3.9 4.3 5.1 5.2
##  [487] 2.9 3.3 3.3 4.5 5.4 4.5 3.3 5.2 3.9 4.0 5.1 5.4 4.3 4.9 4.8 4.2 2.8 4.1
##  [505] 3.3 3.9 3.9 4.5 6.1 3.9 5.2 3.3 3.9 3.6 4.4 4.6 4.1 5.6 3.1 3.8 4.9 3.3
##  [523] 5.2 4.0 5.9 5.2 2.9 4.0 4.2 4.0 4.9 4.5 4.2 4.6 3.4 4.4 3.8 4.8 4.9 3.6
##  [541] 4.6 3.3 5.6 6.1 4.2 4.4 5.3 4.3 4.7 5.6 3.7 5.8 5.9 4.7 5.4 3.2 3.5 4.7
##  [559] 4.7 4.5 4.7 5.0 4.8 5.7 4.9 4.0 5.5 2.7 4.0 6.0 5.4 4.0 3.5 4.3 6.3 4.9
##  [577] 3.7 4.8 5.7 4.0 4.5 3.6 4.6 4.8 4.2 5.0 3.9 2.9 5.0 6.2 5.2 6.2 4.6 3.5
##  [595] 6.8 4.4 4.4 5.3 2.5 5.0 5.4 6.2 4.4 5.3 5.2 4.4 2.3 4.3 5.2 4.7 4.2 5.4
##  [613] 3.7 2.9 3.9 3.5 3.9 5.9 3.9 4.6 4.5 5.0 3.9 4.2 3.9 3.6 5.2 4.8 3.7 4.1
##  [631] 5.0 6.3 6.0 5.4 5.3 3.7 4.1 2.7 2.5 5.9 3.7 4.6 6.8 5.2 4.7 3.1 6.2 5.0
##  [649] 4.0 4.2 3.7 3.6 3.6 3.7 4.8 2.6 5.2 3.9 2.4 5.6 3.4 4.3 4.1 4.8 4.0 3.9
##  [667] 6.0 4.4 5.0 5.1 5.3 3.6 4.1 3.6 6.2 2.2 4.2 5.1 4.1 5.2 4.0 5.4 3.7 4.1
##  [685] 4.1 3.9 3.9 5.2 5.0 3.9 4.6 3.2 3.6 3.6 5.0 4.6 5.3 3.4 3.3 3.8 3.9 3.3
##  [703] 3.7 4.6 5.3 4.5 3.7 4.6 4.7 3.9 5.0 2.3 5.1 2.8 6.5 4.7 5.2 4.8 4.2 6.6
##  [721] 3.6 5.3 4.5 4.7 4.1 3.3 2.4 4.3 4.3 5.2 4.2 4.3 4.7 4.8 5.8 4.1 5.2 4.2
##  [739] 6.0 4.2 5.8 3.4 4.1 5.2 3.2 4.9 4.3 5.1 3.1 5.0 5.4 4.2 3.6 4.7 5.0 3.6
##  [757] 4.7 5.8 4.0 4.2 5.4 4.8 5.0 4.1 3.2 5.1 4.3 4.1 4.7 3.9 5.4 4.7 5.1 3.8
##  [775] 4.7 4.5 5.8 5.1 4.8 3.2 4.0 4.5 4.0 4.9 5.4 2.4 4.4 5.4 5.3 4.8 4.1 5.4
##  [793] 5.7 4.4 4.0 3.4 4.6 4.4 4.2 5.0 4.4 3.8 4.1 4.1 3.7 4.9 5.4 5.0 4.9 5.6
##  [811] 4.2 4.5 5.8 4.6 4.9 3.6 3.3 5.1 4.4 5.0 4.3 5.4 4.5 4.9 4.5 3.0 4.8 5.3
##  [829] 3.2 4.4 6.1 4.3 5.6 4.4 3.6 4.2 4.0 4.1 3.3 5.2 5.5 5.9 3.8 6.3 5.6 5.3
##  [847] 4.9 5.2 4.1 5.1 4.5 5.1 3.9 3.8 3.7 4.5 6.1 3.9 5.4 4.2 3.3 4.1 5.2 4.2
##  [865] 4.3 6.4 3.6 2.6 4.7 3.2 3.4 5.0 2.1 4.9 4.6 4.0 4.9 8.2 5.9 6.8 5.6 2.9
##  [883] 3.0 4.4 4.6 4.1 4.5 4.1 3.4 3.9 6.2 4.1 4.4 4.8 4.2 3.8 6.7 4.4 5.2 4.1
##  [901] 5.4 5.4 4.6 4.0 5.8 3.2 5.0 4.3 3.6 4.5 2.8 4.9 3.1 4.3 3.5 3.1 4.6 5.1
##  [919] 4.4 5.7 4.9 4.6 5.6 4.1 5.1 3.0 3.3 4.1 3.8 3.4 4.9 4.9 5.0 2.1 5.0 6.0
##  [937] 5.9 3.2 4.4 6.2 4.7 4.3 4.6 5.6 4.4 4.0 4.1 4.7 6.0 3.5 4.3 4.6 3.6 3.5
##  [955] 4.9 4.1 4.4 4.1 3.7 3.5 3.6 2.8 4.5 4.0 4.1 4.3 4.3 4.5 4.0 5.5 4.1 4.6
##  [973] 5.3 4.9 5.3 5.8 5.5 5.2 6.6 4.2 4.5 4.2 3.0 2.9 4.2 3.9 5.0 4.3 4.6 4.2
##  [991] 5.6 5.6 4.1 5.0 6.0 4.2 4.5 4.3 5.4 4.1
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.600 5.400 5.292 5.200 5.256 5.000 4.900 4.628 4.500 4.400
## 
## $jack.boot.se
## [1] 1.148685
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
## [1] 0.8290768
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##     shape       rate  
##   3.796760   7.956772 
##  (1.628872) (3.649663)
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
## [1]  0.49898571 -0.56869553  0.52529602 -0.05042822  0.66838666 -0.09355234
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
##    [1]  0.834098559  0.126728682  1.143113070  0.804579133  1.108325108
##    [6]  0.347624785  0.463543905  0.383129625  0.846499789  0.380177576
##   [11]  0.571459872  0.334710777  0.982415268  0.768309362  1.001540889
##   [16]  1.087559258  0.372797322  0.312635245  0.291023672  0.113734870
##   [21]  0.586481527  1.060592034 -0.051072180  1.327695941  0.464047443
##   [26]  0.413179218  0.460610669  1.054377736  0.623985950  0.079349133
##   [31]  0.891232284  0.816526193  1.278973732  0.802833576  0.813922883
##   [36]  1.153110693  0.727837773  0.460610669  0.532822131  0.627619390
##   [41]  0.807868745  0.536320955  1.184771614  0.335295164  1.658794044
##   [46]  0.612815523  0.569401179  1.144044847  0.064994361  0.657477696
##   [51]  1.077596273 -0.034605894  1.539307839  0.724395480  0.735624626
##   [56]  0.544778249  1.013105680  0.629550034  0.389404008  0.399485942
##   [61]  2.525596900  0.127039134  0.926310467  0.345219352  1.902476929
##   [66]  1.279301710  0.598791315  0.809870363  0.458418716  0.589308168
##   [71]  1.487212691  0.510418749  1.291595753  0.549725916  1.544800764
##   [76]  1.347201952  1.318143126  0.014515349  0.804646680  0.980724501
##   [81]  0.083178563  1.311071961  1.392130729  0.225588683  0.271930471
##   [86]  1.050760436 -0.254248175  0.247135959 -0.073273861 -0.306438892
##   [91]  1.221358593  1.780834742  1.040724385  0.905778419  0.108532351
##   [96]  2.390233751  0.976814390  1.370666843  0.512722269  0.214713114
##  [101]  0.415861841  1.605676253  1.007037548  0.950030287  0.194972434
##  [106]  0.585122872  0.503453768  0.547475245  0.841537146  0.688864821
##  [111]  0.399557520  0.187482140  0.643446335  0.658263568  0.911618222
##  [116]  0.537470969  0.550194645  0.953413978  0.615517598  0.108209509
##  [121]  0.840970364  0.812080517  0.820483386  1.671352999  1.097186620
##  [126]  0.902122973  0.684631553  0.597370204  1.547250293  0.874057453
##  [131]  0.697548621  0.499691998  0.824023384  0.834563738  0.701391967
##  [136]  0.728245297  1.299592421  0.748302114  0.410406187  0.302620305
##  [141]  0.235592368 -0.132165468  0.559538776  0.747249091  1.172228711
##  [146]  0.457483837  0.606862081  0.589871556  1.300991717  0.694549536
##  [151]  1.271963420  0.226579968  1.099891616  1.142889444  0.280561929
##  [156]  0.924697657 -0.211650586  0.891733144  0.929331760  0.382391344
##  [161]  0.520770715  2.131023112  0.881641029  0.019291629  0.125558779
##  [166]  1.958111601  0.358003873  0.377222180  1.100596726  0.701943085
##  [171]  1.564911802  0.253979274  0.064063882  0.406149234  0.671615693
##  [176] -0.059419730  1.647737231  0.280362744  0.521868147 -0.044700033
##  [181]  0.528539866  0.279140611  0.597165393  1.025285654  0.884509377
##  [186]  0.342785676  0.465781315  0.064044427  1.291764551 -0.171798132
##  [191]  0.864978167  0.269462006  0.347203762  0.629614238  1.214550311
##  [196]  0.790811973  1.081241640  0.948237537  1.019462637 -0.072168229
##  [201]  0.159794258  0.411787504  0.910063287  0.585661974  0.389315953
##  [206]  0.136828435  0.102012174  0.464983199  0.565238541  1.178372347
##  [211]  0.682221391  0.934024980  1.122840746  1.079043242 -0.062596311
##  [216]  0.898074019  0.750379570  1.060592034  1.292035546  0.311522764
##  [221]  1.048400781  0.439313139  1.200225369  0.777081366 -0.159111854
##  [226]  2.288465920  1.481951446  1.578719180  1.745256778  1.139943201
##  [231]  1.542983771  0.452802711  0.376267274  1.478950418  0.863707922
##  [236]  1.098682264  0.940449065  0.747883770  0.045319299  0.606959446
##  [241]  1.269285330  0.623385781  0.759856208  0.863808769  1.163008586
##  [246]  1.256152860  0.975156967  0.740092913 -0.082525317  0.924114759
##  [251]  0.679319448  0.725852855  2.586872843  1.131204379  0.901011658
##  [256]  0.833624518  1.024789102  0.521903331  1.378915670  1.626150084
##  [261]  0.958822445  0.568380967  0.970119528  0.978025693  0.477623484
##  [266] -0.066726615  0.467830171  2.306830539  1.519592038  0.844154618
##  [271]  0.831114459  0.445070803  1.170555442  0.820483386  0.605196126
##  [276]  0.764508271  1.549245476  0.782229367  0.521578915  1.124769719
##  [281]  1.085570205  0.912607151  1.980834795  0.516917627  1.076020063
##  [286]  0.633140777  0.845463183  0.924188912  2.551813794  0.758041284
##  [291]  0.515962858  0.850864441  2.376081909  1.235465974  1.527558482
##  [296]  1.050830450  0.386994611  2.129584819  0.599055055  0.664853319
##  [301]  1.115423181  1.590995247  0.212962498  0.906643661  1.209978748
##  [306]  0.439331281  2.131934505  0.953290503  1.635818919  1.524264814
##  [311]  1.534076300  1.010722855  1.196555970  1.395392629  0.729472323
##  [316]  0.021351730  0.230803606  0.316863430  0.936919428  0.766710837
##  [321]  0.598264592  0.542793864  0.435672290  0.713270150  1.465623767
##  [326]  0.724210743  0.611054327  0.568974629  0.920502678  0.596099897
##  [331]  1.186983878  0.469971054  0.292877433  0.972266335 -0.060956731
##  [336]  0.622894196  1.314330352  0.122084768  2.006377228  0.771961254
##  [341]  2.225390024  0.315743062  0.494688564  0.344465615  1.794775452
##  [346]  1.356714398  0.525188745  1.424545066  0.136168314  0.795134881
##  [351]  0.944319286  0.900678197  0.672635361  0.504046562  0.777978114
##  [356]  0.548708144  0.136434862  0.145956554  0.468433341  0.338564570
##  [361]  1.036462122  0.108137535  1.023110406  0.681216996  0.617287344
##  [366]  0.713211408  1.498946104  0.248992085  2.511896157  0.598963916
##  [371] -0.108520851  1.087663340  0.076944885  0.436773227  0.032180738
##  [376]  0.936154127  0.463281611  0.578288908  0.109515293  0.552557596
##  [381]  0.936791809 -0.538825741  0.948391977  0.197012306  1.210034056
##  [386]  1.389801302  0.602300845  1.901581190  1.005114137  0.021679537
##  [391]  0.769471324  0.276671598  0.923591754 -0.125020269  0.611895085
##  [396]  1.086322404  0.912829728  0.306539677  1.186465315 -0.058589450
##  [401]  0.076363795  0.673624977  0.831123552  0.717409621  0.677527618
##  [406]  0.010312164  1.555794620  1.307817023  0.276867770  0.027707010
##  [411]  0.307815325 -0.368963324  0.558964924  0.214547493  1.202921405
##  [416]  0.926214060  0.018768602  1.396196867  0.459862442  0.662415341
##  [421]  1.327402762  0.858879804  1.531603775  0.203256230  0.233606817
##  [426]  2.179796576  0.593829699  0.521497327 -0.279341419  0.154270847
##  [431]  1.807643487  0.440870603  0.622094773  0.458373623  0.298933477
##  [436]  0.763478149  0.532772235  0.645420718  1.909594604  0.029335934
##  [441]  1.111967488  0.082879081  1.041244479  0.722319134  0.679303708
##  [446]  0.227254359  1.308873054 -0.728816321  1.189211510  0.534457753
##  [451]  0.468150181  2.288989011  0.520078486  0.539055103  0.602105597
##  [456]  1.419169099  0.310283952  0.516760814  0.834563738  0.549414829
##  [461]  1.016139163  0.355131120  0.039361407  0.205000814  0.065718861
##  [466]  1.606162040  0.860960071  0.360640729  0.564470245  1.540227439
##  [471]  0.927033614  0.751128856  1.295992849  0.841674393  1.185567014
##  [476]  1.495254119  0.045889043  1.007459744  0.435225945  0.689820237
##  [481]  0.435431442  0.346829518  0.431396438  1.340528478  0.868684688
##  [486]  0.902315678  0.630892227  0.203069513  1.346493153  0.664133642
##  [491]  0.254207432  0.887252553  0.289158404  0.727357118  0.949876925
##  [496]  0.070424410  0.609642417  0.837640572  0.658955351  0.387741437
##  [501]  1.397663981  0.151533789  0.372349075  0.458187241  0.344147401
##  [506] -0.032231912  2.160567043  0.244438604  1.299592421  1.048289068
##  [511]  0.828194017  0.944779319  0.477833397  0.515909802  0.820428857
##  [516]  0.782182914  0.230802916  1.383384895  0.933879010  0.352284043
##  [521]  1.353090071 -0.227806208  0.491248109  0.777402886  0.296711785
##  [526]  1.379357324  0.513628712  0.953432549 -0.210023828  0.306604828
##  [531]  0.462198118  0.538509871  0.625660100  1.637254197  0.889855435
##  [536]  0.498234112  1.075067832 -0.218118815  1.643978300  1.766188345
##  [541]  0.380773794  0.439007197  1.356524187  1.496844875 -0.550124354
##  [546]  1.003310128  0.865143834 -0.114356898  0.604523499  1.054412854
##  [551] -0.306350862  1.573374302  0.638617796  0.763231561  1.235895408
##  [556]  0.376484190  0.705037388  0.853968937  1.362814466  1.924007486
##  [561]  0.484315088 -0.050578537  1.570310642  1.413016888  1.471796155
##  [566]  1.391383128  1.894793892 -0.090980070  0.175255940  0.781999227
##  [571]  0.892472489  0.696983640  0.171352441  0.693979557  1.184783451
##  [576] -0.008812059  0.704855424  0.211086699  1.118008165  0.443438954
##  [581]  0.729699268 -0.038059840  1.135346698  1.412517715  0.168058266
##  [586]  0.313048257 -0.372614965  0.982914037  0.782023398  1.015486798
##  [591]  1.003564057  0.126370349  1.185060213  0.007147919 -0.081458859
##  [596]  1.136440481  0.288250552  0.920019680  0.439007197  0.936996671
##  [601]  0.805027644  0.733999404  0.798049546  0.593509734  0.113356113
##  [606]  1.557362522  0.736252089  1.286940971  0.819491272  0.097105303
##  [611]  0.511335080  1.099891616  0.532724938  0.551017583  0.664208721
##  [616]  0.796870174  0.626995297  0.473108391  0.313333253  0.540869479
##  [621]  1.213769593  0.166302174  0.953952495  0.823433893  0.650320455
##  [626]  0.392772583  0.250245239  0.965172134  0.704925667 -0.383749841
##  [631]  0.152149748  0.516344561  0.401633010  1.315478438  2.113658936
##  [636]  0.280368555  0.784334762  0.205572596  0.323340652  0.984870493
##  [641]  0.815665218  0.295427245  0.392501097  0.755131433  0.073853150
##  [646]  0.485266694  0.258999999  0.033527293  0.345573920  1.952724348
##  [651]  0.475213284  0.467555701  1.184279266  0.822888276  1.494482910
##  [656]  0.989084928  0.936683285  0.520986796  1.159989900  1.906166629
##  [661]  0.508200161  0.847937709  1.864466840 -0.193846216  1.066474471
##  [666]  1.067597906  0.982759631  0.423436008  2.110106787  1.102176197
##  [671]  0.983716416  0.628297022  0.443645009  0.722064658  0.769863249
##  [676]  0.722697054  1.224473617  2.016986147  0.613840530  0.295342495
##  [681]  0.192890456  0.438968428  1.236462880  0.983452390  1.311621996
##  [686]  1.515631851  0.444968074  0.467306541  0.877731437  1.009122205
##  [691]  0.107287394  1.452117586  0.583996782  0.623269054  0.681619647
##  [696]  0.479104991  0.433971846  1.141735608  0.642874407  1.003612293
##  [701]  1.141060460  0.122631451  0.861717468  0.575543429  0.638932263
##  [706]  0.498693964 -0.277802314  1.202231353  1.101089396  0.383607796
##  [711]  0.824474559  1.282782560  0.985979502 -0.276219686  0.711409303
##  [716]  1.036364818  1.174610755  1.012981043  0.011494782  0.919726281
##  [721]  0.527523548  0.272798156  0.430919276  0.980679973  0.302613708
##  [726]  0.471912561  1.272987854  1.124583690  0.690776548  0.707563101
##  [731]  1.156377339  1.343791381  0.803939472 -0.234193290  0.757017486
##  [736]  0.634537334  1.644657936  1.162656918  0.215627367  0.194859991
##  [741]  1.219545946  0.485173834  0.669495298  0.644921076 -0.111253085
##  [746]  0.791341695  1.807727521  1.212689166  1.269868684 -0.052568611
##  [751]  0.667805829  1.748664049  1.378808768  0.309711836  0.441933359
##  [756]  0.529060771  0.116278854  0.891062085  0.370764652  1.067043678
##  [761]  0.376948349  1.163090249 -0.143002622  0.463156366  1.875077875
##  [766] -0.321449075  0.536428303  0.514459854  0.073488856  1.507502833
##  [771]  1.233441901  0.345331321  1.350249771  0.824546121  0.542610711
##  [776]  0.934685901  0.473842088  1.643978300  0.560230886  0.388038826
##  [781]  0.521122030  0.295079330  0.526101468 -0.242764529  0.389596106
##  [786]  0.660747556  0.428855964  1.952849999  0.438687032  1.677422356
##  [791]  1.360834000  0.491507801  1.184332307  1.553482722  0.500947721
##  [796]  0.900565990  1.119967047  2.126456113 -0.113675709  1.098894525
##  [801]  0.585090649  1.546982879  0.770142571  1.011833053  1.243058053
##  [806] -0.034490818  1.010404785  1.005575123  0.832760733  1.310474507
##  [811]  0.767766460  0.206746397  1.042656249  0.047679860  0.585169336
##  [816]  1.002063246  0.917007206  1.434316349  2.180494392  0.526284133
##  [821]  0.394905101  1.140281586  0.443577837  1.212689166  0.853731566
##  [826]  0.184756208  0.298566223  0.977073487  0.721646919  0.320924410
##  [831]  0.471285415  1.164855930  0.072766636  1.109848957  0.761032257
##  [836]  0.927656861  0.756121631  0.618013041  0.436850438  1.387854211
##  [841]  1.018687117 -0.586744690  0.437297628  1.217927251  1.176292956
##  [846] -0.192442626  0.898362496  2.555650798  0.605863898  1.080633736
##  [851]  0.655501939  1.062091117  0.412883495  0.936891346  1.833657069
##  [856]  1.208892787  0.682221391  1.232843919  0.037706188  0.807817447
##  [861]  1.933234071  0.750535352  1.327438676  0.541650666 -0.458935768
##  [866]  0.083757559  1.591423678  1.176909877  0.267315939  0.044307510
##  [871]  1.455105983  1.264048176  0.265472499  1.207321775  1.284349186
##  [876]  1.364570004  0.935386180  0.588026871 -0.137034602  0.186409374
##  [881] -0.605064866  0.321918278  0.351740666  1.290582796  1.530245502
##  [886]  0.077213071  0.537123775  0.080777689  0.638791701  1.038328572
##  [891]  1.175901432  2.194359853  0.452842082  1.291071403 -0.020941411
##  [896]  0.809517833  0.432948690  0.523497532  0.599944552  1.544647172
##  [901]  0.658457934  0.832958790  1.063935529  0.025383422  0.718683819
##  [906]  0.124216365  0.848511734  0.562830571  0.922537710  1.222054912
##  [911]  0.453555747  0.649367462  0.491248109  1.097537745 -0.032336030
##  [916]  0.774155322  0.701845339  1.626522647  0.373797290  0.442010867
##  [921]  1.334396974  0.590983087  0.446496595  0.447766996  0.492197097
##  [926]  0.901347954  0.379809686  1.443695359  0.427819337  0.023853933
##  [931]  0.093202564  1.009880503  0.997037385  1.223672357  0.463879440
##  [936]  0.623231919  0.171700763  0.011362104  1.975214938  1.081101820
##  [941]  0.548034798  1.346229024  0.201082183 -0.080916345 -0.162553620
##  [946]  0.350592789  0.558009116  0.491507801  0.273270880  1.364697950
##  [951]  0.917073551  0.484103808  0.438939320  0.501606261  0.831988016
##  [956] -0.739910118  1.397663981  0.629060264  0.486687702  1.355104762
##  [961] -0.065119933  0.315439071  0.072187349  0.591820153  0.652638332
##  [966]  0.733670134  0.438191336  1.200603736 -0.094811875  0.201455358
##  [971]  0.867113898  0.885125028  1.388011656 -0.420060651  0.327405799
##  [976]  1.057551505 -0.033898138  1.236152078  0.445175193  1.438435501
##  [981]  0.354837492  1.755775123  1.284992923  0.611322890  1.487560607
##  [986]  1.053209436  0.877601305  0.818794836  0.959418856  0.836972893
##  [991]  0.501268396  0.475836943  0.469040108  1.429230534  1.661578196
##  [996]  0.840109947  1.067597906  0.611347973  1.384253847  0.525045577
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.47717624   0.25897112 
##  (0.08189386) (0.05790307)
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
## [1] -0.6951762 -0.8849272  1.4665180  0.3752777  0.2094954  0.7022194
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
## [1] 0.0141
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9280908
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
## t1*      4.5 0.005705706   0.8978361
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 4 6 8 
## 2 1 3 1 2 1
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
## [1] 0.0094
```

```r
se.boot
```

```
## [1] 0.8970264
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

