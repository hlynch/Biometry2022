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
## 0 1 2 3 4 9 
## 1 1 1 3 1 3
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
## [1] -0.0135
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
## [1] 2.780528
```

```r
UL.boot
```

```
## [1] 6.192472
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
##    [1] 3.9 6.1 3.1 5.3 4.1 3.7 3.6 3.4 2.9 3.6 2.7 3.1 4.0 4.4 5.0 4.2 5.4 3.6
##   [19] 4.5 4.3 4.7 4.4 6.6 4.5 5.3 4.9 5.7 4.6 4.9 5.4 4.1 4.5 4.8 4.6 4.1 4.8
##   [37] 3.6 5.4 3.4 4.1 3.0 4.1 4.6 3.8 4.0 3.9 5.8 4.5 5.6 3.0 5.3 2.9 3.6 5.4
##   [55] 3.0 5.4 3.3 5.2 5.2 4.8 6.3 4.0 3.9 3.6 3.9 3.1 5.0 5.0 5.1 4.0 3.8 6.8
##   [73] 3.3 5.6 4.7 3.6 5.2 4.1 4.4 5.1 4.3 5.9 5.6 4.1 5.2 4.3 5.3 2.9 4.7 4.3
##   [91] 5.1 5.6 1.7 3.1 3.8 5.3 4.5 2.7 6.6 4.4 3.6 5.7 2.8 5.7 4.0 3.7 5.2 5.0
##  [109] 5.1 3.8 4.5 4.9 4.4 4.9 3.1 5.6 2.4 4.4 4.0 4.6 3.4 5.8 4.7 4.9 4.8 4.0
##  [127] 3.5 5.8 5.3 3.1 6.0 3.2 5.6 5.7 5.1 4.1 5.9 3.7 2.8 5.1 4.0 4.1 4.6 5.8
##  [145] 5.1 1.6 3.5 4.8 4.6 4.8 4.9 4.6 4.2 3.6 5.3 4.2 4.9 4.3 4.9 4.1 3.9 4.9
##  [163] 4.8 4.4 3.6 4.4 4.7 4.6 3.9 4.0 4.3 3.8 4.9 5.3 4.8 4.2 5.3 4.0 4.4 4.9
##  [181] 5.9 4.1 4.3 4.8 4.8 5.7 5.1 5.7 4.6 3.4 5.2 5.3 3.2 3.7 3.3 3.7 4.5 5.6
##  [199] 5.6 4.1 4.6 4.3 4.4 5.5 4.9 4.8 3.9 3.9 4.8 4.2 4.5 4.6 3.5 3.9 2.9 6.5
##  [217] 4.3 5.2 3.6 4.9 6.1 3.4 4.2 5.1 3.9 4.7 5.8 5.3 4.7 5.0 4.5 6.1 4.0 4.5
##  [235] 4.1 5.0 4.5 3.7 3.2 6.5 2.8 2.9 3.9 5.0 3.7 5.1 4.5 5.1 4.5 5.2 5.6 4.8
##  [253] 3.1 5.0 3.4 5.6 4.1 3.7 2.3 2.9 4.0 3.6 4.5 4.3 4.0 5.6 5.4 6.6 4.4 3.5
##  [271] 2.4 4.2 3.6 4.1 3.4 3.1 5.8 4.3 5.5 5.1 6.4 3.0 4.8 3.9 5.1 6.5 4.7 5.4
##  [289] 5.1 4.4 5.4 5.1 5.0 2.7 5.8 2.8 4.3 4.6 3.7 3.4 4.4 4.0 4.2 5.3 4.6 4.4
##  [307] 4.3 5.1 4.7 3.9 4.8 4.9 4.0 4.8 4.9 3.5 4.7 4.3 3.4 4.4 3.0 5.0 5.5 2.8
##  [325] 4.8 4.2 5.7 5.1 4.2 4.4 3.9 3.3 5.3 3.1 4.2 5.3 4.4 4.9 6.4 5.2 4.6 4.1
##  [343] 5.3 4.8 5.8 4.9 4.1 5.2 4.5 5.1 5.0 4.2 2.3 5.7 4.6 4.2 4.3 4.6 5.9 5.1
##  [361] 3.5 3.8 4.1 4.0 3.9 5.2 5.7 3.6 3.7 3.5 1.9 5.2 4.3 6.9 4.1 5.1 4.7 5.8
##  [379] 3.9 3.8 3.8 4.6 4.9 4.9 3.0 5.5 5.5 5.2 6.4 4.3 3.4 3.0 5.8 6.1 2.5 5.2
##  [397] 3.3 5.1 3.5 4.5 4.1 5.1 3.3 3.6 5.7 5.6 4.9 2.9 3.7 4.7 4.9 4.1 5.5 4.0
##  [415] 4.8 5.8 5.0 6.2 6.0 3.5 4.4 5.6 5.5 4.8 4.8 6.3 6.5 3.5 3.8 5.3 3.9 5.0
##  [433] 4.6 5.6 4.4 3.7 5.0 3.3 4.7 4.7 3.7 5.4 5.4 4.1 3.9 4.9 4.3 5.5 3.8 4.6
##  [451] 4.8 4.4 4.8 5.6 4.6 4.0 5.2 4.3 5.5 4.8 4.0 4.8 3.7 4.1 3.4 5.4 5.1 4.0
##  [469] 3.5 5.4 4.0 3.9 3.0 4.1 5.8 3.7 4.6 3.6 4.8 4.4 3.7 4.9 4.9 4.6 5.1 5.2
##  [487] 4.4 4.7 4.1 6.3 4.3 2.9 3.8 3.7 4.6 4.4 4.1 2.5 3.5 4.6 5.1 5.0 6.0 2.3
##  [505] 3.2 5.8 4.4 5.6 3.5 4.1 3.3 4.3 5.2 4.5 4.9 5.7 6.1 3.7 5.5 3.4 5.3 4.2
##  [523] 5.6 5.7 4.1 4.4 6.0 4.5 4.5 5.5 4.9 4.0 4.3 6.0 4.9 5.1 3.8 4.5 4.8 3.3
##  [541] 5.1 4.4 5.5 4.4 4.9 2.7 5.6 4.4 5.4 4.6 4.0 3.9 2.9 4.2 5.6 4.9 4.5 5.3
##  [559] 4.3 4.1 4.8 4.2 3.7 4.4 6.0 4.1 4.9 5.8 5.5 4.9 4.8 5.1 4.4 4.3 3.0 4.9
##  [577] 4.4 3.7 3.5 4.9 3.2 3.4 4.9 5.4 4.5 3.7 4.6 4.9 5.1 6.1 4.4 5.6 3.5 5.6
##  [595] 5.1 5.3 4.9 3.9 4.4 3.9 4.0 4.1 3.6 5.8 3.5 3.9 3.4 4.5 3.2 3.5 5.3 4.3
##  [613] 5.0 5.1 4.1 4.0 5.2 4.6 3.3 6.3 4.9 3.6 4.0 2.3 4.4 5.2 6.5 6.1 3.6 4.9
##  [631] 3.2 4.5 2.8 3.3 4.2 3.4 4.2 5.3 4.1 2.4 4.3 4.7 2.2 4.0 4.1 5.2 5.7 5.2
##  [649] 4.2 4.1 4.9 2.9 4.7 5.5 4.4 3.3 5.2 5.0 3.5 4.3 3.7 5.9 4.1 3.8 4.5 4.3
##  [667] 4.6 4.9 4.1 3.9 4.0 5.2 3.2 5.9 5.7 5.8 2.9 4.3 5.0 3.2 3.2 5.1 4.8 4.3
##  [685] 4.8 5.1 5.0 5.8 2.6 4.5 4.8 5.5 5.3 4.8 4.5 3.7 5.7 6.1 3.9 4.6 2.9 5.6
##  [703] 4.4 4.2 3.9 3.3 5.1 5.6 5.4 3.8 3.0 6.5 4.1 4.0 4.2 5.5 3.9 5.6 7.0 4.3
##  [721] 3.8 3.3 3.1 5.0 4.2 3.4 5.7 3.6 4.4 5.9 4.3 5.0 4.6 4.6 4.7 2.3 3.9 5.4
##  [739] 4.9 5.5 5.2 5.5 6.0 3.2 3.0 4.8 3.9 3.9 6.9 3.9 3.3 4.5 6.1 4.0 4.5 4.0
##  [757] 6.7 4.3 3.8 5.6 5.8 5.3 2.2 5.8 3.4 5.5 5.8 4.7 5.6 5.8 5.5 4.4 4.7 5.3
##  [775] 5.2 3.3 5.4 4.7 4.6 4.4 2.9 4.3 6.1 5.3 4.1 5.1 2.8 3.7 3.3 4.7 5.3 3.0
##  [793] 4.0 3.2 4.7 4.5 4.6 4.5 6.6 4.1 5.1 5.4 4.7 5.1 4.8 3.9 4.7 6.2 3.5 4.8
##  [811] 5.3 4.9 6.1 3.7 4.7 3.7 5.9 4.4 3.3 3.0 5.0 6.5 4.2 6.9 3.4 5.3 4.0 2.6
##  [829] 3.9 3.8 4.5 3.9 4.0 5.2 5.7 4.5 3.6 3.5 5.0 5.0 4.9 4.1 5.3 4.9 4.5 3.4
##  [847] 4.4 4.9 5.7 4.5 2.0 3.6 3.9 5.9 4.1 3.5 3.9 3.8 3.8 4.4 4.4 4.8 4.9 5.6
##  [865] 4.2 2.4 4.1 4.8 5.1 5.6 4.6 4.3 3.6 3.8 3.2 5.3 3.5 4.9 3.7 5.5 3.7 5.3
##  [883] 5.6 6.3 5.5 3.7 3.8 5.0 6.2 2.4 5.7 4.5 5.4 5.0 6.2 5.2 3.9 3.6 2.5 4.9
##  [901] 4.7 4.8 3.2 4.2 5.9 4.7 4.6 3.9 3.5 5.4 4.8 4.4 5.1 4.1 4.0 4.5 4.3 4.6
##  [919] 3.9 4.1 4.9 4.8 4.6 3.5 5.9 6.0 5.6 5.0 4.6 4.2 5.1 5.1 5.6 3.8 4.6 4.4
##  [937] 3.0 3.7 4.3 4.9 4.3 4.5 3.8 4.6 4.1 3.5 4.0 4.7 3.9 4.4 5.0 5.1 5.5 4.7
##  [955] 4.5 5.5 5.2 4.9 5.7 6.3 3.0 4.2 4.1 3.7 5.9 3.0 5.7 5.4 4.0 4.7 4.8 4.8
##  [973] 5.5 4.5 5.3 4.8 4.9 4.5 5.7 5.1 4.5 3.4 4.3 4.7 3.4 4.5 3.6 5.2 3.6 5.9
##  [991] 3.0 4.6 3.9 4.7 3.6 5.3 3.7 4.4 3.0 4.5
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
##   2.5%  97.5% 
## 2.7975 6.3000
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
##    [1] 3.2 3.9 5.6 4.4 3.2 4.9 3.8 5.3 5.8 3.9 5.1 6.1 5.3 3.3 5.4 6.1 4.0 4.2
##   [19] 3.2 5.9 5.0 3.9 4.3 4.5 3.7 5.1 4.2 4.6 4.8 4.5 3.8 2.5 3.8 5.6 5.7 4.0
##   [37] 4.1 3.9 4.5 4.4 3.3 3.8 4.8 4.9 4.6 3.9 4.8 4.2 4.5 4.9 5.8 4.0 3.9 5.8
##   [55] 4.9 6.0 4.9 6.9 3.8 4.8 4.8 3.3 3.9 4.2 4.2 2.3 6.0 3.8 4.5 4.1 5.2 2.5
##   [73] 4.9 4.9 4.0 5.2 3.7 4.3 4.6 4.8 3.2 4.4 3.8 4.7 3.4 5.4 3.5 5.6 4.3 4.5
##   [91] 4.3 4.6 5.3 4.9 5.5 5.4 3.0 5.0 3.0 4.9 4.0 4.8 3.2 5.7 5.3 3.2 4.5 4.9
##  [109] 5.3 5.7 4.1 5.9 5.4 5.0 3.3 5.4 4.5 3.9 4.6 4.5 5.3 4.5 3.9 6.3 4.1 4.7
##  [127] 4.2 5.9 5.9 5.2 5.1 4.3 5.1 3.4 5.8 5.9 5.1 3.6 5.8 5.5 5.3 4.3 5.1 4.0
##  [145] 5.2 4.7 5.2 3.8 2.4 2.4 4.5 3.9 5.8 5.0 4.5 3.4 5.9 4.0 4.9 3.4 3.8 4.6
##  [163] 4.5 2.7 4.5 4.8 2.5 4.2 4.1 5.3 3.5 4.9 4.3 4.4 5.9 5.6 4.5 4.0 5.3 4.6
##  [181] 4.1 5.9 5.3 5.1 2.8 4.3 5.5 5.0 3.0 4.5 4.4 3.0 5.8 5.6 4.5 5.0 5.0 3.6
##  [199] 5.5 4.8 5.7 5.9 4.6 5.1 5.1 4.8 5.4 4.7 4.4 4.7 4.6 5.1 4.4 4.0 5.6 5.3
##  [217] 4.8 4.9 3.8 5.4 4.8 3.2 4.5 3.8 4.5 4.7 4.7 4.2 4.6 3.6 3.4 4.8 4.6 3.8
##  [235] 3.1 3.3 3.6 5.0 3.9 5.0 5.2 4.9 4.1 4.8 4.1 4.3 5.0 7.0 3.9 5.0 5.5 5.9
##  [253] 3.2 4.1 3.9 4.5 3.4 4.6 5.0 5.1 4.2 5.1 5.1 3.2 5.0 5.9 3.5 4.3 5.1 6.1
##  [271] 4.5 5.4 4.0 5.3 4.4 3.9 5.8 3.0 2.0 3.7 6.0 4.0 3.6 2.7 5.6 4.5 5.0 3.3
##  [289] 4.2 4.1 4.3 4.7 3.2 3.6 5.0 2.3 4.4 2.0 5.3 5.5 3.2 6.1 5.0 4.2 4.6 3.6
##  [307] 4.0 4.4 5.0 5.8 3.4 3.9 5.2 5.5 5.4 4.2 4.9 6.1 3.8 5.7 3.7 1.8 5.6 3.1
##  [325] 6.6 4.9 2.7 4.8 5.5 2.1 3.2 3.3 5.8 5.0 5.0 4.3 3.6 3.4 4.4 5.2 6.5 3.7
##  [343] 5.1 4.7 3.6 3.8 5.8 4.2 4.1 3.9 3.8 3.8 4.1 3.8 3.6 5.7 5.4 5.6 5.5 3.8
##  [361] 5.4 5.9 4.1 5.8 3.9 5.4 4.6 5.5 2.4 3.8 4.0 5.3 5.2 5.4 3.5 5.0 4.4 3.9
##  [379] 3.0 4.3 4.6 5.8 3.9 3.7 4.2 3.9 3.9 4.6 4.4 4.8 4.6 4.4 4.2 4.1 3.5 5.1
##  [397] 3.1 3.2 3.3 3.5 5.8 5.3 5.0 3.4 4.3 5.2 7.0 3.0 1.8 2.9 3.3 5.5 3.2 4.1
##  [415] 4.6 5.9 5.6 4.7 4.1 3.3 5.5 5.3 5.0 4.7 3.7 5.2 3.5 3.6 4.9 4.4 5.1 4.9
##  [433] 5.3 5.4 6.2 4.5 5.9 4.6 5.6 3.0 4.8 2.8 2.8 4.1 3.2 3.9 3.8 5.1 3.6 4.1
##  [451] 6.0 5.2 5.3 3.7 3.6 4.2 4.9 3.9 3.2 3.8 4.2 4.2 4.2 5.4 6.3 4.4 6.1 4.4
##  [469] 4.6 4.7 5.3 3.8 4.2 5.3 3.8 4.5 3.4 4.7 3.9 4.6 3.2 4.3 5.8 5.5 4.0 4.0
##  [487] 4.3 4.7 5.0 3.6 4.0 5.9 5.7 4.7 3.9 3.6 5.1 4.5 5.7 4.7 4.0 2.5 5.1 3.5
##  [505] 5.6 3.9 4.8 4.9 4.3 5.1 3.8 3.5 6.0 4.4 5.0 5.4 4.1 4.1 4.6 5.2 2.9 6.2
##  [523] 4.4 4.4 5.0 4.5 4.5 5.9 3.6 4.8 5.5 4.6 4.2 3.5 4.1 4.9 5.9 4.9 3.9 4.5
##  [541] 4.8 5.2 3.9 4.8 4.0 5.1 4.4 4.5 6.3 4.5 4.0 5.0 3.0 3.5 4.7 4.6 4.0 5.1
##  [559] 4.3 4.9 5.0 4.2 5.2 3.7 4.8 4.3 5.1 5.7 5.0 4.8 5.6 5.3 4.8 5.5 4.2 3.5
##  [577] 3.5 5.6 4.4 5.2 5.0 5.1 5.6 5.9 4.3 5.5 2.9 5.1 5.4 4.0 4.5 3.7 5.3 5.4
##  [595] 3.8 4.3 5.9 4.9 3.0 4.0 4.8 5.7 4.2 5.0 3.6 5.4 5.3 2.9 4.7 4.3 3.7 5.3
##  [613] 5.8 5.0 4.0 3.2 4.9 4.9 4.3 3.9 5.5 5.8 5.2 4.3 4.2 3.9 3.7 4.4 3.1 4.1
##  [631] 3.4 4.4 4.8 4.9 5.2 4.5 5.1 3.2 4.5 5.6 3.8 5.0 3.8 4.5 5.4 5.4 5.0 3.3
##  [649] 3.8 4.1 4.5 3.2 3.7 4.6 3.7 4.4 6.0 5.9 4.7 4.3 5.9 4.2 4.8 3.1 3.9 3.9
##  [667] 4.1 2.1 5.2 2.7 3.6 5.1 2.7 3.9 6.6 5.2 3.7 3.8 4.5 4.1 5.2 3.8 6.2 4.5
##  [685] 4.1 3.7 3.7 4.5 4.3 3.5 3.0 4.8 4.2 4.2 3.4 6.3 5.6 6.7 4.5 4.6 2.8 4.0
##  [703] 5.4 4.4 4.6 4.1 5.1 4.5 4.0 5.4 5.5 4.6 4.3 4.8 4.0 5.0 4.6 4.3 4.4 6.4
##  [721] 5.1 3.5 3.2 3.3 5.0 4.8 4.0 4.7 3.7 4.6 3.3 3.6 3.7 4.6 4.6 3.4 5.3 4.0
##  [739] 4.6 5.0 3.6 3.4 5.4 4.0 4.4 4.6 5.2 3.7 5.6 4.8 5.6 5.0 5.0 5.5 4.6 4.1
##  [757] 3.2 4.4 3.6 4.3 4.5 2.6 4.6 5.0 5.6 4.1 3.7 3.4 4.6 6.1 6.0 2.9 4.3 5.3
##  [775] 2.9 3.8 4.4 4.2 4.6 5.0 3.4 5.2 5.6 5.7 5.8 5.3 3.9 3.2 4.6 4.2 4.1 5.6
##  [793] 4.8 5.8 4.5 4.7 3.3 4.3 6.0 4.1 5.5 4.9 4.0 5.4 4.4 6.8 4.3 5.3 4.0 4.5
##  [811] 3.9 3.9 5.9 2.3 5.2 3.6 4.0 4.2 4.6 5.9 3.6 3.9 3.8 4.3 3.5 3.5 4.6 6.1
##  [829] 2.5 3.3 5.2 4.2 3.5 4.7 5.0 4.5 4.5 2.9 6.1 3.5 3.2 3.9 4.7 4.1 3.9 5.0
##  [847] 4.6 3.8 2.8 3.7 6.1 5.2 3.3 4.9 4.9 5.5 5.3 4.8 5.1 5.2 5.7 5.6 4.4 4.7
##  [865] 2.9 4.3 3.3 4.7 5.9 4.4 5.6 4.6 4.6 4.1 3.5 4.3 4.3 4.8 3.6 5.3 4.6 3.8
##  [883] 4.2 5.7 3.5 4.7 4.2 4.4 5.1 3.3 4.5 3.4 4.1 5.4 6.0 5.4 4.1 4.0 4.3 3.0
##  [901] 4.3 4.2 3.8 3.7 5.0 5.3 3.8 5.2 4.8 3.4 3.3 3.5 4.8 4.1 6.0 6.4 4.0 5.5
##  [919] 4.6 4.9 3.8 4.0 3.6 5.0 4.3 4.4 3.8 4.6 4.4 3.3 4.1 4.8 4.1 3.6 5.3 5.3
##  [937] 3.5 3.3 4.4 4.0 5.2 5.7 5.4 4.5 4.8 4.6 3.0 3.6 5.0 5.2 4.4 3.3 5.3 5.6
##  [955] 4.3 5.6 4.0 4.4 5.6 3.7 3.5 4.0 5.0 4.6 3.4 5.0 3.1 6.3 4.4 4.7 3.3 5.6
##  [973] 5.0 4.4 4.5 3.3 5.1 4.5 5.1 3.2 5.2 2.8 2.6 3.2 4.8 3.1 4.4 3.2 4.3 5.0
##  [991] 5.5 3.7 4.1 5.4 4.5 3.6 4.0 4.1 5.0 4.5
## 
## $func.thetastar
## [1] -0.0149
## 
## $jack.boot.val
##  [1]  0.45648415  0.32607450  0.26105919  0.15329341  0.03893333 -0.09194030
##  [7] -0.13246377 -0.29519774 -0.33750000 -0.52689873
## 
## $jack.boot.se
## [1] 0.9051571
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
##    [1] 3.6 5.2 3.8 4.2 4.9 2.5 4.4 5.3 4.9 4.4 3.6 5.2 4.6 5.6 4.3 2.7 4.7 5.3
##   [19] 4.3 4.3 2.0 5.1 4.0 6.0 4.3 5.6 4.5 4.1 3.7 4.5 5.3 4.2 5.3 2.8 6.2 3.7
##   [37] 3.5 4.6 4.6 3.6 5.4 5.0 5.1 4.1 5.1 3.4 4.9 3.3 4.2 6.5 5.1 3.2 5.3 6.1
##   [55] 2.8 2.5 5.5 4.5 5.0 5.2 6.3 4.5 5.7 4.2 3.3 2.7 3.0 5.3 4.4 4.7 4.4 4.5
##   [73] 5.0 4.2 4.7 4.4 4.4 5.1 4.1 5.7 4.2 4.1 4.5 2.5 4.7 5.8 7.0 3.5 4.7 3.4
##   [91] 4.5 3.7 4.9 3.4 4.6 5.0 4.6 5.2 5.2 4.7 2.6 3.1 4.3 3.1 4.9 4.9 4.4 6.1
##  [109] 3.0 3.4 3.1 5.1 4.2 4.8 3.2 6.1 5.9 4.3 4.3 3.6 3.9 5.2 4.6 4.3 4.3 3.9
##  [127] 6.0 5.3 6.4 5.0 4.0 5.7 4.1 5.8 4.6 4.8 4.3 4.4 4.4 4.7 4.8 2.9 4.1 5.1
##  [145] 5.1 3.8 4.5 4.7 4.0 4.8 4.1 4.1 5.0 4.6 4.0 4.3 2.0 5.4 4.8 2.9 4.1 3.6
##  [163] 3.2 3.8 4.8 3.7 3.8 4.3 6.0 3.6 3.4 5.5 4.3 3.3 5.8 2.6 6.9 4.7 3.6 5.4
##  [181] 2.7 4.7 3.8 4.3 3.9 5.1 3.9 5.4 3.8 4.5 4.1 5.4 5.0 4.7 3.7 4.4 3.1 4.3
##  [199] 4.6 4.0 5.5 5.2 4.4 3.4 4.0 4.3 3.5 3.9 4.2 5.0 4.3 5.2 4.2 3.6 4.6 6.2
##  [217] 4.1 2.5 3.8 3.7 4.8 4.7 5.0 3.8 5.5 4.0 3.5 5.0 4.0 5.5 4.0 4.6 4.3 4.2
##  [235] 3.7 4.8 4.4 5.1 4.6 4.9 4.6 3.0 4.0 3.9 5.3 4.6 3.9 4.4 5.5 4.3 4.2 4.0
##  [253] 5.2 4.9 4.1 4.8 5.7 3.0 5.1 5.1 5.2 3.5 4.5 3.8 6.8 5.3 5.7 6.2 6.1 4.8
##  [271] 4.3 3.7 4.1 4.2 3.4 4.9 4.2 4.0 4.5 4.6 4.7 5.5 5.2 4.6 4.7 3.5 5.1 4.5
##  [289] 3.2 4.5 4.3 5.5 3.5 2.8 4.7 3.2 5.8 4.4 5.5 5.2 5.6 5.4 4.2 4.5 4.1 3.6
##  [307] 4.9 4.7 3.9 4.3 5.3 3.1 5.5 4.5 4.1 4.0 4.6 3.4 4.1 2.5 3.0 3.2 3.4 2.9
##  [325] 4.9 3.2 5.4 5.6 2.7 3.6 6.0 2.6 6.0 5.4 3.2 3.7 3.7 4.3 4.1 5.0 5.4 3.8
##  [343] 6.2 4.0 5.0 3.6 4.4 5.5 5.3 6.0 4.1 4.9 4.2 4.3 4.5 3.4 5.0 5.0 5.4 4.3
##  [361] 4.1 5.2 5.7 2.7 5.3 3.2 4.5 5.2 6.2 3.6 3.8 3.5 4.3 4.6 6.0 4.9 3.7 6.4
##  [379] 4.8 5.2 5.2 3.8 5.1 4.9 5.6 5.3 4.5 4.7 4.5 3.8 4.1 4.6 5.2 4.3 5.4 4.9
##  [397] 3.9 5.0 3.3 5.5 5.5 4.3 4.5 6.0 4.6 3.9 4.6 5.0 5.3 6.6 3.5 3.2 4.8 5.8
##  [415] 3.5 5.7 6.1 4.9 4.5 4.9 4.9 4.1 5.7 3.5 4.5 4.6 5.6 6.3 3.6 5.2 4.3 4.6
##  [433] 4.7 5.6 6.2 3.7 3.7 5.5 4.7 3.9 4.8 6.2 2.8 5.5 3.9 6.9 4.9 4.4 3.8 5.0
##  [451] 5.3 6.4 3.8 4.1 4.0 5.5 5.1 5.5 4.8 4.6 3.2 4.9 4.9 3.9 5.2 3.4 4.1 5.8
##  [469] 6.2 3.9 2.5 4.1 3.8 3.2 5.6 5.5 4.4 3.9 3.6 2.8 3.9 3.1 4.5 5.5 4.7 4.5
##  [487] 4.8 4.0 5.9 5.2 3.2 5.2 4.8 5.1 2.2 3.7 4.6 4.6 4.8 4.4 5.4 5.0 5.8 4.9
##  [505] 4.3 3.1 5.4 3.4 4.0 4.2 3.7 4.8 5.1 5.1 3.6 4.4 6.5 4.4 2.6 4.6 6.1 5.0
##  [523] 5.2 5.4 5.1 3.7 4.8 5.0 4.3 5.1 4.7 4.0 4.3 6.1 5.2 5.0 5.3 3.1 4.6 4.4
##  [541] 5.5 4.5 3.7 5.0 5.9 4.6 4.9 3.4 3.2 4.7 3.6 4.2 3.6 3.8 3.8 4.5 3.8 4.0
##  [559] 4.3 4.8 6.1 6.2 3.6 3.5 6.1 4.5 3.3 5.4 4.4 4.4 4.3 4.8 1.9 5.2 2.6 5.2
##  [577] 4.2 3.6 3.5 5.1 4.4 5.1 4.9 3.6 3.2 5.6 4.3 4.5 4.6 3.1 3.6 3.1 5.7 2.6
##  [595] 4.2 6.1 3.9 5.8 4.3 4.5 4.2 5.0 5.0 4.7 7.0 2.7 4.7 4.7 4.2 3.7 4.2 3.3
##  [613] 4.1 5.0 2.9 3.7 3.0 4.0 5.3 2.8 5.9 4.6 4.4 5.1 3.7 4.2 4.4 4.0 5.4 5.8
##  [631] 4.4 4.3 4.3 3.9 5.1 4.6 4.7 4.2 5.2 4.8 3.1 5.9 5.0 4.8 4.8 4.6 5.0 5.6
##  [649] 4.2 4.1 4.9 4.3 4.0 4.5 5.0 5.0 3.8 5.3 4.3 4.7 5.1 4.2 4.5 4.5 5.2 4.0
##  [667] 5.7 3.9 5.5 4.9 5.1 4.5 2.4 4.0 3.1 3.1 4.5 4.8 4.5 2.7 5.0 4.9 5.6 4.0
##  [685] 5.5 4.5 3.2 4.6 4.7 6.4 3.5 4.9 5.1 5.4 4.7 4.6 4.9 4.8 2.4 4.0 4.5 3.9
##  [703] 5.3 3.6 2.7 4.0 5.4 4.1 5.8 5.1 3.8 5.9 7.0 3.6 5.2 6.9 3.6 5.3 4.2 3.6
##  [721] 3.6 4.6 5.7 5.4 5.0 2.5 4.3 3.0 4.2 5.4 3.3 6.4 5.1 4.1 4.9 5.2 2.0 4.5
##  [739] 3.7 4.7 4.8 5.3 4.7 3.0 3.8 4.4 3.4 5.2 6.1 3.9 4.6 3.4 4.6 5.3 5.3 3.3
##  [757] 5.4 4.5 5.4 6.2 5.3 4.9 5.5 3.7 5.1 4.9 3.6 3.5 3.6 4.8 3.6 3.7 6.1 5.4
##  [775] 5.2 3.4 4.6 4.0 4.9 5.7 4.2 6.2 4.2 4.6 4.6 5.1 5.3 4.2 4.8 4.5 5.0 5.4
##  [793] 4.9 3.1 4.2 3.9 6.1 4.1 5.5 5.8 3.8 5.2 4.8 4.7 4.2 5.1 5.8 3.5 4.7 4.9
##  [811] 3.6 4.9 3.7 4.5 5.9 4.7 4.4 4.8 5.1 2.8 4.0 4.0 5.9 4.7 4.9 5.3 4.7 4.4
##  [829] 4.7 2.7 5.1 5.2 5.0 2.9 5.7 5.2 6.1 4.0 4.6 3.5 3.3 5.1 4.4 4.4 4.0 2.4
##  [847] 5.5 5.0 4.5 5.3 5.1 3.2 3.3 6.2 6.0 4.9 4.8 6.6 4.7 4.5 4.5 5.2 5.2 4.7
##  [865] 4.2 3.9 3.5 5.0 3.2 3.9 4.4 3.8 3.1 5.8 4.1 5.5 4.1 6.3 5.2 4.7 3.4 3.8
##  [883] 4.3 4.0 4.5 5.6 3.0 3.6 3.1 4.9 4.7 4.6 3.0 4.1 4.5 5.3 3.8 4.4 5.7 3.7
##  [901] 5.0 4.3 4.4 4.4 6.0 5.1 5.2 4.4 5.4 3.4 3.6 6.7 3.3 4.0 4.5 4.1 3.0 5.3
##  [919] 5.0 4.8 5.1 1.7 5.5 5.9 5.7 4.9 5.5 3.9 3.8 6.3 3.4 6.0 6.8 3.1 4.6 5.1
##  [937] 4.2 5.1 2.8 3.7 5.2 3.5 3.1 3.4 3.9 5.7 3.2 3.9 5.7 4.4 4.7 7.1 3.5 4.8
##  [955] 4.2 4.7 4.5 4.6 4.8 4.9 3.3 4.5 5.6 2.8 4.6 4.0 3.2 3.6 4.2 3.4 3.8 3.6
##  [973] 3.8 2.6 4.0 4.1 5.1 5.2 4.0 6.4 3.9 4.7 5.1 4.4 4.8 4.1 3.5 4.6 4.8 5.0
##  [991] 5.6 5.3 5.7 3.6 4.1 5.3 3.4 3.8 5.5 4.3
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.300 5.300 5.200 5.100 5.100 4.900 4.712 4.600 4.600
## 
## $jack.boot.se
## [1] 0.8965984
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
## [1] 0.5392151
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
##      shape       rate   
##   1.9922048   3.5567576 
##  (0.8272006) (1.6781042)
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
## [1] 0.3547752 1.2849363 1.0526949 0.5604734 0.6607864 0.5112805
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
##    [1]  0.2822192535  0.6614495078  0.0933234961  0.7970347778 -1.2184778126
##    [6]  0.6032518489  0.4695546750  0.4787484159 -0.6360017667  0.3092239853
##   [11]  0.1813994480 -0.1598106585  0.6883759690 -0.2127880539  0.2298805760
##   [16] -0.0159018982  0.2801483902  0.8163496294 -0.2307772933  0.3240415293
##   [21]  0.4977996438  0.7053013633  1.5232026340  0.5366998783  1.1196335447
##   [26]  0.2719088022  0.8118695060  1.1591110869  0.2883105827  0.4283295417
##   [31]  0.3568887155  0.0029250922  1.0262913510  0.5060670785  0.0917771586
##   [36]  0.3852823547  0.5836355130  0.1275875631  0.5459821600  0.0819105196
##   [41]  1.3108575307  0.3683560517  0.3910598770  0.6489937490  0.3745392509
##   [46]  0.4893680704  1.0502576976 -0.0215239011  0.4710000190  0.5686579237
##   [51]  0.4736967273  0.2347631103  0.5923609616  0.2786887173  0.5085768094
##   [56]  0.3884790544  0.4701079035  0.6913355979  0.5798655389 -0.2117881189
##   [61]  0.5333426710  0.7787064142  0.9515639993  0.9918359107  0.7680402764
##   [66]  0.3742152119  0.3190586531  0.9523279685 -0.3603993180  1.2138022362
##   [71]  1.7059946693  0.6353646449  0.0067729670  0.1578070568  0.4916700531
##   [76]  0.3412909012 -0.6755456030 -0.0568530889  0.2895709063  0.7394499707
##   [81]  0.7805139774  0.9558248016  0.6225067131  0.2960843204  0.7096024901
##   [86]  0.7922228271  0.0095135098 -0.2449641324  1.0214676699  0.0677858704
##   [91]  0.4766175948  0.5483559385  0.4742469517 -0.6886746411  0.2634358615
##   [96]  0.9659802692  0.8866757008  0.6478192542 -0.2622288088  0.4707431572
##  [101]  0.2445559794 -0.6662216276  1.4484294869  0.7271284150  0.3174138041
##  [106]  0.3020118408  0.6182387873 -0.4916552518  0.5363055739 -0.2047002854
##  [111]  0.2763423661  0.0233519928  0.5275305564  1.7250123841  1.4361571997
##  [116]  0.5866987250  0.7832223073  0.9775107500  0.6455342108  0.4785787099
##  [121] -0.0619283025  0.1691104627  1.1610177435  0.1228818952 -0.0034582818
##  [126]  1.1595460189  0.3907733872 -0.2985054335  0.5265143763  0.1091000249
##  [131]  0.6303180873 -0.3564911401  0.6390688825 -0.0168806549  0.7109459973
##  [136]  0.3186901968  1.3833333041  0.4925086169  1.5147108955  0.3832411278
##  [141]  0.3960257409 -1.5534529913 -0.1318033918 -0.2467079786 -0.0154882864
##  [146]  0.6010974325 -0.0420315134 -0.1324771264  0.7477517702  0.3409212566
##  [151] -0.3002540447  0.2475412775 -0.1269949047 -0.0460566938  0.5019073931
##  [156]  0.1848539154 -0.1662005948  0.9408756253 -0.6668395005  0.6116097880
##  [161]  1.0829021790  0.9322285983 -0.1088345242  0.2296778142  0.6282045339
##  [166]  0.0322564244  0.3455855093  1.0723803095  0.3798531734  1.4982962594
##  [171]  0.5681956446  0.9349732571  0.5860166267  0.6463526232  0.3622102516
##  [176]  0.5695641126  0.3020302344  0.8398187719  0.4464867747  0.4902910459
##  [181]  0.7849914526 -0.0417232253  1.2647783500  0.4965489971 -0.4151691935
##  [186]  0.7157620742  0.6686201131  0.3434029626  1.4933371560  0.0992516474
##  [191]  0.8648373742 -0.3105151807  0.0425243677  1.3130692345 -0.7180300659
##  [196]  0.7371745466  0.8746322524  0.3207989863  0.7281235012  0.5131000871
##  [201]  0.0934196835  1.1603468740 -0.1442429063  0.3772768597  0.6684444112
##  [206]  0.9036607825  0.0518382689  0.6104015285  0.1081116994  0.4545655278
##  [211]  1.0449848985  0.3443432827 -0.2354356450  0.5643317750 -0.3556644439
##  [216]  0.4697380933  0.3710850968  0.4824956196  0.4732488427  0.2866065990
##  [221]  0.3083832926  0.4239038834  0.5058177836  0.2459799816  0.0413221423
##  [226]  0.0128855847  0.8243534561  0.9043725473  0.4625908261  0.4274818736
##  [231]  0.6597775313 -0.0572459658  0.9655968447 -0.1491285118  0.4618038860
##  [236]  0.4733417765  0.4358088064  0.7294545471  0.6257163140  0.6031584931
##  [241]  0.0822653297  0.3792755530  0.6646061215  0.7978081912  0.5194238720
##  [246]  0.3317892100  0.3006590522 -0.0345325710  0.2708340965  0.4340611657
##  [251]  0.8301088346 -0.2325434686  0.2352150672 -0.4201039777  0.1373939019
##  [256]  0.8847242919 -0.8459155730  0.0582458856  0.7682080303  0.3457095794
##  [261]  0.9517492884 -0.3503808471  0.6303180873 -0.5427532136  0.4836726846
##  [266]  1.6684170015  0.7182292752  0.5097617367  0.8909496923  0.9643524534
##  [271]  0.4902910459  0.5529375473  0.4123380290 -0.2405385780  0.1462929481
##  [276] -0.1189159084 -0.0374856905 -0.2280768822  0.0578909751  0.2340448474
##  [281]  0.0800027800  1.3102996353  0.4480634580  0.5843712454 -0.2066592280
##  [286]  0.4407779814  0.9136999395  0.2662669537  0.3760303765  0.1203888503
##  [291]  0.3628388849 -0.3799881254  0.0014550892  0.6516824809  0.4386584554
##  [296]  0.7639336302  0.3498333973  0.2244517344  1.0581568709  0.0433308388
##  [301]  0.2992105135  0.7356360624 -0.2133055641  0.1908362349  0.2377937229
##  [306]  0.9443163269  1.5057751106  0.0814203261  1.0898529045  0.2270258679
##  [311]  0.3264574194  0.4788311476  0.6528590476  0.1191912228  0.2905816186
##  [316]  0.0393143328  0.7655927740  0.8556731468  1.3065870987 -0.1240503287
##  [321]  0.2937509747 -0.1088345242  0.0132847895  0.7621519080  0.9440932818
##  [326]  0.4073935994 -0.3664138535  0.9968301389  1.2006115502  0.5837448653
##  [331]  0.6467604041  0.5044305804  1.1358733314  0.1491158247  0.1088213149
##  [336]  0.7348253148  0.1592668539 -0.4016246912  0.9086646036  0.3008862824
##  [341] -0.1778252417  0.3959232923  0.6881795597  0.3665884248  1.0449848985
##  [346]  0.1848736272  0.0400327159 -0.9477074904  0.8780813845  1.0892114676
##  [351]  0.6145453976 -0.2893222548  0.8376253145  1.1497468645  0.5754661727
##  [356] -0.1242560157  0.4227917264 -0.0493448871 -0.0498763088 -0.4750311680
##  [361]  0.5821629060 -0.0421172446  0.7095243193  0.5625193267 -1.2184778126
##  [366] -1.0902423255  0.4101254462  1.1952348207  0.7797829763  0.1997736354
##  [371]  0.0905837181 -0.8109152946  0.1934522417  0.4982667566 -0.5365257514
##  [376]  0.1894312123  0.4238740453 -0.0258457840  0.7636912800  0.0662501767
##  [381] -0.0703226767 -0.3559768073  0.2375531120  0.6630787330  0.2415829497
##  [386]  0.7318872617 -0.2893322600  0.4448313620  0.6632920930  0.2963428265
##  [391]  0.6230016106  0.2363760502  0.6083494301  0.0215526308 -0.0226029628
##  [396]  1.3364352908  1.1880837483  0.5307396967  0.4023289235  0.0322524149
##  [401]  1.4379650986  0.1391253905  0.0635617862 -0.8570344394  1.0016942427
##  [406]  1.0491258757  0.8805860569  0.0244559673  0.0801616680 -0.4396991577
##  [411]  1.2162538885  0.5719342356  0.2717389411  0.2483221357  1.1811702024
##  [416]  0.3958880922  1.6033004229  0.5866987250 -0.0339834547  0.0486417438
##  [421] -0.0537472621  0.7562154402  0.5315926388  1.0517869372  0.4715455466
##  [426]  1.3113020458  1.0807683169  0.5619957902  0.3949396561 -0.0491792709
##  [431]  0.6651889411  0.0394283406  0.1182274480 -0.4580032180 -0.1401264122
##  [436] -0.0553655708 -0.0383315863  0.1260626316  0.4784287858  0.1694510438
##  [441] -0.4191611251 -0.2002082789  0.4649365527 -0.1181124928  0.6847367522
##  [446]  0.3124764658  0.6754554742  0.3642026419  0.9488337266  0.6730721367
##  [451]  0.6155504063  0.2202006923  0.9223674873  0.4028567375  0.4582657771
##  [456] -0.0238646808 -0.8636133429  0.8540723376  0.2574031132 -0.0694181344
##  [461]  0.6287514339  0.1847745868  0.0041014282 -0.0007681818 -0.4224232748
##  [466] -0.0530650125 -0.6486282266  0.5461517811  1.1595460189  0.5463996270
##  [471]  0.1240503827  0.6313599725 -0.0747107769  0.5332380185  0.7100540371
##  [476]  1.1811702024  0.7313164275  0.9212194231  0.5138796621  0.4812508225
##  [481]  0.6376283363 -0.3884284721  0.8927618961  0.8848739411  0.9387164754
##  [486]  0.6992221025  0.4076941610 -0.7977265639  1.3019186061  0.1492281070
##  [491]  0.4483706997  0.2345293244  0.1516684285  0.1776304685  1.5603578006
##  [496]  1.1862273571  0.2938258770  0.6536240161  0.7257668832  0.7874030960
##  [501]  0.3505852084 -0.1854884948  0.5144976927  0.2204612924  0.3834382652
##  [506] -0.1369915834  0.3115134870  0.6101682585 -0.5945822178 -0.1926038041
##  [511]  0.8236920018  0.2125583176  0.2235559260  0.3252838923  0.4790506679
##  [516]  0.3959232923  1.1556002726 -0.8894945788 -0.0661646714  1.4395920704
##  [521] -0.2135058379  0.9491862303  0.7503309794 -0.0125239521  0.1412359335
##  [526]  0.4020152674  0.6662948797 -0.6889250959  0.7915834796  0.3188640013
##  [531]  0.5752836255  1.0271338722  0.9145952794  0.2548283480  0.5873480694
##  [536]  0.5469485547  0.8729844536  1.0342345923  0.7471823318  1.7617763862
##  [541]  0.5175978956  0.1049739646 -0.0779186938  0.3603051010  0.5467127341
##  [546]  0.0391244918  0.1331414984  0.1376195279  0.9231583614  0.1168357324
##  [551] -0.0572843792  0.8679318892  0.8772471024  0.6410513320  1.0865390409
##  [556]  0.8168586393 -0.7188515293  0.0422064226 -0.2416807558  0.2479633435
##  [561]  0.3827937609  0.6007895836  0.8216586137  0.4246626233  0.5784856213
##  [566]  0.0414902364 -0.8760058384  1.5209401366  0.9400547920  0.1779786363
##  [571]  0.0744440394  0.6346153890 -0.3466702928  1.1619515703 -0.2260753124
##  [576]  0.5829786396  0.2796376285  1.0793503605  0.6663480003  0.5137669637
##  [581]  0.9481100157  0.1634097367 -0.3014428513  1.2006870274  0.9017016893
##  [586]  1.1213790403  1.2094940804 -0.5616077058 -0.0099779621  0.3167981717
##  [591] -0.2950119367  0.7493382579  0.2631248458  0.7536641262  1.3718786110
##  [596]  0.3397765503 -0.7633537575  0.1885510301  0.2785487569  0.4506274321
##  [601] -0.6861337065  0.0987872838 -0.0077898172  1.3839796359  0.1674610163
##  [606]  1.0335585972  0.5288499530  0.4449601944 -0.2087778203  0.1347074051
##  [611]  0.8713306367  0.6839315631 -0.5224053977  1.0323467783  0.6602840665
##  [616]  0.9325326086 -1.0731454254 -0.9857701345  0.3042473331  0.4704309029
##  [621]  0.2694455234 -0.0343653176  1.6035870912 -0.0557363793  0.8428700717
##  [626] -0.2434873422  0.4209706581 -0.2916061910  0.3937016323  0.1704689816
##  [631]  0.9852374196  0.9047885916  0.4020574454  0.3371931703  0.4550881745
##  [636] -0.1491180927 -0.1214994220  0.8877230010  0.5336419910  0.1695211287
##  [641]  1.1610177435  0.8133026687 -0.1174847851  0.2211252215 -0.2432174890
##  [646]  1.1093106216  0.1940481543  0.2167552912  0.1456533124  0.0730056907
##  [651]  0.3647460384 -0.0045454829  0.7989362176 -0.3662717987  0.5608544670
##  [656]  0.2646987644  0.9516302609  0.4722075462  0.5592835906  0.0487889917
##  [661]  0.1103565245  0.2473181367 -0.1858437160  0.4854885151  1.6425420273
##  [666]  0.8031382187  0.3311476264  0.3642490425 -0.8659332964  2.1185091685
##  [671]  0.9640063992  0.4006827321  0.1772459342 -0.1882562048 -0.7163870620
##  [676]  0.2310851072 -0.7083875564  0.4283174950  0.5568457019  0.6566249412
##  [681]  0.2260971532 -0.3846304307 -0.3236791720  0.0445085922  0.8640376346
##  [686] -1.1142070664  0.3291846455  0.5175998537 -0.7162146457  0.4088845316
##  [691] -1.0353203667  0.4011557275 -0.2335147474  0.9209362343 -0.4275263478
##  [696]  0.2865445219  0.3086036112 -0.0785244470 -0.3664199448  0.2824573809
##  [701]  0.2407784400  0.0569682176  0.2968920124 -0.0213480396 -0.2542311041
##  [706]  0.9316675492 -0.2043006851 -0.4594745128 -0.1263816728  0.8411956560
##  [711]  0.5341643731  0.2479022749  0.3783237575  0.6605061876  0.1664189752
##  [716]  0.4309042389 -0.4954916068  0.7226689434  0.2250408882  0.5264228601
##  [721] -0.3645600980 -0.1302391008  0.8614119275  0.4654637331  0.8376446554
##  [726] -0.1319790185  0.4386584554  0.2730295162 -0.3542829947 -0.4049433513
##  [731] -0.5132277376  0.8031780905  0.3713627326  0.3340181636  0.5418726744
##  [736]  1.0825133604  0.9791196448  0.0031006457  0.5764732253  1.4812357011
##  [741]  0.3328993183 -0.0729999069  0.6567622279 -0.0234834944 -1.3519901216
##  [746]  0.0526307496  0.0032295635  0.2375011631  1.5779926769  0.6627973535
##  [751]  0.7147208739  0.2530188098 -0.3684485835 -0.1850931178  0.8116044869
##  [756] -0.1994185670  0.6315632413  0.6368553609  0.4177623318  1.1927288478
##  [761] -0.8407672979  0.9501247038  0.2416976585  1.1021330085 -0.2781224437
##  [766]  0.5461464238 -0.0785244470  0.4273014688  0.5394859072  0.6051370639
##  [771]  0.9569315498  0.6967488293  0.6998396584 -0.7224743030 -0.0791236750
##  [776] -0.2608594218  0.2754758462  0.5598001725  0.9016048114  0.2650270768
##  [781]  0.6067394984  0.2601997740  0.9289603774 -0.0806638958  1.5767430634
##  [786] -0.1229158065  0.2747399950  0.0911133449  0.1380102108 -0.2934889666
##  [791] -0.0565820287  1.2261596516  0.0977388099  0.4121852578 -0.9968704770
##  [796]  1.6396553528  0.3950806322 -0.2543274001 -0.7871115934  0.2632486812
##  [801]  0.1313029740  0.9446083196  1.2497500913  0.2894945091  0.0301153337
##  [806]  0.2398566770  0.6422531135  0.3222534707  0.5093963524  0.1283604409
##  [811]  0.1270362076  0.7493382579  0.7707818185  0.1196062368  1.8634348972
##  [816]  0.5960544578  0.5520822576 -0.0353615162 -0.2511985056  0.6371439045
##  [821]  0.4668934534  0.0786851289  0.2062821320  0.0345165529  0.0630259280
##  [826]  0.5336410292  0.3035201535 -0.1330661389  0.1384426510  0.4333432920
##  [831]  0.4086597459  0.5636205395  0.6610075276  0.5793612912 -0.0640853503
##  [836] -0.2412274458  0.7467069923  0.4736491691  0.3412265439  0.0921858282
##  [841]  1.8000202880  0.3776495347  0.0058204603  0.1516853527  1.7450834395
##  [846] -0.5490299252  0.4452469178  1.5376486320  0.7642193652  0.4005043437
##  [851] -0.3563054083  0.3419725177 -0.0190691068  0.1093745363  0.5528510035
##  [856] -0.4120044604  0.1983924544  0.4217381896  1.1621920160  0.5754661727
##  [861]  0.5538277801  1.7270180178  1.4920442963  0.7462375711  1.2324496979
##  [866]  0.2032526119  0.1791943045  0.4373623691  0.7233238293  0.9365757755
##  [871]  1.3708264681 -0.2418423260  0.6604711535 -0.9672183345  0.5414150288
##  [876] -0.0721481098  0.1623838971  0.5115677316 -0.0649018039  0.0557996579
##  [881]  0.8688348953  0.8185396942  0.0051802526  0.7071834425  1.0629203048
##  [886]  0.1841248807  0.2376102686  0.0116169593  0.8645419716  0.6810091564
##  [891]  0.3701865041  0.0706919611  0.4316357240 -0.3539903247  0.2083919290
##  [896] -0.2039458199  1.4804766301  0.7070354196  1.6008180709 -0.6209026389
##  [901]  1.0096181050 -0.0749291710 -0.3130732362  0.4925396255  2.1099238960
##  [906]  0.5857554476 -0.3896573334 -0.2046586097  0.4076318145  0.1769215620
##  [911]  0.7741856858  0.2989649159  0.5262444371  0.1976112700  0.0160238989
##  [916]  0.6041428168  0.8939140695  0.5930897145 -0.6044260391  0.5286773826
##  [921]  0.2637170397 -0.1081450840 -0.2542311041  0.1516359551  1.2358497069
##  [926]  0.0232097463  1.0719879175 -0.9224371509  0.7797829763  0.2757309571
##  [931]  0.8221093360  0.0550440625 -0.8077369115  0.8078755382  0.7089836996
##  [936]  0.5312936244 -0.2543274001 -0.0498156604  0.1511231697  0.9040562038
##  [941]  0.7766676029  0.2423407013  0.6649561354  1.4524393208  0.7271403281
##  [946]  0.2883668168  0.5555453210  0.5531933157  0.7629222822  0.3008862824
##  [951] -0.4921537328  0.2097713326  0.1548676199  0.3972236170  1.3614940384
##  [956]  0.4326197318  0.1186716001  1.0320907629  0.8778362898  0.7964797226
##  [961] -0.3212605722  0.2291704059  0.3865536643  0.2411146551  0.4131140445
##  [966]  0.8013637023  0.5671430611  0.5737537836  0.7324523441  0.5695641126
##  [971]  1.0023042737  0.2165825589 -0.0848294233  0.0632574439 -0.4943760906
##  [976] -0.5901984389 -0.9633275581  0.0111139665  0.6059003524  0.2063483668
##  [981]  1.5404058583  0.8866943036  0.7147208739  0.2763239639  0.7366228986
##  [986]  0.2984365021  0.3932534367  0.6417926170  0.7126501453  0.9208863460
##  [991]  0.8877230010  0.4314273540  0.6743962455  0.4867464017  0.2676708151
##  [996]  0.4429014400 -0.2206717747 -0.2282468432  0.7556858068 -0.1589778460
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
##   0.56011801   0.34993346 
##  (0.11065868) (0.07824487)
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
## [1]  0.70035809 -0.11369518 -0.84043995 -0.44802458  0.79684791  0.07043005
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
## [1] -0.0115
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8859586
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
## t1*      4.5 0.01281281   0.8888961
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 5 6 9 
## 1 1 2 1 2 1 2
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
## [1] 0.0125
```

```r
se.boot
```

```
## [1] 0.9105289
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

