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
## 2 3 5 6 7 
## 4 2 2 1 1
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
## [1] -0.0408
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
## [1] 2.650972
```

```r
UL.boot
```

```
## [1] 6.267428
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.3
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
##    [1] 5.1 3.3 4.8 4.7 4.6 3.9 6.1 4.8 5.6 4.9 6.1 4.8 5.1 3.8 3.4 4.1 2.5 5.1
##   [19] 3.9 4.7 4.1 5.3 5.2 5.3 4.4 4.3 4.2 4.5 5.9 6.3 2.5 4.7 6.1 5.7 3.5 5.7
##   [37] 5.2 4.7 6.3 5.3 4.1 3.4 3.4 4.9 4.2 3.6 4.6 3.7 4.1 5.0 4.2 3.2 4.3 3.9
##   [55] 4.6 4.2 4.7 5.6 4.4 4.5 4.5 4.5 5.1 3.8 6.0 4.9 5.1 5.7 3.5 5.4 4.4 2.4
##   [73] 3.7 3.9 3.5 3.7 3.3 5.0 4.2 3.4 3.7 4.2 3.1 4.2 3.8 3.8 2.4 4.1 3.1 6.3
##   [91] 4.4 4.7 4.7 3.1 5.6 5.2 4.0 3.8 3.7 6.7 3.7 3.6 3.6 4.1 3.9 4.0 4.8 3.6
##  [109] 5.4 5.0 5.9 6.8 5.6 3.9 4.4 4.6 5.0 5.5 4.1 5.0 3.7 5.2 3.1 3.9 4.5 4.4
##  [127] 3.5 5.6 5.9 6.7 4.3 4.6 4.9 4.2 4.3 4.3 5.4 5.1 4.9 3.8 4.7 5.2 6.1 4.9
##  [145] 2.9 4.4 4.7 3.9 4.9 3.4 5.2 3.5 5.1 3.3 4.7 4.7 4.4 5.0 3.7 4.7 4.7 5.5
##  [163] 4.4 3.1 4.8 3.0 1.9 5.4 3.6 3.9 5.0 2.0 4.6 4.3 3.8 5.0 4.4 5.2 3.6 4.0
##  [181] 3.1 3.5 5.4 5.4 5.2 5.0 4.2 3.1 4.7 4.7 3.8 4.4 4.3 4.7 3.2 4.5 5.9 2.0
##  [199] 4.0 3.7 4.4 3.4 4.4 2.7 4.4 6.0 4.2 4.7 4.4 3.0 4.2 4.6 4.6 4.6 5.9 3.7
##  [217] 6.4 5.4 4.8 4.3 3.3 6.1 4.6 4.4 5.0 5.7 3.6 5.9 5.3 5.0 3.5 5.1 4.2 3.8
##  [235] 3.5 4.0 4.6 4.0 4.1 4.3 5.4 5.0 4.0 3.1 3.4 3.5 4.8 3.7 4.4 3.8 4.8 4.3
##  [253] 2.7 2.7 3.9 5.5 4.4 3.7 4.9 4.8 4.6 4.4 5.2 3.5 3.7 4.1 4.1 5.2 5.9 4.7
##  [271] 5.6 4.9 4.8 3.5 3.0 3.0 4.0 6.1 5.9 6.7 2.4 3.6 5.1 4.5 3.7 4.7 4.8 5.4
##  [289] 3.9 3.1 4.3 4.2 5.0 3.7 5.3 4.8 5.2 4.8 5.2 5.3 4.5 6.3 6.7 5.9 3.8 4.5
##  [307] 4.3 4.0 3.9 5.4 3.9 5.7 4.8 3.3 3.3 4.5 4.4 4.0 3.4 3.7 5.9 3.9 2.3 5.7
##  [325] 6.1 3.6 4.8 3.3 6.1 3.1 4.6 5.3 3.9 4.5 4.3 4.9 5.0 4.6 5.4 4.7 4.2 4.1
##  [343] 5.8 5.3 6.0 4.3 5.6 4.8 5.2 3.1 5.6 4.1 3.4 5.5 3.8 4.2 4.5 3.9 5.6 4.0
##  [361] 5.7 3.2 3.1 2.8 4.4 6.1 5.5 3.2 3.1 5.0 2.9 3.5 3.7 3.8 3.7 4.0 3.2 5.9
##  [379] 3.7 4.1 4.1 3.5 2.6 3.6 6.2 2.4 3.6 4.7 5.4 4.4 3.5 4.3 5.8 5.2 4.6 4.1
##  [397] 5.1 3.5 5.7 5.7 3.7 4.3 5.2 5.1 4.1 4.4 3.6 4.5 5.1 4.3 4.0 5.6 5.8 4.8
##  [415] 4.6 5.3 4.4 5.1 4.3 3.2 5.0 2.0 3.8 5.0 3.2 7.1 2.6 2.2 5.4 4.1 5.2 5.3
##  [433] 5.1 4.2 4.9 4.3 5.9 4.8 5.4 5.4 4.0 4.7 4.6 5.1 4.9 3.7 3.6 5.9 4.8 5.6
##  [451] 5.0 4.4 4.6 4.1 4.1 2.6 4.0 4.8 4.8 4.6 5.3 4.5 3.2 4.7 3.6 4.1 5.3 5.0
##  [469] 5.7 4.1 3.4 3.4 3.8 4.3 5.9 5.7 4.9 5.2 5.1 5.3 4.7 4.2 4.7 4.0 4.5 3.7
##  [487] 4.1 4.7 4.5 5.1 4.0 4.8 3.2 3.3 5.3 5.2 5.4 4.2 5.5 3.0 5.3 4.2 2.6 5.6
##  [505] 5.1 4.7 4.4 3.8 5.1 4.9 6.7 4.9 4.5 4.6 4.8 4.0 5.0 3.5 6.0 5.2 4.7 4.3
##  [523] 5.5 5.6 3.7 4.4 4.8 4.4 4.3 4.8 6.5 5.0 6.8 4.4 4.2 3.1 3.5 4.3 4.2 4.2
##  [541] 3.6 5.0 4.9 4.3 4.9 5.1 4.7 4.5 5.5 5.0 3.3 2.9 5.4 3.6 4.5 5.1 3.8 5.2
##  [559] 4.2 3.7 4.4 4.9 3.9 5.7 4.7 6.1 3.8 3.3 5.1 2.8 5.3 3.6 4.1 3.1 5.0 4.1
##  [577] 5.0 4.6 4.9 4.8 5.7 5.9 3.7 5.2 4.4 3.2 2.5 5.6 5.0 4.7 5.0 3.2 6.3 2.0
##  [595] 4.0 5.1 5.0 4.8 4.4 4.8 5.2 4.5 3.0 3.9 4.0 5.4 4.6 3.9 4.6 4.2 5.4 6.1
##  [613] 3.9 5.5 4.5 3.1 4.2 3.5 5.3 4.4 6.2 4.4 5.8 4.4 4.6 3.5 3.5 4.7 3.7 4.0
##  [631] 4.4 4.5 3.6 4.4 5.5 4.5 3.1 3.4 3.9 3.7 4.9 2.3 5.6 5.4 4.2 4.5 5.7 4.4
##  [649] 4.6 3.7 5.5 3.6 4.1 4.6 4.4 3.5 5.7 3.0 3.3 4.2 4.5 4.1 4.5 4.8 5.0 3.4
##  [667] 3.7 4.5 4.3 4.0 4.5 4.8 4.2 5.7 3.6 4.3 4.4 4.8 6.0 2.8 6.3 6.1 2.8 4.7
##  [685] 4.5 4.0 5.3 5.1 6.6 4.4 4.8 5.7 3.8 3.7 4.4 3.9 4.0 5.3 5.5 3.0 3.9 5.3
##  [703] 4.7 3.5 5.2 4.3 4.6 3.8 4.5 4.3 4.3 3.4 3.9 3.9 5.8 3.3 3.0 4.8 5.7 5.0
##  [721] 4.7 3.8 5.7 4.4 5.4 2.8 3.9 2.6 3.5 4.6 4.7 4.1 5.0 4.2 5.0 4.6 5.3 3.4
##  [739] 3.6 4.0 4.3 5.3 4.1 4.9 4.9 5.5 5.7 5.1 3.8 3.9 4.2 4.0 3.9 5.2 5.4 4.1
##  [757] 3.3 3.7 2.8 4.5 4.3 3.4 6.1 5.2 4.0 3.7 5.1 3.2 6.9 5.0 3.7 4.4 4.7 5.0
##  [775] 5.6 5.6 5.1 4.6 5.4 4.5 4.8 3.8 4.2 4.9 3.2 3.3 5.1 3.8 3.2 5.4 5.0 4.0
##  [793] 4.2 5.4 4.4 4.0 4.4 4.0 4.3 4.4 3.2 3.8 4.4 3.6 3.6 4.6 3.0 4.9 3.3 4.7
##  [811] 5.8 5.7 4.6 5.8 4.3 4.5 4.7 4.7 3.9 3.5 3.8 4.9 4.7 3.9 4.4 5.5 3.9 3.5
##  [829] 5.0 3.2 3.7 4.0 2.7 4.5 3.5 4.1 5.3 2.7 3.8 5.3 3.6 3.0 4.8 4.7 4.1 4.5
##  [847] 3.3 4.8 4.4 5.5 5.6 4.1 3.9 2.9 4.3 2.0 2.9 3.8 5.1 5.0 4.9 3.8 4.4 3.8
##  [865] 5.0 4.5 3.3 5.6 4.0 4.8 4.1 3.8 4.6 5.6 4.2 4.1 5.2 4.6 3.9 5.5 5.3 3.3
##  [883] 4.5 5.6 5.3 4.2 3.4 4.3 5.5 4.5 4.1 4.1 3.1 5.2 4.4 5.2 4.8 5.3 4.1 5.2
##  [901] 4.7 6.6 3.9 4.3 5.6 4.5 3.2 4.2 5.1 5.0 5.7 2.7 3.1 4.2 3.6 5.2 5.8 3.7
##  [919] 4.2 5.0 4.6 4.1 5.3 3.4 3.9 2.8 4.5 4.9 5.0 3.5 4.4 4.4 4.0 3.8 4.0 3.9
##  [937] 4.4 4.8 4.1 5.4 4.6 4.9 3.7 5.6 5.6 4.1 5.1 3.2 4.3 4.2 3.6 2.4 4.8 3.7
##  [955] 4.7 3.8 4.6 4.2 4.5 5.7 3.0 5.3 5.6 3.9 4.4 4.5 4.8 5.4 3.8 5.4 4.8 5.1
##  [973] 4.2 4.6 4.9 6.5 3.8 5.1 5.4 2.9 5.0 2.9 5.9 4.6 5.0 3.9 3.9 4.1 3.8 4.9
##  [991] 4.2 5.5 4.8 4.9 5.3 4.5 3.3 5.3 4.5 4.5
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
##   2.7   6.1
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
##    [1] 6.0 5.5 4.0 4.1 5.4 2.3 6.0 3.3 3.6 4.8 3.3 4.6 4.9 4.4 5.3 3.7 4.8 3.4
##   [19] 4.6 5.8 5.1 4.1 4.7 3.7 4.2 5.8 4.1 4.0 3.4 5.9 4.1 2.9 5.1 3.7 4.4 5.9
##   [37] 4.8 4.4 6.2 3.8 4.2 4.4 4.2 4.9 4.3 3.5 5.8 3.6 3.4 3.3 4.3 5.1 5.2 3.8
##   [55] 5.1 5.2 4.5 4.0 5.1 3.3 4.8 4.7 4.4 3.4 4.7 3.8 3.5 4.8 5.7 5.2 5.5 4.2
##   [73] 2.4 3.9 5.3 5.5 4.7 3.1 5.4 4.5 4.8 4.3 6.0 3.9 5.9 4.4 4.6 4.6 4.7 6.1
##   [91] 5.2 2.6 4.6 4.4 5.3 6.3 4.2 4.9 4.0 4.2 4.9 3.7 4.2 3.2 5.4 5.6 5.6 3.3
##  [109] 6.0 6.3 3.7 6.3 5.9 4.7 4.9 2.4 3.9 2.3 5.4 3.7 5.0 4.4 3.0 3.6 5.5 3.2
##  [127] 4.5 4.5 4.2 4.7 2.6 4.7 4.4 5.6 6.2 4.0 4.0 4.8 4.9 4.9 5.5 4.7 3.7 5.5
##  [145] 4.1 3.3 6.4 5.1 4.0 3.5 5.6 4.2 3.7 4.4 3.7 5.7 5.4 2.9 6.2 2.9 4.8 3.6
##  [163] 5.4 3.4 3.7 2.3 4.3 3.3 4.9 5.4 5.8 5.1 5.3 3.4 4.5 4.5 3.6 4.1 4.8 4.2
##  [181] 4.0 5.3 4.7 5.0 4.6 4.2 4.7 5.8 5.0 4.9 3.3 2.6 4.9 4.0 4.0 5.7 4.3 3.0
##  [199] 3.6 4.7 4.8 5.0 4.8 5.2 3.1 4.6 3.7 5.1 4.5 3.7 4.3 3.5 5.5 4.9 4.1 3.7
##  [217] 5.2 2.8 4.1 5.1 3.5 4.1 5.0 4.1 3.3 3.4 5.4 4.6 5.8 4.6 3.7 5.0 5.2 4.9
##  [235] 3.8 5.1 5.4 5.4 4.0 5.7 4.6 5.3 3.7 2.8 4.2 5.3 3.2 5.2 3.4 4.8 2.7 4.0
##  [253] 4.1 5.4 6.7 3.7 5.2 5.7 7.1 5.0 4.5 5.5 3.6 4.7 3.0 3.7 5.8 4.3 5.6 3.9
##  [271] 4.7 4.1 4.4 5.0 4.1 6.0 5.4 3.2 4.9 2.3 4.7 4.7 2.9 2.9 5.5 6.3 4.6 5.1
##  [289] 4.7 5.8 4.9 3.7 3.7 3.6 4.0 4.3 3.9 4.6 5.7 5.5 4.2 3.2 5.2 3.8 5.0 3.0
##  [307] 4.5 2.6 4.8 4.9 4.6 3.4 4.9 4.8 3.2 3.5 3.2 3.8 4.9 5.1 3.9 4.6 3.5 4.1
##  [325] 5.9 4.3 4.4 2.4 5.5 5.7 4.1 4.8 5.1 4.4 6.0 3.8 5.0 4.1 5.7 4.6 4.2 5.4
##  [343] 5.1 5.9 4.4 3.4 4.4 3.6 3.6 4.7 5.4 5.3 4.5 5.0 5.3 4.4 4.6 5.3 4.2 3.1
##  [361] 6.7 5.0 3.2 5.1 4.9 5.0 4.9 4.5 3.9 3.4 3.7 3.7 6.3 6.0 5.7 5.8 5.2 4.5
##  [379] 3.8 4.8 5.4 3.4 4.0 3.7 5.3 4.3 4.1 4.0 5.2 5.0 3.9 4.2 3.6 6.0 3.4 2.7
##  [397] 2.9 4.7 4.3 3.0 3.8 4.5 4.6 3.4 4.7 5.3 5.6 5.5 3.1 5.4 5.4 4.8 3.9 3.5
##  [415] 5.2 3.1 4.3 6.2 5.3 4.7 4.3 4.2 3.2 3.4 4.0 2.7 4.9 4.2 3.8 4.7 3.7 7.5
##  [433] 5.8 3.7 5.0 4.9 5.9 4.5 6.4 4.4 4.9 2.5 3.8 2.3 4.7 3.8 3.3 5.7 6.1 5.0
##  [451] 4.1 5.3 5.8 3.2 3.9 5.5 4.4 4.2 4.0 3.9 6.1 1.6 6.3 5.5 5.5 5.0 3.0 2.4
##  [469] 5.2 3.8 5.2 4.4 5.1 5.2 4.1 4.8 5.3 3.7 4.0 3.2 4.2 3.2 4.2 4.8 3.8 3.9
##  [487] 4.5 2.8 2.9 4.6 2.9 4.3 5.6 4.9 4.2 5.1 6.5 6.1 5.6 3.8 5.4 3.3 3.9 3.9
##  [505] 3.8 4.1 5.2 4.6 4.2 2.9 3.8 5.5 4.6 5.0 4.0 4.5 4.7 4.9 5.4 4.7 5.2 4.5
##  [523] 4.0 4.0 5.6 4.6 2.8 4.0 4.8 3.5 4.2 4.4 6.6 5.0 3.9 4.8 5.9 4.0 5.6 4.0
##  [541] 4.7 5.5 4.1 2.0 5.9 6.1 4.4 4.6 3.3 3.6 4.3 3.4 5.2 5.3 5.7 4.9 4.9 3.9
##  [559] 4.5 4.3 4.3 6.5 6.2 5.1 3.9 5.6 5.1 4.1 5.5 3.6 6.5 3.4 4.3 3.6 4.8 5.4
##  [577] 5.2 5.4 3.5 2.7 5.0 4.1 3.9 4.7 4.7 5.3 5.8 4.3 5.6 4.5 5.0 5.7 3.6 4.2
##  [595] 5.1 4.1 4.3 3.9 3.0 5.0 4.4 5.3 4.0 5.5 4.9 4.3 3.1 6.3 5.1 5.3 4.2 4.8
##  [613] 4.3 4.4 5.1 4.5 4.4 5.9 3.1 3.9 4.8 4.6 5.1 4.5 4.3 3.5 6.2 5.4 4.1 4.5
##  [631] 4.3 5.4 4.5 7.1 1.9 2.1 4.4 4.4 4.2 3.2 2.4 3.2 4.3 4.0 4.8 5.3 5.5 4.8
##  [649] 3.4 4.6 4.9 3.8 4.5 4.8 4.8 5.3 5.0 4.4 5.5 4.9 4.6 3.7 5.7 5.2 3.8 5.4
##  [667] 4.5 5.1 5.9 4.5 4.5 5.3 4.7 3.9 3.7 3.5 5.2 4.3 3.8 2.5 5.1 4.0 4.7 5.0
##  [685] 4.5 4.1 4.1 4.6 3.2 2.9 4.1 3.7 4.9 5.8 6.0 4.2 6.3 3.3 3.2 2.6 5.7 5.2
##  [703] 4.5 3.3 4.8 3.3 4.7 4.0 6.1 5.1 5.4 3.5 1.9 5.1 5.4 5.5 5.2 4.3 3.5 4.1
##  [721] 5.2 3.7 4.9 3.5 3.9 5.0 5.5 3.3 4.9 4.3 3.4 3.6 4.7 5.9 4.3 3.4 4.8 4.9
##  [739] 4.6 4.7 5.7 2.7 4.5 3.1 3.4 4.1 5.5 4.7 5.3 4.8 4.6 4.2 5.1 4.7 3.9 6.1
##  [757] 4.2 4.3 4.1 5.4 4.2 3.7 4.4 5.0 6.5 4.4 3.8 5.0 3.9 3.9 4.7 5.0 6.3 4.2
##  [775] 3.3 3.1 5.0 3.9 6.5 5.8 5.1 5.8 6.7 6.3 4.3 4.5 3.0 3.7 5.7 5.1 4.6 4.3
##  [793] 4.5 4.3 4.7 5.5 3.1 5.7 5.3 3.2 6.0 4.8 4.4 3.9 3.8 4.3 3.6 4.8 3.6 6.1
##  [811] 3.6 5.2 4.3 5.6 4.0 5.0 3.1 4.8 4.9 2.9 5.6 4.2 4.3 4.9 4.0 3.5 4.5 4.9
##  [829] 5.6 2.9 4.0 4.4 2.8 3.2 4.3 5.5 3.4 4.0 4.6 3.1 4.2 4.2 3.6 5.9 5.1 4.1
##  [847] 5.0 4.3 5.5 5.2 5.2 3.9 3.7 4.3 4.8 4.3 6.3 3.9 4.7 5.0 3.9 4.5 4.7 3.6
##  [865] 4.4 3.4 4.2 5.2 3.4 5.2 3.9 3.3 5.0 5.9 5.1 4.8 4.7 3.6 4.5 4.5 3.2 3.9
##  [883] 3.9 3.6 5.5 6.4 3.8 4.5 6.2 4.3 5.4 4.1 4.5 2.3 2.8 3.5 2.6 5.7 3.9 5.3
##  [901] 4.0 4.9 5.9 3.7 4.6 3.9 4.1 3.8 3.4 3.7 3.8 4.8 4.2 4.9 6.0 5.0 4.0 4.4
##  [919] 6.1 4.5 5.6 5.5 4.7 4.7 4.3 4.6 6.2 6.2 6.8 4.1 4.6 2.4 5.1 4.1 4.1 3.3
##  [937] 4.3 4.1 4.9 4.8 2.8 5.0 3.2 6.1 4.0 3.4 4.4 4.2 4.5 4.8 6.0 3.4 3.1 3.3
##  [955] 3.6 6.1 3.6 4.6 3.9 5.3 4.7 3.9 3.1 4.2 4.2 3.4 5.9 5.2 4.2 6.4 3.1 3.1
##  [973] 3.4 3.7 3.9 5.3 3.7 4.7 5.5 5.0 4.4 4.3 3.3 3.9 4.9 3.5 2.3 3.4 4.2 5.7
##  [991] 6.0 4.1 5.7 3.4 4.8 4.0 2.7 4.1 4.4 4.2
## 
## $func.thetastar
## [1] -0.0185
## 
## $jack.boot.val
##  [1]  0.457863501  0.380653951  0.251841360  0.224117647 -0.001886792
##  [6] -0.088515406 -0.217819149 -0.330790960 -0.427704485 -0.516279070
## 
## $jack.boot.se
## [1] 0.9836892
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
##    [1] 4.7 3.3 4.3 5.7 5.8 4.4 4.9 4.8 4.7 4.2 5.1 4.9 4.0 4.4 3.8 4.2 3.3 4.9
##   [19] 4.1 3.4 2.3 6.5 4.1 3.7 5.7 3.8 3.4 4.2 5.8 4.0 3.9 4.6 4.5 2.7 4.0 3.9
##   [37] 3.7 4.0 2.3 3.3 5.6 2.9 4.0 4.6 5.5 5.3 5.2 5.4 3.7 5.4 4.8 5.7 4.4 4.6
##   [55] 5.4 1.8 5.9 5.0 4.7 5.0 4.3 4.4 4.5 4.2 3.7 4.5 3.6 4.8 4.6 4.6 4.9 2.7
##   [73] 5.5 4.7 4.4 4.0 2.2 3.9 3.9 4.5 4.8 4.6 4.0 4.4 3.4 6.0 5.1 3.6 3.9 4.3
##   [91] 4.6 2.7 3.9 5.9 4.2 3.3 5.9 4.0 2.9 3.7 4.8 6.6 4.6 5.1 5.4 3.7 4.9 4.4
##  [109] 4.1 5.3 2.4 6.0 4.9 4.1 4.4 3.4 5.5 3.9 4.5 5.1 5.3 2.3 5.7 4.2 3.4 4.2
##  [127] 4.1 4.1 4.8 4.8 4.5 3.9 4.0 3.8 5.1 3.2 6.6 5.1 4.7 6.1 4.5 3.6 4.2 5.7
##  [145] 3.7 4.0 5.5 3.8 6.6 3.1 4.9 4.4 3.3 5.6 3.7 2.8 3.5 2.9 5.9 4.4 4.9 5.0
##  [163] 5.5 3.1 3.6 5.3 3.0 5.4 3.9 4.9 5.9 5.6 4.8 5.6 4.0 5.4 3.8 3.8 4.4 5.9
##  [181] 5.0 3.5 4.6 4.4 4.6 5.0 3.8 6.2 5.1 2.9 5.0 3.4 4.2 4.6 4.8 4.4 4.9 5.0
##  [199] 4.2 2.8 3.9 4.7 5.8 4.5 5.2 4.2 3.6 4.3 4.8 4.2 4.7 5.4 5.0 4.9 5.7 5.8
##  [217] 4.6 6.4 5.7 4.3 4.3 4.2 4.5 4.5 3.7 4.5 5.2 4.3 5.3 5.2 4.3 6.1 4.4 4.2
##  [235] 3.3 5.5 4.5 4.9 5.3 5.8 5.0 4.4 3.9 5.0 5.2 4.9 4.4 6.0 3.0 2.1 4.5 6.1
##  [253] 4.3 4.5 4.0 4.7 4.4 3.8 5.2 4.4 5.1 3.4 4.2 5.4 4.7 4.1 5.9 4.8 4.2 3.8
##  [271] 3.9 3.3 4.2 4.6 4.5 4.6 3.9 4.8 5.6 3.8 4.6 4.8 4.1 4.4 5.6 3.8 5.0 5.3
##  [289] 4.0 5.5 4.2 6.0 4.1 5.1 5.5 4.5 3.8 5.0 4.0 6.4 4.0 4.6 5.7 4.1 4.3 4.2
##  [307] 3.3 4.5 6.2 4.6 5.2 3.6 3.5 4.7 5.6 3.2 4.8 2.8 4.2 5.2 5.5 4.0 4.7 5.5
##  [325] 4.6 4.1 5.4 4.3 4.4 3.7 4.5 4.4 5.2 4.9 3.6 5.1 4.5 4.0 4.4 4.8 5.1 5.1
##  [343] 6.0 2.8 3.8 5.2 5.8 3.4 5.5 4.7 4.1 2.5 3.7 3.7 3.7 3.0 5.2 5.7 2.5 6.0
##  [361] 5.9 5.3 4.6 4.5 3.4 4.5 5.3 5.3 3.8 3.9 3.3 4.3 5.1 4.2 4.5 4.9 4.0 5.1
##  [379] 4.6 5.3 4.5 4.0 3.3 4.6 3.8 4.3 5.4 5.1 4.3 3.2 5.2 4.8 3.9 5.1 4.8 4.1
##  [397] 5.5 4.8 4.9 6.3 4.9 4.0 3.4 5.1 4.0 4.6 5.6 5.1 5.3 6.2 4.9 4.1 6.2 3.9
##  [415] 5.5 4.3 5.4 3.9 4.8 4.4 4.5 5.4 4.5 6.1 4.2 5.1 4.2 4.2 4.9 4.7 4.7 3.7
##  [433] 2.7 4.0 6.0 4.0 4.0 5.1 3.1 4.6 4.6 5.7 5.0 3.6 4.0 3.9 4.6 5.4 5.2 6.0
##  [451] 4.0 4.4 5.5 4.0 4.4 5.0 3.5 6.2 4.0 4.1 4.0 5.2 3.3 3.9 4.3 5.4 3.3 3.1
##  [469] 5.4 4.0 4.3 4.6 6.2 3.4 3.9 6.3 4.0 4.1 3.8 4.3 4.8 5.1 5.0 4.1 3.5 4.4
##  [487] 2.6 3.8 4.9 4.3 4.5 4.9 4.6 4.7 5.1 5.2 4.7 5.4 3.5 3.1 4.0 4.1 4.6 5.6
##  [505] 3.4 5.6 5.4 3.5 5.2 5.5 5.6 4.9 4.7 4.2 4.6 5.2 5.4 4.3 5.0 3.7 4.3 3.1
##  [523] 3.4 4.9 5.2 3.9 4.5 5.4 4.3 5.9 5.3 4.3 4.0 5.4 5.5 2.1 4.2 4.9 4.8 6.0
##  [541] 4.4 5.8 4.3 5.7 3.9 4.7 3.3 6.0 4.4 6.2 4.5 4.2 5.1 4.8 4.9 6.1 4.9 3.5
##  [559] 4.1 4.3 3.8 3.7 3.8 3.4 4.6 5.0 3.9 5.0 3.6 4.3 3.9 4.0 5.2 4.3 4.3 4.9
##  [577] 4.6 4.7 4.5 4.3 3.1 5.5 5.4 4.5 3.9 4.9 4.5 4.0 4.6 4.4 5.1 3.9 4.1 4.5
##  [595] 4.5 5.2 4.0 3.7 3.8 4.8 4.5 5.0 4.1 4.6 5.6 3.2 3.8 6.5 5.7 3.8 6.2 3.8
##  [613] 3.6 3.0 5.7 6.3 3.9 4.0 5.2 3.9 4.9 5.7 4.0 6.0 4.6 5.9 6.4 4.3 5.4 3.9
##  [631] 4.1 4.9 6.2 4.7 4.6 4.6 3.4 3.9 4.1 5.7 4.4 3.8 3.9 4.2 4.6 4.1 4.4 4.0
##  [649] 4.9 2.7 4.9 4.0 2.8 3.9 6.3 5.8 5.3 5.4 3.3 2.7 3.5 5.9 3.9 6.0 6.6 6.6
##  [667] 5.3 3.5 3.1 4.9 3.8 3.6 4.4 4.7 4.6 4.4 3.5 5.4 3.5 3.1 4.8 4.2 3.4 4.4
##  [685] 5.2 4.0 4.8 5.2 3.1 2.9 4.2 4.9 4.1 5.2 4.6 5.5 3.8 4.6 4.4 3.9 2.4 5.4
##  [703] 5.6 6.2 3.2 5.4 3.1 4.6 4.8 5.6 6.0 4.2 3.4 3.5 3.9 5.0 3.3 4.5 4.9 4.2
##  [721] 4.7 5.0 4.1 3.3 4.0 5.8 4.1 5.6 4.0 4.2 6.0 5.5 4.6 5.7 3.2 5.8 4.0 2.8
##  [739] 3.5 5.1 3.1 4.6 6.3 3.7 5.2 3.8 4.8 3.8 3.5 3.5 4.7 5.7 5.2 3.9 5.5 4.0
##  [757] 5.0 6.1 5.0 5.3 5.0 4.2 3.7 3.7 4.5 5.8 6.0 3.8 4.6 3.5 3.9 5.5 3.2 4.8
##  [775] 4.5 4.0 4.7 4.5 3.6 5.3 4.7 2.8 3.5 4.3 5.3 5.2 3.9 3.3 3.3 3.6 5.6 4.3
##  [793] 5.0 4.5 5.5 5.1 5.2 4.5 3.8 5.5 3.4 4.2 4.6 5.4 6.2 3.5 3.3 4.7 5.3 3.4
##  [811] 4.0 5.7 5.6 3.7 4.3 6.8 4.1 4.3 5.0 4.6 6.3 3.9 3.2 6.6 3.9 5.4 5.8 5.5
##  [829] 5.9 4.5 3.6 3.4 3.6 5.8 4.7 4.6 4.3 3.2 4.6 5.4 6.5 4.7 3.9 5.1 4.2 3.4
##  [847] 4.5 3.3 3.1 5.4 4.1 4.9 5.0 3.0 2.8 3.2 4.8 5.7 3.6 4.1 5.0 3.9 5.0 5.4
##  [865] 4.3 4.4 6.0 4.1 5.5 4.8 3.9 5.5 4.7 6.1 3.6 4.4 3.9 3.6 3.3 5.0 5.2 4.5
##  [883] 3.9 5.0 4.0 3.2 4.8 2.8 4.4 3.9 4.0 4.7 5.7 3.3 3.9 5.7 3.6 5.9 3.7 4.8
##  [901] 5.2 4.4 5.4 4.6 6.6 5.8 3.7 4.7 6.2 4.0 5.2 5.0 5.6 5.0 5.0 5.6 4.8 3.2
##  [919] 4.3 4.2 5.8 3.6 3.2 6.0 4.3 4.5 4.8 5.1 5.8 4.4 5.0 3.7 4.4 6.2 4.9 3.8
##  [937] 4.3 6.1 5.0 5.2 3.7 5.4 3.5 3.6 4.8 4.8 5.7 3.6 4.2 3.7 4.2 2.6 3.5 4.7
##  [955] 3.8 4.7 4.4 3.2 4.7 6.4 4.5 4.3 5.4 4.9 4.7 3.7 4.1 4.0 5.2 3.7 5.4 4.3
##  [973] 3.9 3.9 5.5 6.4 6.3 6.2 5.7 5.8 4.1 3.9 4.2 5.5 4.7 3.8 4.3 4.9 5.2 2.4
##  [991] 5.1 2.7 3.8 4.7 4.0 5.3 4.4 5.0 3.5 4.6
## 
## $func.thetastar
##   72% 
## 5.028 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.400 5.200 5.188 4.964 4.900 4.756 4.600 4.500
## 
## $jack.boot.se
## [1] 0.9992881
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
## [1] 0.6748849
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
##   3.179746   5.394416 
##  (1.354072) (2.488406)
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
## [1] -0.6353227  0.7450815  0.8419238  0.1380402 -0.2861410 -0.1365442
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
##    [1]  0.121077301 -0.167892253  0.762961785  0.378414170  0.338410784
##    [6]  0.627005716  0.088667068  0.962501224  1.839459977  0.604048438
##   [11] -0.079897301  1.345650736 -0.175309839  0.349658600  0.611670197
##   [16]  0.194483760 -0.023734212  0.784195794  0.332918878  0.404128952
##   [21]  0.029458927  0.654781947 -0.341239982  0.911130758  0.754123059
##   [26]  0.565031735  0.308927879  0.244904979  0.045795883 -0.222733834
##   [31]  0.138567731  1.624361667  1.063454707  0.161374046  0.371982899
##   [36]  0.644702701  1.727479587  1.289819672  0.883279512  0.558460058
##   [41]  1.459925873  0.221023199  1.532057298  0.133065792  1.194677844
##   [46]  0.034597604  0.400004639  1.627880411  1.230965189  1.057077014
##   [51] -0.244950596  0.520099502  0.522389116  0.397618810  0.531763022
##   [56]  0.690481374  0.397888654  0.907027458  1.188222783 -0.281366187
##   [61]  0.462518891  0.055254889  0.530953215  0.509353969 -0.628950405
##   [66]  0.386971057  0.914817823  1.195623205  1.188222783  1.158964586
##   [71] -0.195815398 -0.887173320  2.086579542 -0.258510537 -0.021757390
##   [76]  0.479772087  0.118683338  0.145730596  0.734560923  1.055281050
##   [81]  0.270079648  1.383225882  0.487460578  0.358460475 -0.030416413
##   [86]  0.541290337  0.151970122  0.239867695 -0.166734726  0.767614506
##   [91] -0.459642740  2.180182198  0.275797648  1.781277686  0.700933681
##   [96]  1.412760006  0.474835879  0.960323706  0.604351442  0.175241972
##  [101]  0.997494406 -0.064323024  1.164938257  0.004283848  0.666791209
##  [106]  0.758440992 -0.705412634  1.415799924  0.155978514  0.460935089
##  [111]  0.267748346  0.009291630  1.221529129  0.907040745  0.457419362
##  [116]  0.543656923  0.213524684  0.848447485  0.416568419 -0.400384719
##  [121]  0.107487371  0.091720314  0.706333106  1.174570836  1.716201662
##  [126]  0.390830099  0.707703633  0.419542566  0.450253954  0.064290779
##  [131] -0.158506150  0.439121272  0.965825282  0.447358347  0.270071834
##  [136]  0.851914990  0.957674963 -0.121688177  0.384415563  0.257006476
##  [141] -0.056351537  0.194058753  0.443993591  0.486241125  1.118943225
##  [146]  0.475656118 -0.314114867  0.076278436  0.587175684  0.367461680
##  [151]  0.711245099  0.353004823  0.500317542  1.058759144  0.305649248
##  [156]  0.913227411  0.406092550  1.272942718  0.566714787  0.671438414
##  [161]  0.488326485 -0.408386545 -0.062983746  0.861348408 -0.600871352
##  [166]  0.389527242  1.085692863  0.968856602  0.316908740  0.762715771
##  [171]  0.315129407  0.524778781  0.677966859  0.527350935  0.726095173
##  [176]  0.799442542  0.470772890  0.803915830  0.745029582  0.520880571
##  [181]  0.542522768  0.268503968  0.191785818  0.509490492  1.055579827
##  [186]  0.408693360  0.548552208  0.981529955  0.911083307  0.776027591
##  [191]  0.484341023  0.421560134  0.315212749 -0.279685204  0.857907201
##  [196]  0.917178067  0.472325499 -1.087073527  0.210199830  0.763515707
##  [201]  0.239090184  1.254441684  0.201473997  1.216265759  0.544127133
##  [206]  1.106588536  0.514693892  0.620248791  1.520552441  0.210009642
##  [211]  0.161203284  1.080562591  0.401820786  0.262396870  0.571741110
##  [216]  0.390360922 -0.220615829  0.020465133  0.720669657  0.686203271
##  [221]  1.436698697  0.273067580  0.575130590  0.690966218  0.443087926
##  [226]  0.892069565  1.085515755  1.174080598  0.895840899  0.018416537
##  [231]  0.886527899  0.238992807  0.380081290  1.241050475  0.742008292
##  [236]  0.207420324  0.222396767  0.610902528  1.113179987  1.409350445
##  [241]  0.045162694  0.662632852  1.242679326  1.406256565  0.519022916
##  [246]  0.431273733  1.165287599  0.334794018  1.036310712  0.129977956
##  [251]  0.960323706  0.557137300  0.807998663  0.327483873  0.593326590
##  [256] -0.086914542  0.737751673  0.700121478  1.076075608 -0.708226699
##  [261] -0.212305989 -0.097694470  0.813147854  0.979682151  1.612104331
##  [266]  0.096315045  0.035266342  0.322975894 -0.564969467  0.524192558
##  [271]  0.848227672  0.685210030  2.051701923  0.187489229 -0.177077630
##  [276]  0.442927504  0.702664390  0.656913336 -0.331933890  1.045031059
##  [281]  0.221865771  1.395783371  0.609756665  0.538231814  0.265693056
##  [286]  0.537104643  0.598590637  0.970347461  0.666730664  1.076078299
##  [291]  0.431569468  0.217635002  0.717002560  1.703157802  1.184827870
##  [296]  0.089798715  0.951037361  0.682894464 -0.638496777  0.597785405
##  [301]  0.660507555  0.459834287  0.534139971  0.057304600  0.896856521
##  [306]  0.423397014  0.315880363  0.654049505  0.289015548  0.041880106
##  [311]  0.531182031  0.666548359  0.055492759  0.323557105  0.075947616
##  [316]  1.156474658  0.533458482  0.444879097  0.757625342  1.128489557
##  [321]  0.193946825  0.872526000  1.442116881  0.279837401  0.117252442
##  [326]  0.365763623 -0.118675702  0.040770185  0.691722621  0.167906532
##  [331] -0.088420110  0.833635019 -0.050140976  0.353206612 -0.838470992
##  [336]  0.915643690  0.353059998 -0.380584188  0.494721341  0.291340798
##  [341]  1.000892973 -0.417479455  0.512693968  0.772793342  1.508011851
##  [346]  1.271368382  0.445140013  0.683772827  0.962501224  0.172549921
##  [351]  1.477595124 -0.195897670  0.591673032  0.187358062  2.185121104
##  [356] -0.357289949  0.430144647  0.449436913  0.695186835  1.093273906
##  [361] -0.061123658  0.737983220  0.357437513  0.428734338  0.560646411
##  [366]  0.998576360  0.578657621  0.105140979  0.320632905 -0.217939103
##  [371]  0.014469912  1.015242323  0.989663121  1.025518922  0.237355223
##  [376]  0.683803807  0.723894874  0.023105659  0.106367666  0.390524715
##  [381]  0.684355207  0.679138776  1.236072062  0.921385848  1.373725874
##  [386]  0.924724484  0.664969806  0.243421346  0.783107795  0.806296092
##  [391]  0.109704128  0.636084031  0.660507555  0.248840470  0.344509484
##  [396]  0.754779200  1.203779915  0.096118003  0.618205718  0.840299149
##  [401]  0.451912433  0.456185496  0.761187097  1.075290132  0.607692742
##  [406]  0.136961778  0.757875641  1.015334263 -0.498311146  0.683274708
##  [411]  0.631896095  0.389128959  0.536894619  0.731778124  0.830628364
##  [416] -0.065545599  0.320002092  0.863836539  0.278417003 -0.168234470
##  [421]  0.369896922  0.952483879  0.764440969  0.900142914 -0.382854657
##  [426]  0.721254478  0.312264592  0.336688857  0.128809193  0.735734974
##  [431]  0.874690203  0.755859810  0.254345117  0.097021500  0.744779717
##  [436]  1.192754702  0.496320078  1.475233314  0.427185483  0.523907761
##  [441]  0.917893308  0.760561560  0.419604835  0.656245862  0.966089556
##  [446] -0.031886622  0.914399939  0.182858599  1.043003777  0.277934741
##  [451]  0.900607911  0.565166728  0.453556597 -0.277103976  0.630914568
##  [456]  1.112822316  0.866153914  0.942784579  0.170461743  0.813147854
##  [461]  0.824364162 -0.564187627  1.843367943  1.416163512  1.188768242
##  [466]  0.412712742  1.406803025  2.037328194  1.337985550  0.185915296
##  [471] -0.412399634  0.747077693  0.965300332  0.292487917  1.066980473
##  [476] -0.129285027  0.318343568  1.593483636 -0.107536641  1.300672569
##  [481]  1.485487230  1.390552945  0.395713437  0.899075655  0.403918010
##  [486]  0.703867110  1.108559712  0.683950872 -0.017413316 -0.037335332
##  [491]  0.270599986  1.398819457  2.436639815  0.702609877  0.375809506
##  [496]  0.294641936  1.109213109  0.192158169  0.811535574  1.086897262
##  [501]  0.740121469  0.813869088  0.685005362  0.946993161  0.082835553
##  [506]  0.183618192  0.873809710  0.388875705  0.202658377  0.571952216
##  [511]  0.281179011  1.205436296  0.615345724  0.737983220  0.305916793
##  [516]  0.390349644  0.749398472  0.170467718  0.134549398  0.571313172
##  [521]  0.897714030  0.003730625  1.218482583  1.207951703  0.632449102
##  [526]  0.408556050  0.269528664  0.498311544  0.247998471  0.233142199
##  [531]  0.922543780  1.324406758 -0.244884408  0.184181375  0.234548207
##  [536]  0.809845865  0.709301991  0.979702497  1.563771142  0.883941268
##  [541]  0.784409740  1.440602792  0.917197437  0.493919315 -0.105036462
##  [546]  0.852341397  0.268115286  1.202637845  1.279175600  0.266685090
##  [551]  0.041022121  2.067235405  0.267976860  0.898237669  1.196726777
##  [556] -0.103936661  0.108216453  0.639999026 -0.094171785  0.178507589
##  [561]  0.040221498  0.596271057 -0.808121301  0.448111473  0.115266356
##  [566]  0.265773923  0.220325602  0.882521217  0.646312103  1.133514777
##  [571]  1.067657561  0.615290191 -0.639014286 -0.611548973 -0.059638737
##  [576]  0.476067813  0.015509831  0.836813369  0.745760962  0.674884874
##  [581] -0.442146843  0.527767051  1.176501464  0.314523891  1.594797703
##  [586] -0.038932706  1.614975835  0.610486941  0.503654167  0.217598095
##  [591]  0.560646230  0.333470304  0.076278659  0.108789773  0.159021655
##  [596]  0.612630614  0.364354758  0.489320743  0.784085876  0.965013017
##  [601]  0.068604966  1.397296449  1.130903388  0.368489376  0.921527240
##  [606]  0.377389526  0.569501781  0.939528743  0.684230501  0.345382036
##  [611]  0.117714831  0.142142136  0.988063215  0.320451581  1.650810752
##  [616]  0.297764609  0.224626918  0.610273471  0.159171277  0.658084895
##  [621]  1.056936700 -0.388586539 -0.045190891  0.740196615  0.135107577
##  [626]  0.399466698  0.031287003 -0.104587584  0.736685667  0.639999026
##  [631]  0.735784850  0.639964446  0.815902716  0.687370519  2.196662300
##  [636]  1.109055483 -1.087073527  0.309950641  0.104446625  0.328311026
##  [641]  0.640047855  0.498601330  0.896208577  0.966089556  0.798609235
##  [646]  0.579710246  0.355159014  0.491413664  0.503384292 -0.085738291
##  [651]  0.082101156  0.346252799  0.376070557 -0.013164875  1.946132204
##  [656]  0.384265398  0.022564505  1.295221136  1.047815081 -0.373271108
##  [661]  0.629670074  0.644697251 -0.228972955  0.727937516  0.433684899
##  [666]  1.086423336  0.765236080  0.926696078  0.173691292  0.153762213
##  [671]  0.181428119  0.716975623 -0.363824475  1.785707032  1.322853656
##  [676]  1.172052123  0.273335146  0.857597707  1.117726833 -0.315271021
##  [681]  0.738061957  1.557600768  0.365831867  1.540805211  0.548552208
##  [686]  1.378108424 -0.176736700  0.571427109  0.378090781  0.526408202
##  [691]  0.245773484 -0.101733897  0.193946825  1.115273213  0.473108910
##  [696] -0.148185812  0.292381167  0.706152632  0.330782289 -0.028816827
##  [701]  0.916609632 -0.810678673  0.866117141  0.989290932  1.214669289
##  [706]  0.095664298  0.559769694 -0.166287861  0.143990188  1.135614537
##  [711]  0.798447843  0.569825755  0.874301235  0.909103156  1.055053884
##  [716]  0.203577764  0.120718312  0.228273756  1.045742464  1.276822966
##  [721]  0.490340328  0.838310901  0.450658213  0.745839918  0.722390609
##  [726] -0.547259970  0.992970830  0.527151985  0.590823714  1.296574439
##  [731]  1.248070248  0.891415479  0.314903092  0.086506230  1.293842753
##  [736] -0.003642544  0.674884874  0.671930481  0.517979845  0.313518369
##  [741]  0.950009004  0.939521174 -0.204966364 -0.473787243  0.117778491
##  [746]  1.229474330  0.189702424  0.816335084  0.706668799  0.583844989
##  [751]  0.998325180  0.883175964  0.837149194  0.509417855  0.182474702
##  [756]  0.026834860  0.921190358  0.728417272  0.172074051  0.742109244
##  [761]  1.099790792  0.674754042  0.324161959  2.090796220  0.849410603
##  [766]  0.051775735  1.029202227  1.215723159  0.538231814  0.453565281
##  [771]  1.464083848 -0.153269215  1.868537037  1.187122259  1.005157569
##  [776]  0.243824875  0.474152466 -0.033924372  0.033699117  1.133954218
##  [781]  0.436705536  0.616483747  0.557857553  1.155817347  0.468748548
##  [786]  1.322284862  1.150238877  0.282558838  0.794465413  1.204530995
##  [791]  0.429408442  0.890894586  0.952611513  0.338410784 -0.306459217
##  [796]  0.674384443  0.982133532  0.391480804 -0.681191991  0.363958719
##  [801]  0.885183184  0.468784109 -0.031844104  0.918294188  0.567556334
##  [806]  1.023952125  0.516399935  0.906757627  1.736544781  1.112127335
##  [811]  0.066414559  1.039877444  0.954830132 -0.174037021  0.598031230
##  [816]  0.551896928 -0.020852549  0.342082820  0.479293052  0.547019649
##  [821] -0.748482339  0.346332469  0.773040519  1.127378283  0.781243891
##  [826] -0.112930102  0.880730276  0.974211982  0.496637880  0.966947593
##  [831]  1.178176466  0.734516171  0.290806758  0.090083113 -0.065880335
##  [836]  0.072326711  0.424038440  0.600834718  0.803946095 -0.260153101
##  [841]  1.123566727  0.311730254  1.021804901  0.113353993  0.363820285
##  [846] -0.376774481  1.373492148  0.472457142  0.553677241  0.626521085
##  [851]  1.596133931  0.283078096  0.412088924 -0.347045387  2.403414167
##  [856]  1.253875844 -0.525964615  0.746310166  1.288058642  0.657746621
##  [861]  1.272783841  0.706677658  0.604010796 -0.686860872  0.119915088
##  [866]  0.580916291  2.103272165  1.269741619  1.018675792  0.309349575
##  [871]  1.644744975  1.054805238  1.320802107  0.845568149 -0.175309839
##  [876]  0.558018432  0.344519646  0.941888443  0.600048915  0.961446388
##  [881] -0.066911741  0.369443565  0.515030497  0.137780948  0.102231480
##  [886]  0.728332478  1.254376846  0.539331043  1.630943184  1.297171409
##  [891]  0.201856008  0.668502714  0.142958548  0.268820761 -0.175485258
##  [896]  0.874875962  0.086507247  1.091011152  0.459613689  0.708494155
##  [901]  0.616483747  0.065673448  0.592422527  1.409203691  0.731754700
##  [906]  0.550215448  0.270033453  0.554102933  1.014429897  0.098187950
##  [911] -0.456943268  0.473961604  0.301298361  1.177413363  0.224105377
##  [916] -0.106001105  1.748453094 -0.599942314  0.943969042  1.327680566
##  [921]  0.394669285  0.252060792  0.600804675  0.664969806  0.980283452
##  [926]  0.619538254  0.667411737  0.092723486  0.241980448  0.488823691
##  [931]  0.558650498 -0.430383140 -0.426610665  0.890894586  0.488222445
##  [936]  0.655374241  0.666226201  1.348468667  0.577163909  1.157167336
##  [941]  0.005031131  0.170489147  0.303663751  1.965827900  0.247745798
##  [946]  1.275562807  0.144833500  0.367642187  1.098043528  0.488276794
##  [951]  0.153131639  0.745042677  0.402766495  2.000195164  0.895138866
##  [956]  0.610933260  0.833064511  1.319201869  0.892164806  1.212618116
##  [961]  1.204871995  0.543006241  0.968480902  0.821274950  1.016119678
##  [966] -0.091093109  0.778766101  0.242806417  0.655511881  0.216643721
##  [971]  0.144241347  0.544175723  0.782005492  0.418155933  0.709806660
##  [976]  0.638690235  0.135805858  0.236442201 -0.172341126 -0.547430874
##  [981]  0.382711555  0.305352743  1.508011851  0.418877678  0.216395401
##  [986]  0.232930584  0.621735510 -0.155958879  0.584736127  1.031226677
##  [991]  0.237736654  0.067593928  0.678725693  0.806830269  0.893847872
##  [996]  0.745225350 -0.051636107  0.598096752  0.922171522  0.233711949
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
##       mean          sd    
##   0.58945093   0.32773412 
##  (0.10363863) (0.07327974)
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
## [1]  0.6079320 -0.5895159 -0.6994234 -0.1812479 -0.2541770 -0.5187905
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
## [1] -0.0091
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9357365
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
## t1*      4.5 0.02552553   0.8981718
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 5 6 7 9 
## 1 1 2 2 1 1 2
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
## [1] -0.0047
```

```r
se.boot
```

```
## [1] 0.9157109
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

