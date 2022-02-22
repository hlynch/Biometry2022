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
## 1 2 3 4 5 6 7 8 9 
## 1 1 1 2 1 1 1 1 1
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
## [1] -0.0168
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
## [1] 2.708564
```

```r
UL.boot
```

```
## [1] 6.257836
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.6   6.2
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
##    [1] 5.1 4.7 3.7 4.9 3.2 5.0 3.1 3.5 5.8 4.7 4.8 4.6 5.6 5.1 4.7 4.6 5.3 4.0
##   [19] 3.7 5.3 4.9 4.9 4.5 2.7 4.6 3.8 3.9 2.3 4.2 4.5 4.9 4.2 6.1 5.3 5.6 3.1
##   [37] 5.3 5.0 3.9 5.0 4.8 2.9 5.5 4.5 4.0 5.6 5.6 5.1 5.5 4.3 5.8 3.1 1.7 5.3
##   [55] 4.9 5.0 6.7 4.1 4.4 5.0 4.8 3.4 6.0 5.1 3.9 6.3 5.4 4.5 5.4 5.3 5.5 3.4
##   [73] 5.3 5.3 4.7 4.8 5.6 4.4 4.9 5.2 4.1 4.7 4.4 3.4 4.5 4.7 4.7 3.6 4.7 3.2
##   [91] 4.1 4.2 3.5 4.8 4.8 5.1 4.3 4.1 5.0 3.8 5.4 4.5 3.3 3.9 5.9 5.4 3.2 4.4
##  [109] 6.0 4.5 4.8 5.0 5.6 4.4 4.0 3.1 3.4 4.1 6.2 3.2 4.0 2.4 4.3 4.6 5.2 3.9
##  [127] 3.6 5.4 5.0 4.6 4.6 5.5 4.9 4.9 4.8 3.8 5.0 5.1 4.7 4.9 4.1 3.8 5.2 4.9
##  [145] 4.7 2.3 4.3 4.2 3.3 4.5 5.6 2.6 5.1 4.5 5.8 5.0 3.8 3.6 4.1 3.6 4.9 6.3
##  [163] 4.2 6.3 5.9 6.7 5.2 3.9 4.6 3.5 5.0 5.5 3.9 4.9 5.2 5.2 4.0 3.9 4.9 4.6
##  [181] 5.8 4.3 3.3 4.1 3.0 3.7 4.1 4.7 5.0 3.9 4.0 4.0 4.1 4.5 3.4 5.6 4.7 5.1
##  [199] 4.4 3.0 5.1 6.0 3.2 5.1 4.9 5.2 4.7 4.8 5.0 4.3 4.8 4.4 5.0 4.4 4.5 4.3
##  [217] 4.6 6.3 5.1 4.2 2.9 6.1 4.3 4.0 3.1 4.4 3.1 4.8 4.4 6.3 4.4 4.5 6.6 4.8
##  [235] 3.5 4.8 5.4 5.3 5.5 4.1 3.9 3.5 4.3 6.0 5.6 3.9 4.6 4.5 4.3 5.8 5.8 5.3
##  [253] 3.9 3.5 3.1 5.0 5.1 4.6 5.4 4.7 4.7 5.3 5.0 3.2 4.8 5.3 3.2 5.4 6.1 5.5
##  [271] 4.2 4.1 5.0 6.3 5.6 4.8 4.4 2.5 6.0 3.6 6.2 6.4 5.4 3.8 4.6 4.6 4.7 4.9
##  [289] 5.2 4.7 5.1 4.9 3.9 4.1 3.2 4.9 3.0 7.3 2.2 3.0 3.0 4.1 4.9 3.9 5.1 5.1
##  [307] 3.2 4.0 4.1 3.1 4.3 4.6 5.9 3.8 3.0 5.6 5.4 4.0 3.0 3.5 3.9 2.7 3.9 5.4
##  [325] 5.2 4.8 4.4 6.0 3.7 3.9 3.3 4.2 4.4 2.5 4.4 4.5 3.6 5.1 5.6 3.8 5.2 3.5
##  [343] 4.0 5.5 2.7 5.1 4.5 4.0 5.0 5.4 4.9 6.1 4.9 4.8 4.9 2.7 3.1 4.4 4.3 3.0
##  [361] 4.6 4.9 4.3 4.4 3.8 5.3 4.5 6.5 4.3 2.7 3.5 4.8 5.5 4.5 4.9 5.3 4.4 4.2
##  [379] 5.1 4.7 4.7 3.3 4.2 4.2 4.9 4.6 4.6 4.7 4.9 3.9 5.3 4.5 5.4 5.4 6.2 5.4
##  [397] 6.7 4.5 6.0 5.3 5.7 4.6 2.7 4.2 4.9 4.6 5.3 4.2 5.0 4.6 4.7 5.4 4.5 4.2
##  [415] 4.7 5.4 5.5 4.4 2.7 4.2 4.0 3.0 3.8 5.5 5.4 3.9 5.1 4.1 5.9 4.5 5.0 3.7
##  [433] 4.0 3.0 5.9 5.3 4.6 5.3 4.7 4.1 5.6 5.3 4.6 3.2 6.0 4.3 5.1 5.3 2.1 3.9
##  [451] 5.2 5.6 5.2 5.6 5.7 5.0 4.3 5.3 5.5 4.3 5.3 4.9 4.0 5.0 3.9 5.2 3.3 4.9
##  [469] 4.4 4.1 5.0 6.1 3.9 5.1 5.8 3.0 2.8 4.4 5.6 3.9 3.9 3.4 4.8 4.9 3.9 4.9
##  [487] 5.4 4.4 3.7 4.6 4.6 5.3 3.7 5.5 3.7 3.0 4.8 4.8 4.6 4.8 4.6 3.4 5.9 6.0
##  [505] 4.5 4.3 3.2 5.8 5.1 5.7 3.9 6.2 4.8 4.4 4.3 5.0 4.8 4.0 5.1 5.8 4.9 5.3
##  [523] 4.0 5.9 3.3 5.0 2.8 5.3 4.7 4.6 4.4 3.1 4.3 4.5 3.6 4.3 3.5 4.6 4.7 4.5
##  [541] 6.3 4.1 4.2 5.0 3.6 6.6 4.0 4.5 5.7 3.1 6.2 3.7 6.3 3.4 3.5 3.3 2.8 5.6
##  [559] 4.5 4.7 4.7 4.5 3.4 4.9 4.7 5.0 3.3 4.8 5.5 3.8 3.2 4.4 2.8 3.3 4.2 4.9
##  [577] 4.1 4.8 6.6 5.3 3.9 5.7 5.2 4.5 5.1 3.4 6.2 4.1 5.5 5.2 3.5 4.2 6.3 2.9
##  [595] 3.8 5.4 2.7 6.6 4.9 3.4 4.1 5.7 4.7 3.5 5.1 4.1 5.4 6.2 4.2 6.1 5.5 4.3
##  [613] 3.9 4.6 4.6 4.6 3.0 4.7 6.0 4.5 2.6 4.2 3.8 4.2 6.9 4.7 2.9 4.8 2.6 5.9
##  [631] 4.8 5.1 6.2 4.0 4.7 3.8 3.7 5.5 4.1 4.3 4.7 4.3 3.4 3.7 4.5 5.8 4.8 6.4
##  [649] 5.2 4.9 2.9 3.3 6.1 4.0 5.2 2.9 5.9 4.0 4.5 4.1 5.6 3.9 6.2 3.2 4.6 4.6
##  [667] 4.5 4.8 4.9 5.5 4.3 3.5 3.7 3.9 5.4 4.1 4.1 4.2 4.1 4.7 4.3 2.3 5.6 3.2
##  [685] 4.8 6.3 5.5 3.7 4.7 4.5 4.8 4.1 3.5 4.7 4.2 4.1 4.0 4.3 2.7 4.5 3.5 4.1
##  [703] 3.4 4.7 5.5 4.1 3.5 5.1 5.0 5.0 4.0 3.4 5.4 4.0 2.2 4.3 4.1 3.9 4.4 5.7
##  [721] 4.4 3.4 4.6 5.0 3.5 3.7 3.7 5.3 7.8 5.9 5.3 1.9 5.7 6.2 5.6 5.5 3.3 2.6
##  [739] 3.4 5.1 4.7 2.7 3.0 5.1 3.5 4.9 5.6 2.5 5.3 4.5 4.8 4.4 3.3 5.2 3.9 6.7
##  [757] 5.5 4.0 5.2 5.9 3.5 5.0 4.6 4.8 5.0 5.0 4.4 4.5 6.4 4.4 5.0 4.0 2.3 4.3
##  [775] 3.1 3.5 5.9 2.7 4.2 5.2 6.2 6.3 5.9 4.3 4.1 4.0 3.3 4.4 3.5 5.5 3.4 4.0
##  [793] 3.2 5.2 5.0 5.9 5.1 5.2 3.3 6.4 4.4 5.2 3.6 4.6 4.6 3.9 3.1 5.4 4.4 4.7
##  [811] 4.5 3.2 4.0 1.5 3.6 4.2 5.8 5.3 4.7 4.6 5.4 5.0 5.7 6.0 5.3 4.5 5.7 4.0
##  [829] 5.7 4.9 3.4 5.3 6.3 4.8 4.0 4.1 5.0 5.1 5.1 3.1 4.9 4.7 3.3 5.2 3.6 4.5
##  [847] 4.2 3.1 5.8 5.5 5.3 4.7 2.1 5.6 6.9 5.5 4.9 5.5 4.4 3.6 4.8 3.9 4.3 4.8
##  [865] 6.5 4.7 4.1 4.0 5.3 5.4 3.9 4.4 5.6 3.9 5.2 3.9 4.4 6.7 4.9 4.9 5.1 4.3
##  [883] 3.8 4.6 4.4 4.7 4.5 4.8 3.3 3.4 5.7 5.4 5.9 4.3 5.9 4.3 4.2 5.3 5.2 6.6
##  [901] 5.7 4.5 6.4 3.6 4.2 4.4 4.1 5.0 4.3 5.9 3.3 5.8 4.6 3.7 5.6 4.9 3.6 5.3
##  [919] 4.7 4.6 4.7 5.0 5.0 4.8 3.8 3.2 2.1 4.6 2.6 5.0 2.9 4.0 5.3 4.7 6.2 6.0
##  [937] 3.7 3.6 4.0 4.4 3.2 5.8 5.3 6.4 5.5 4.7 6.1 5.4 3.6 3.4 4.9 3.3 3.9 4.1
##  [955] 3.7 3.8 3.7 4.9 4.8 5.3 3.9 3.4 2.8 5.0 3.3 4.0 4.5 4.1 4.0 3.6 4.7 5.3
##  [973] 4.7 5.0 4.3 4.6 4.7 2.4 6.1 5.9 4.5 5.1 4.2 3.6 4.4 5.1 2.5 3.4 4.4 5.5
##  [991] 4.6 4.2 4.8 6.3 5.3 4.0 3.5 3.3 4.6 5.0
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
##   2.7   6.3
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
##    [1] 2.7 5.3 3.8 2.8 3.5 2.0 3.3 6.6 4.4 4.2 4.7 3.7 4.4 3.4 6.0 4.6 4.5 2.0
##   [19] 4.0 4.9 4.9 3.4 3.9 5.1 5.8 4.1 4.6 5.7 2.5 4.2 4.8 5.9 3.4 4.6 3.8 4.3
##   [37] 2.6 4.1 4.6 6.0 5.1 5.6 6.0 2.6 3.2 4.0 4.8 3.9 6.0 6.1 6.2 3.5 2.7 4.6
##   [55] 4.7 4.3 4.6 5.0 4.5 5.6 4.8 5.0 7.1 4.8 4.5 4.6 5.3 4.8 4.2 3.9 3.8 3.6
##   [73] 3.3 5.0 3.6 3.8 3.9 4.0 4.3 5.0 4.1 5.2 4.6 4.1 6.7 4.1 3.8 5.1 5.4 4.7
##   [91] 4.0 5.6 4.3 3.7 4.6 5.1 4.3 4.6 4.9 5.9 4.0 4.4 3.3 4.2 2.8 4.5 5.1 4.3
##  [109] 5.3 3.4 6.3 4.7 6.1 5.3 3.9 4.0 5.3 3.6 4.1 4.0 4.5 6.1 3.9 5.9 4.6 3.9
##  [127] 3.8 3.3 4.2 4.7 3.3 2.9 4.9 3.7 4.7 4.8 4.7 4.7 4.2 4.5 5.7 4.4 3.8 2.7
##  [145] 3.8 5.0 3.8 3.9 4.3 3.9 2.8 4.8 4.4 6.0 4.4 3.6 5.3 6.7 4.9 4.6 4.7 3.8
##  [163] 5.1 4.5 3.9 5.6 4.0 3.6 4.5 3.2 4.1 5.9 4.3 3.7 4.7 5.8 4.4 3.7 4.3 3.5
##  [181] 3.5 3.8 5.2 7.1 3.8 4.2 4.1 4.6 3.6 5.8 4.4 3.3 4.7 4.7 4.5 3.9 4.3 4.2
##  [199] 5.3 4.2 3.8 3.0 4.0 4.2 4.3 4.9 5.5 2.8 2.9 3.9 5.0 3.5 4.8 3.8 5.4 4.1
##  [217] 4.8 4.7 4.1 4.6 4.4 4.5 4.1 3.9 5.9 6.2 4.2 3.5 3.6 4.4 5.5 6.7 4.8 4.4
##  [235] 5.5 5.2 4.0 3.9 3.2 4.4 4.9 3.5 5.2 4.2 4.7 4.7 3.3 6.3 5.2 5.1 4.0 5.9
##  [253] 5.0 5.7 3.8 4.2 4.0 4.6 4.7 4.3 4.5 4.2 4.3 5.4 4.6 4.4 5.6 4.1 5.2 5.5
##  [271] 4.7 4.0 5.9 4.1 4.9 4.1 4.5 4.5 4.3 3.4 5.8 2.2 4.8 4.7 6.0 1.5 4.5 5.7
##  [289] 5.4 3.8 3.3 4.0 4.8 4.3 4.6 5.0 5.0 4.0 5.4 3.8 4.6 5.1 3.2 4.1 4.0 4.1
##  [307] 4.1 4.1 5.4 4.3 3.0 5.2 4.2 4.9 4.0 4.3 5.6 3.6 4.1 6.3 5.4 3.0 4.0 3.9
##  [325] 4.6 6.0 4.2 3.1 3.4 4.0 3.1 3.4 6.0 6.0 2.8 4.6 4.1 4.2 5.5 4.0 3.0 5.5
##  [343] 4.7 5.1 5.1 5.1 5.3 5.1 5.9 6.0 5.1 4.0 4.5 4.3 3.7 4.1 5.6 5.0 4.7 5.2
##  [361] 2.9 3.0 5.0 5.5 3.4 5.4 5.1 3.1 4.3 3.9 3.1 2.8 4.9 3.7 4.7 3.6 5.3 4.7
##  [379] 3.2 3.9 5.9 3.5 4.9 4.1 5.3 5.0 3.3 4.4 4.2 4.0 4.7 4.6 5.8 3.8 4.7 5.5
##  [397] 4.2 5.1 4.4 3.9 4.6 4.2 4.0 3.7 4.8 4.4 4.2 3.9 4.3 6.2 4.2 4.6 6.1 4.7
##  [415] 4.6 3.9 4.2 4.2 3.5 5.7 3.8 5.8 5.1 4.4 5.7 3.4 7.1 4.3 4.3 3.6 5.9 5.3
##  [433] 3.2 5.7 3.5 4.5 3.7 5.0 4.0 4.3 2.8 4.3 4.4 3.7 4.6 4.6 6.1 5.2 4.0 3.6
##  [451] 3.4 4.6 4.8 5.0 4.6 4.0 4.3 4.0 3.9 6.4 5.3 4.4 5.1 5.6 3.3 5.3 4.4 4.1
##  [469] 5.0 2.8 5.1 6.3 4.3 5.4 5.3 6.3 6.7 3.7 3.2 4.9 5.3 4.5 4.5 4.8 4.1 4.2
##  [487] 2.7 3.6 4.7 3.8 4.7 7.3 4.6 5.9 2.5 3.6 4.1 3.7 6.1 4.7 5.0 5.2 5.5 2.6
##  [505] 3.8 6.8 4.7 4.7 6.4 3.0 4.1 3.9 6.4 4.0 5.8 6.2 4.7 4.5 5.4 4.9 4.5 5.3
##  [523] 4.1 4.5 2.9 5.2 4.6 5.0 3.6 5.2 6.3 4.2 6.1 6.0 4.5 3.9 5.5 4.4 4.4 4.2
##  [541] 4.2 4.7 4.2 4.6 4.8 4.7 6.2 5.4 3.6 4.2 5.4 4.1 4.5 4.5 4.0 4.3 4.7 3.8
##  [559] 4.8 4.3 3.2 4.3 4.5 4.2 5.1 4.4 5.5 4.4 4.9 3.9 4.8 5.8 4.2 6.2 5.0 5.1
##  [577] 4.1 4.9 3.8 3.5 3.8 5.6 5.0 3.4 3.7 5.0 4.0 4.4 4.0 4.3 5.8 2.6 3.6 3.4
##  [595] 4.4 4.9 3.0 5.0 3.9 5.9 4.5 4.2 5.0 4.1 4.2 6.2 4.8 5.5 4.6 4.2 5.4 4.1
##  [613] 5.2 3.8 2.4 3.9 5.3 4.3 3.5 4.1 3.2 3.4 4.5 3.9 3.4 4.7 4.3 4.0 4.0 4.7
##  [631] 4.3 3.7 5.3 4.0 3.8 4.8 2.9 3.8 4.5 5.8 5.0 4.4 3.0 5.6 4.9 5.5 4.6 4.7
##  [649] 6.1 5.2 6.5 5.6 4.5 3.7 4.3 4.3 5.8 4.1 4.3 5.6 3.0 3.1 5.5 4.3 4.0 5.6
##  [667] 5.8 4.7 5.2 5.8 3.4 4.5 3.9 4.2 5.0 5.4 4.5 4.6 3.0 5.1 5.3 4.9 4.6 5.0
##  [685] 3.1 4.9 5.3 4.1 5.5 4.5 4.9 6.2 4.2 3.9 5.7 6.5 4.7 3.7 4.1 4.2 5.0 5.1
##  [703] 5.0 3.4 5.4 4.5 4.3 5.2 3.1 5.0 3.7 5.1 4.3 5.1 4.7 4.4 5.7 5.0 2.7 4.5
##  [721] 5.9 3.0 5.3 2.7 5.0 5.0 3.9 4.5 5.2 3.4 4.3 5.9 4.6 3.1 2.7 6.4 6.1 4.3
##  [739] 4.2 4.5 4.5 3.7 3.8 5.1 2.8 4.4 4.9 4.5 4.8 4.8 5.4 4.8 5.0 4.4 6.3 4.4
##  [757] 5.3 4.5 5.4 5.9 5.3 5.5 3.7 4.3 4.8 5.0 6.2 4.4 2.8 3.6 4.5 3.4 4.9 3.9
##  [775] 6.1 4.9 5.7 4.5 3.9 4.1 2.2 5.1 4.5 4.9 3.5 3.4 4.8 6.0 5.0 3.1 3.4 5.7
##  [793] 3.9 3.0 5.5 4.3 3.8 4.6 4.6 5.5 4.0 3.9 4.3 4.3 5.2 5.2 5.0 2.9 5.6 4.9
##  [811] 5.2 3.7 3.7 5.9 4.9 4.4 4.4 3.7 2.5 5.4 2.4 5.0 4.7 3.4 5.2 5.6 4.0 3.0
##  [829] 6.9 3.8 4.8 3.4 6.2 3.5 2.7 4.6 3.8 4.5 4.0 4.5 4.2 4.1 4.7 5.9 5.2 4.0
##  [847] 5.1 4.8 3.0 3.7 4.9 5.5 5.2 4.9 5.0 3.9 3.9 5.5 4.6 3.6 4.6 5.1 6.0 4.4
##  [865] 3.6 5.7 3.7 3.3 3.3 5.0 4.1 3.8 4.3 5.3 4.0 5.8 4.7 3.9 2.4 4.9 5.0 3.3
##  [883] 4.4 4.2 4.4 4.3 4.6 4.4 4.2 2.5 5.8 5.4 3.0 3.2 3.9 5.3 4.0 6.6 4.2 3.6
##  [901] 4.6 6.0 3.7 5.6 3.2 5.3 3.8 4.3 4.5 4.8 5.4 4.3 4.4 5.2 5.1 5.8 1.4 3.5
##  [919] 4.2 5.2 4.1 6.3 2.8 4.5 5.7 4.2 3.6 5.2 3.4 5.2 3.4 6.2 6.0 4.3 4.2 4.7
##  [937] 5.3 5.4 4.5 4.0 5.2 4.8 4.8 5.4 3.1 5.4 4.0 6.0 4.5 3.3 3.7 4.2 5.2 4.8
##  [955] 4.1 3.9 3.1 4.4 5.9 4.5 4.6 5.2 2.9 5.5 3.1 4.1 4.1 2.0 3.5 4.7 4.2 4.1
##  [973] 3.9 5.9 3.3 4.2 4.6 4.4 5.9 3.9 4.2 5.8 4.0 4.8 5.7 4.1 4.3 4.3 5.9 2.8
##  [991] 4.3 3.2 3.9 3.4 3.7 4.4 2.4 4.6 5.7 5.3
## 
## $func.thetastar
## [1] -0.0059
## 
## $jack.boot.val
##  [1]  0.49476923  0.42033426  0.25748503  0.14147059  0.10407609 -0.01925287
##  [7] -0.15123967 -0.24788732 -0.44161677 -0.53716012
## 
## $jack.boot.se
## [1] 0.989016
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
##    [1] 4.9 5.3 4.9 5.0 5.2 5.2 5.5 5.2 4.7 4.7 6.7 3.3 4.8 5.8 2.9 4.7 4.0 5.2
##   [19] 3.3 5.5 5.4 3.8 5.6 4.3 3.2 3.0 3.8 5.3 4.2 5.9 3.9 5.7 2.8 4.3 4.3 4.3
##   [37] 2.2 4.9 4.0 4.9 3.0 4.7 4.9 2.7 5.4 4.9 4.8 3.8 2.9 4.9 5.5 4.4 4.6 4.3
##   [55] 6.2 5.3 3.1 5.9 5.4 4.8 5.4 5.6 4.7 6.8 4.3 4.8 4.2 4.2 2.5 4.2 4.7 3.4
##   [73] 3.6 5.0 4.2 4.1 3.9 6.2 3.2 4.3 5.5 5.0 3.2 5.2 2.4 5.0 4.6 4.4 4.4 5.7
##   [91] 2.6 4.4 4.0 4.7 6.2 5.3 5.3 3.0 4.2 3.2 3.5 4.5 4.3 5.0 5.5 6.3 4.6 4.6
##  [109] 4.5 3.6 4.1 4.4 4.9 6.0 4.4 5.2 5.4 3.8 5.2 4.4 5.2 4.2 3.6 5.4 3.7 5.4
##  [127] 3.0 5.0 4.4 4.5 4.5 5.4 5.5 5.1 3.7 5.1 4.2 4.3 5.4 4.8 4.8 4.7 4.3 4.8
##  [145] 5.0 4.2 5.0 5.0 3.6 4.2 2.7 5.1 5.4 3.3 5.5 4.0 4.8 7.2 2.8 5.3 5.1 4.5
##  [163] 4.7 5.2 4.7 6.1 3.2 4.6 4.8 4.8 4.5 4.5 5.6 1.8 4.3 5.2 2.4 5.3 2.4 4.1
##  [181] 5.2 4.4 1.8 3.7 4.4 2.5 3.5 4.4 4.4 5.1 4.9 4.7 4.2 3.9 3.1 6.0 5.8 6.1
##  [199] 6.6 5.6 4.0 4.5 3.1 4.5 3.7 4.9 5.2 3.6 3.7 3.8 3.8 5.8 4.7 4.6 5.1 4.2
##  [217] 3.8 4.3 5.4 4.1 4.3 5.3 2.9 4.3 2.9 5.0 4.1 5.5 5.4 2.8 4.3 5.7 5.5 4.7
##  [235] 2.8 4.8 7.0 3.4 4.0 3.7 5.5 4.8 4.4 4.2 5.5 5.5 3.3 4.2 5.6 4.9 5.5 5.8
##  [253] 6.3 5.3 5.9 4.4 5.0 6.0 5.8 4.3 3.8 5.1 2.6 5.2 5.5 4.7 4.6 5.6 4.6 4.8
##  [271] 4.8 4.0 4.0 3.9 5.4 6.0 4.8 5.3 4.4 4.3 5.4 5.2 3.9 4.2 4.5 3.6 4.3 3.8
##  [289] 5.2 4.8 5.1 5.2 4.0 4.1 4.9 4.7 4.8 4.0 4.8 5.1 4.7 4.8 4.9 5.3 4.2 4.8
##  [307] 4.6 5.9 4.8 4.8 4.2 5.9 5.3 3.7 3.1 4.3 5.0 3.1 3.3 4.9 4.5 5.4 4.8 2.9
##  [325] 3.5 5.1 4.8 4.2 6.1 4.1 5.3 4.1 4.4 4.7 4.2 4.1 3.6 2.1 4.2 4.0 4.4 4.7
##  [343] 5.4 4.7 4.8 5.4 4.2 5.8 3.7 5.7 5.2 6.0 4.5 4.3 4.0 3.7 4.7 4.0 4.2 4.0
##  [361] 4.0 3.1 4.8 4.6 4.3 4.7 4.7 5.0 4.5 5.7 5.4 4.8 5.1 4.1 6.8 5.0 5.4 4.2
##  [379] 4.6 4.1 4.5 4.4 3.8 4.0 4.1 5.0 3.7 4.5 6.6 5.6 4.6 3.8 3.9 4.6 4.9 3.8
##  [397] 5.4 4.8 4.6 5.9 2.8 4.1 3.6 4.3 4.8 4.9 3.1 5.1 2.8 5.2 4.0 4.6 4.6 3.5
##  [415] 5.5 4.6 5.0 3.2 4.3 4.0 4.4 4.9 5.7 3.8 5.6 4.3 4.9 4.0 6.0 4.3 3.0 3.1
##  [433] 4.7 4.0 4.2 4.6 4.3 4.3 6.4 2.0 4.3 2.7 4.0 4.6 3.1 5.4 4.0 3.1 5.1 3.8
##  [451] 4.7 4.4 5.7 4.7 4.6 4.2 4.7 5.2 5.2 5.8 3.3 5.0 5.4 4.8 5.7 5.5 5.6 4.3
##  [469] 4.5 3.6 4.9 4.2 5.4 4.9 3.6 5.2 3.6 4.6 5.6 5.4 4.7 4.9 5.8 4.2 3.4 4.5
##  [487] 4.5 4.1 4.6 4.9 5.7 5.1 5.0 5.6 4.5 3.8 3.8 3.3 3.2 4.9 5.3 4.5 3.5 5.0
##  [505] 3.4 3.4 5.2 3.3 3.9 3.8 4.4 3.0 5.7 4.1 3.2 5.1 3.0 4.9 3.7 3.8 4.9 5.5
##  [523] 6.2 3.5 5.8 5.6 4.7 4.1 5.0 4.7 3.7 5.0 4.2 5.2 3.9 4.5 4.0 4.9 5.4 3.7
##  [541] 3.0 4.8 3.8 4.3 4.7 4.9 5.0 4.0 4.7 3.4 4.0 4.8 4.6 3.3 3.4 4.5 5.4 3.2
##  [559] 5.4 4.7 6.4 5.7 4.9 3.5 3.6 5.8 5.9 4.7 4.4 5.4 3.3 3.4 2.6 2.7 3.1 5.2
##  [577] 3.0 4.3 5.0 6.3 4.8 4.9 3.9 5.2 3.8 3.6 6.0 4.5 5.3 4.5 3.6 3.0 4.6 6.8
##  [595] 3.5 4.3 3.7 4.7 4.9 5.4 5.9 2.7 3.8 4.3 5.4 4.5 6.0 3.6 4.4 5.3 4.1 2.2
##  [613] 6.8 5.5 3.9 3.1 4.0 3.7 5.3 2.0 4.5 4.8 5.4 4.6 3.2 4.6 4.6 5.1 4.9 4.9
##  [631] 5.1 5.2 3.6 5.5 3.3 5.4 5.9 4.8 5.0 3.5 4.0 4.5 5.0 4.9 2.8 4.1 4.6 4.0
##  [649] 3.8 4.2 3.9 4.1 3.3 5.0 3.5 5.1 5.1 4.3 5.4 4.3 3.7 4.4 3.0 4.7 5.2 4.6
##  [667] 3.7 5.1 3.5 4.6 4.6 3.7 4.7 5.5 5.1 3.7 6.8 3.9 4.3 4.4 4.1 3.4 5.5 5.8
##  [685] 5.3 3.7 5.3 5.7 4.4 5.8 3.8 5.0 4.0 4.5 6.3 3.9 5.4 3.0 5.3 5.0 6.5 4.7
##  [703] 4.2 4.7 4.1 6.1 4.8 4.0 3.7 4.2 4.2 5.7 4.9 3.2 4.2 4.5 5.6 4.7 4.5 6.5
##  [721] 4.5 3.2 3.7 4.2 4.5 3.7 4.5 5.6 3.6 3.6 4.9 3.4 3.7 6.5 5.1 2.8 4.0 3.4
##  [739] 4.2 5.2 3.2 4.1 4.9 1.9 5.2 4.1 6.5 7.0 4.0 4.4 5.3 5.4 4.8 6.3 4.0 4.2
##  [757] 4.9 3.7 4.7 4.4 5.6 3.0 3.8 5.2 2.5 3.4 4.6 5.0 4.6 3.9 4.7 4.8 5.6 5.0
##  [775] 4.7 6.0 4.9 3.1 5.3 2.9 3.7 4.1 3.2 4.1 4.0 4.7 4.0 5.0 4.5 4.6 3.3 4.6
##  [793] 5.5 4.4 4.9 5.0 3.2 6.0 4.4 4.2 4.2 5.1 5.9 4.2 3.2 4.7 3.6 5.4 3.9 4.7
##  [811] 4.2 4.1 3.9 5.0 4.8 5.3 4.0 4.9 4.5 3.3 5.0 5.1 4.6 4.0 5.1 3.8 4.6 3.9
##  [829] 5.1 3.6 5.5 3.5 5.4 4.7 5.1 5.7 4.7 4.9 5.1 4.3 5.0 4.0 5.5 3.1 5.3 4.7
##  [847] 4.0 2.1 3.9 3.6 6.1 5.0 4.4 3.8 4.7 3.0 6.1 3.6 5.5 4.8 5.0 4.4 3.9 3.3
##  [865] 4.4 5.9 4.9 2.9 5.0 3.0 5.7 2.8 4.8 5.3 4.7 3.9 5.9 4.0 5.0 5.9 4.6 3.3
##  [883] 3.7 5.5 4.2 5.1 3.1 3.9 4.3 4.8 4.7 4.1 6.0 6.3 3.0 5.1 3.6 4.2 4.9 4.1
##  [901] 7.3 5.0 3.6 5.1 3.9 2.8 4.9 4.5 4.5 3.3 4.9 4.5 3.3 7.1 6.3 4.1 3.5 1.9
##  [919] 3.1 3.2 4.8 3.9 4.3 3.9 5.6 4.5 2.3 3.4 4.2 5.1 5.8 4.9 2.9 6.6 5.6 4.4
##  [937] 3.2 4.2 4.0 4.3 5.1 5.9 4.0 3.3 4.1 4.4 5.1 3.7 3.5 5.9 4.7 4.0 6.2 5.2
##  [955] 4.4 4.7 4.5 4.2 3.1 5.1 3.5 3.8 3.2 6.3 3.4 2.6 5.2 3.0 4.7 4.4 4.5 4.9
##  [973] 4.4 4.9 4.0 5.7 4.6 5.0 4.1 3.9 5.7 3.3 3.2 3.9 4.7 4.1 5.0 4.8 3.9 3.2
##  [991] 3.6 4.6 4.9 5.2 4.3 5.2 4.1 5.9 4.2 4.4
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.2 5.0 4.9 4.9 4.8 4.7 4.5
## 
## $jack.boot.se
## [1] 0.9178235
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
## [1] 1.152174
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
##   2.692651   6.045794 
##  (1.137515) (2.807250)
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
## [1] 0.6857007 1.1268330 1.0050665 1.1251176 1.7490466 0.2735984
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
##    [1] -0.206919248  0.983806393  0.548373211  0.939003048  0.207284449
##    [6] -0.611920363  1.144158764  1.164899874  0.695792973  1.403496342
##   [11]  1.194859392  0.660252816  0.725862781  1.075439627  1.308910098
##   [16]  0.653743154  1.139861099  0.705724180  0.481269784  1.565952628
##   [21]  1.416024247 -0.176938047  0.970697295  0.457433760 -0.207107446
##   [26]  0.170763371  0.963578455  1.401785566  1.270708537  0.792093979
##   [31]  0.811855879  1.055694600  0.974567816  0.860822387 -0.159573000
##   [36]  1.360060142  0.288296514  1.174795281 -0.166625570  0.478644719
##   [41]  0.563216909 -0.089768211  1.306852158  0.544406315  1.022935547
##   [46]  1.335817004  0.577413258  0.624670086  0.634926590  0.910224114
##   [51]  0.302306682  2.094492156  1.160445597  0.781715210  1.363167353
##   [56]  0.330488319  1.630932877 -0.175456280  1.022687937  0.283753813
##   [61]  1.096466736  0.887143449  0.258992433  0.454944660  1.321519319
##   [66]  0.648802426 -1.033086704 -0.146627441  0.886368284 -0.039977421
##   [71]  1.690515548 -0.139046172  0.675547257  0.437056375  0.605633258
##   [76]  1.821172136  1.292211092 -0.160184206 -0.847820589 -0.061855635
##   [81]  0.446518122  0.898751777 -0.421742458  0.199967482  0.935540003
##   [86]  0.602085475 -0.102056805 -0.903882152 -0.091044544 -0.544118361
##   [91] -0.111592753  0.889308660  1.047663627  0.324671627  1.141012052
##   [96]  0.350286520  1.659928740  1.347303709  0.735226340  1.353100368
##  [101]  1.635565447  0.704765186  0.589529680 -0.219474446  0.693327998
##  [106] -0.552213223 -0.149435515  1.254468234  1.220978038  1.476013470
##  [111] -0.049666839  0.311461298 -0.107731957  0.270143937  1.044622092
##  [116]  1.166237900  1.096422870  2.440693477  0.343516274  0.440418268
##  [121]  0.211286298  1.057437721  0.978415910  0.854567054  1.408195762
##  [126]  0.779329490  0.861492339  0.651732761  0.244213814  0.979977595
##  [131]  0.699008444  1.242317273  0.564393459  1.736144702  0.816913366
##  [136]  0.940630401  1.071519720  0.241193581  0.350554197  0.587931299
##  [141]  0.755906536  2.502696388  1.751149186  0.710001790  0.953799107
##  [146]  0.604975004  1.515806101  1.092283937  1.018314174  0.480802133
##  [151]  0.517001021  2.498053084  0.500280805  1.488223574 -0.388978160
##  [156]  0.258435041  1.130621921  0.601525379  0.905546680  0.762543520
##  [161] -0.244619109  0.946658693  1.152174166  1.100787547  0.897195486
##  [166]  0.201062176  0.249849717  1.213932087  1.358028530 -0.154227310
##  [171]  0.794678475  1.631570463 -0.439745613  1.145235129  1.166019327
##  [176] -0.948139613  0.605149150  1.047123407  0.803681969 -0.147828664
##  [181] -0.141113675  1.513346757  0.826023815  0.690192405  0.691109802
##  [186]  0.260718339  0.793401042  1.714941905  2.016543511  0.708119821
##  [191]  1.189974358  0.829448376  1.162344516  0.935526621  0.793186656
##  [196]  1.035838957  0.924317573 -0.521307869  0.910983203  1.125478681
##  [201]  0.094682092  1.546926823  0.912352759  1.822771119 -0.044080364
##  [206]  0.817357846  1.449264324  1.460684071  0.406366161 -0.412398585
##  [211]  0.592750781 -0.078458940  1.710301632  0.353488734  1.144775912
##  [216]  0.993033975 -0.558743335  1.653142370  0.734884488  0.634319432
##  [221]  1.032780632  0.496117144  2.103557469  0.886368284  0.181822492
##  [226]  0.716242773 -0.098428210  1.220010356  0.562834911  1.382834075
##  [231] -0.534285054  1.395120447  0.766426523  1.366037770  1.336680727
##  [236]  0.225039481  1.065401163 -0.226486595  0.499377383  1.373632094
##  [241] -0.173622290  1.240371761  0.898847241  1.100266167  0.792502338
##  [246]  1.221573856  0.536286278  2.650330344  1.026914370  0.948069174
##  [251] -0.197429072  0.143686536 -0.143364712  2.621412191  0.732053975
##  [256]  0.377790554 -0.988866779  1.162230709  0.140573434 -0.099350999
##  [261]  0.637221478  0.969443624  1.589327725  0.301979247  0.663471687
##  [266] -0.223306088  1.604209791  0.161577496  1.240569346  1.432657018
##  [271]  0.299784443 -0.098684688  1.753445373 -0.194639826  1.153827181
##  [276]  0.834684309  0.833656917  0.682468026  0.871662007  1.309663484
##  [281]  0.263360516  1.106522247  1.030973148  1.111222266 -0.162980417
##  [286]  0.870660654  0.790270502  0.005105360  1.481468079  2.426583439
##  [291]  0.607976318  1.317870120 -0.483447401  0.930015956  0.386739969
##  [296]  0.866723131  0.982388888  0.636437073  1.652147876  0.890409611
##  [301]  1.761281267 -0.191757344  1.240443753  0.865053172  0.824420601
##  [306]  1.337262347 -0.167553938  1.192846248  1.203496192  0.824933084
##  [311] -0.543859279  0.879052627  1.904021118  0.415405409  1.077302503
##  [316]  0.529986623  1.251375224  0.596768467 -1.049017641  0.784457603
##  [321]  1.058208421  0.928734747  0.869454617  1.418544295  1.138471930
##  [326] -2.521663166  1.030173842  0.021634475  1.105043104  0.412222075
##  [331]  0.647870814  0.162420647  1.116152059  0.987880959  1.653142370
##  [336]  0.602323995  1.373573526  1.333381363  0.562782730  0.899266119
##  [341]  1.497053718  0.471258468  1.343174349  1.027419913  1.102104654
##  [346] -0.211056287  1.362653002  1.172188081  0.886551723  0.195861186
##  [351]  1.277750020  0.916541135  0.740099151 -0.164620369  1.488223574
##  [356]  1.498308244  1.260945792  1.092805043  0.569882526  0.222807087
##  [361] -1.417755646  0.712542468  0.798838642 -0.432475063  0.357317029
##  [366]  1.409329896  0.814475866 -0.175124409  0.933521472  1.373456927
##  [371]  0.838301758  0.926204073 -0.501344276  0.664262667  1.131488992
##  [376]  1.205065592  1.253884945  0.280670503  1.413026429 -1.607081923
##  [381]  1.034670886  0.314896032  0.114299594  1.200602685 -0.614667414
##  [386]  1.154432253  1.385736716  1.088374626  0.308364225  1.335492129
##  [391]  1.974745695 -0.901661545  0.414197865  1.067987514  1.255906512
##  [396] -0.116751950  0.729024152  0.335729486  1.223186395  0.356707697
##  [401]  0.883941122  1.059262632  1.155852082 -1.647239517  1.773957416
##  [406]  1.227356205  0.282470111  1.215552008  0.464379372  1.450673646
##  [411]  0.348964341  1.131590149  1.087995983 -0.506772381  1.376815814
##  [416]  1.651097782  0.831323268 -0.628536773 -0.572456389  0.844147125
##  [421]  0.498813225  0.277245221  0.904851576  0.161788906  0.422485102
##  [426]  1.216371775  0.715740145  1.113513022  0.963919596  0.788859314
##  [431] -0.060266740  1.186005190  1.221536934  0.189217458 -0.973545482
##  [436]  0.513468755  0.723585017  1.545166831  0.892403183  1.614718223
##  [441]  0.739936730  1.218703826  0.797147754  0.952038896  0.641414164
##  [446]  0.877479868  0.541998921  0.872857729  0.990484414  0.698510215
##  [451]  0.572721449  1.326152858 -0.023391020  0.933884640  1.082599238
##  [456]  1.283137466 -0.835243364  0.724445368  0.273008358  0.807836911
##  [461]  1.309392309  0.440077416  1.192453924  1.399726864  0.750557510
##  [466] -1.078502848  1.244586041  0.725948512  0.862675942  0.471538690
##  [471]  0.127839291  0.374986729 -0.153875342  1.097450340  0.964747571
##  [476]  1.062783379  1.173675590  0.976753513  0.539607987  0.700811299
##  [481]  0.581965871  1.570315249  0.181539021  1.713900598  0.907447617
##  [486]  1.060875698 -0.381739480  0.860287057  1.297112595  1.207952290
##  [491]  0.275735795  0.125250212  1.155851383  1.585068535  1.893945248
##  [496]  0.528460885 -0.976206908  1.146379103  1.345970333  1.356269952
##  [501] -0.047524091 -0.007301077  0.778884492  1.047889889 -0.137567302
##  [506]  1.240443753  0.599004552  0.524994605 -0.105433330  1.125241073
##  [511]  1.107025712  1.416431875 -0.563962454 -0.175749077  1.114169030
##  [516]  1.006496432  1.158299345  1.277750020  1.331803980  0.751655248
##  [521]  0.382970470  1.165466557  0.157467029  0.890289399  0.928302128
##  [526]  1.324119280  0.475385738  1.133115634 -0.090426025  1.305576706
##  [531]  1.046635139 -0.490217815  1.298790270  0.821992669  0.162353683
##  [536] -0.044080520  1.993295706 -0.420705763 -0.619047992  0.313037158
##  [541]  1.465813334  0.920102730  0.898871492  0.839307597  2.054641183
##  [546]  0.117481659  1.533853868 -0.510678165  1.975217913 -0.537884243
##  [551]  0.602982858  1.115200678  0.724445368 -0.109762840  0.801591885
##  [556]  1.310339842  0.934005227 -0.093697722  0.535078432 -1.626244271
##  [561]  1.150959287  1.183160129  0.731871384  1.003832747  0.982250692
##  [566]  1.019151751  1.392673265  0.794633941  1.041333235  2.012200248
##  [571]  1.104036606  0.644044413  0.316447544  0.632488813  1.284855780
##  [576] -0.887614303  0.876032206 -0.150733578  0.532521563  2.410413586
##  [581]  1.723068544  0.779707595 -0.518913758  1.433022624  1.358835599
##  [586]  1.950974377 -0.861718535  0.565354017  0.918629856  0.123234613
##  [591]  0.839665200  0.932325227  0.744739642  1.025187151  1.246717907
##  [596]  0.852482152 -0.064050657  1.185397442  1.394774100  0.773724711
##  [601]  0.896359050  2.085773173  0.644017237  1.191339777  0.336361536
##  [606]  2.056010104  1.526999537  0.736621254  0.196709649  0.850232448
##  [611]  1.151782249  1.320190479  0.644371207  0.975185200  0.909887204
##  [616] -0.402212988  0.777860073  1.634345430  0.384013302 -0.570339415
##  [621]  1.077438118  0.549980332 -0.143821329  1.229572966  0.004297813
##  [626]  0.419900222  1.389054484  0.162104004 -0.447275249  0.777003372
##  [631]  0.318557025 -0.119424864 -1.579043307  1.395201840  0.600789888
##  [636]  1.369959333  1.174795281  0.181595520 -0.429197955  1.111680006
##  [641]  0.562130559  0.259776062 -0.053117437  0.618605628 -0.187307302
##  [646]  1.033186432 -0.017290118 -2.339560575  1.315070444  0.148481743
##  [651] -0.074538656 -0.128712167  1.151281155  1.160615702  0.344691531
##  [656] -0.620346744  1.391966503  1.063291294 -1.014122518  1.329398926
##  [661] -0.084919595 -0.536988611  0.370950332  1.386719560  1.606601615
##  [666]  0.713877326  1.345001466 -0.425867055  1.009913927  1.526391371
##  [671]  0.203769029  0.982634868  1.448826223  1.124254354  0.924176356
##  [676]  1.476676496  1.041026381  0.883428440  0.772422113  1.966171373
##  [681] -0.204518465  0.875590033 -0.554494163 -0.097637622  0.759438866
##  [686]  0.511481870  2.391435749  0.982181086  1.566610898  1.455005931
##  [691]  0.758374899  0.094231210  1.201856713  0.210097054  1.200322014
##  [696]  0.149601133  0.699995717  0.265111618  0.911585543 -0.551191574
##  [701]  1.069963622  1.014395843  1.696776956 -0.178605528  0.798448331
##  [706]  0.486458659 -0.508542303  0.604389650 -0.045114803  1.437525238
##  [711]  1.185689132  0.673638768  0.362321095 -0.427373802  0.906877144
##  [716] -0.581344574  0.596346485  0.624733163  0.311976049  1.654748462
##  [721] -0.504213560  1.182002009  0.872947002  1.728389698  0.685574897
##  [726]  0.827788301 -0.111750775 -0.605805272  0.518222438  0.835845424
##  [731]  1.981886250  1.103255225  0.320925212  1.007259025  1.192846399
##  [736]  0.794678475 -0.241815920  1.005903308  1.264542080  0.045809983
##  [741]  1.139218058  1.780257242 -1.028745873  0.504264921  0.498057217
##  [746]  0.693115777  0.577386911  0.850961817  1.209921871  1.104718807
##  [751]  0.536635409  1.677719492  1.665143923  0.549412957  1.217821099
##  [756]  0.875028531  0.389465099 -0.406826066  1.094359670  1.639871237
##  [761]  1.032667637 -0.119607185  1.061076912  1.333936065  1.533302807
##  [766]  1.294819117  0.779377157  1.423911416  0.220973677  1.014370620
##  [771]  0.535350676  1.371191493  0.149452571 -0.865627974  1.395201840
##  [776]  0.774849607  1.322899592  0.864335024  0.705363443  1.160534689
##  [781] -0.067860838  1.032148838  1.169675530  0.689928444  0.026138820
##  [786]  0.176883447  1.485442443  0.611812174 -0.879950150  1.343126038
##  [791]  0.876235458  1.125087844  1.479903227  1.028256559 -0.112781387
##  [796] -0.133596048  1.174234016  0.648802426  1.216373373  1.114540953
##  [801]  0.886901577  0.856058972 -0.168058852  0.988870238  1.145789299
##  [806]  1.148638966  1.231217996  1.008240805  1.169037879  1.100984964
##  [811]  1.226659729  1.065497757  0.966928860  1.403644214  0.521479691
##  [816]  0.456275007 -0.560880747  0.127103767  0.668133600  0.282910716
##  [821]  1.064688091  1.635287944  1.412592361  1.694504841  1.503353769
##  [826]  0.638006145  0.576335082  0.746951894  0.914295823  0.726063223
##  [831]  0.498674928 -0.097230372  0.623809218  1.076202969  1.540321286
##  [836]  0.785189470 -0.443951112  1.136727490  1.200695181  0.882817979
##  [841]  0.546412050  0.275722003  0.718200416  0.295002278 -0.092650607
##  [846]  0.185450181 -0.213308960  1.212086148  2.018024361  0.901260827
##  [851]  1.078404189  1.318178134  0.905632324 -0.570114039  0.260009127
##  [856]  0.217677254  1.042048605  0.197261428 -0.084919595  0.217373630
##  [861] -0.501936810  0.195966347  0.746140499  1.292077853  0.661246127
##  [866]  0.419156471  1.152174166 -0.241815920 -0.168463542  0.604027618
##  [871] -0.147794599  1.079894533  1.612543476  2.466754451  1.429537626
##  [876] -0.585879266  1.124559908 -0.157757533  0.671492627  0.806943292
##  [881] -0.908720705  0.602842148  0.647937296  1.571942167  0.118459316
##  [886]  0.704286193 -0.014963336  0.968874018  0.443257923  0.607581206
##  [891]  0.916132678  0.664778121  0.475156662  0.519821137  0.649876672
##  [896] -0.142610966  0.939154570  0.656501376  1.208722656 -1.363861092
##  [901]  0.631280235  0.937754955  0.684338902  0.189461006  0.786586415
##  [906]  1.308213858  1.744159920 -0.560991992  1.170913364  0.682574833
##  [911]  1.086203931  0.392170643  2.063346263  0.919330891  1.053862407
##  [916]  1.420819045  0.940630401  0.717342135  1.259813821  0.854197411
##  [921]  0.881464420  1.585520795  2.448733780 -0.879669461  0.794060690
##  [926]  0.841631926  0.999548876 -0.167487565  1.404246471  1.089091873
##  [931]  0.564938086  0.780820776 -0.538631493 -0.874858723  0.611275086
##  [936]  0.194412568 -0.439684677  0.973828427  1.052549755  0.523838711
##  [941]  0.871069081 -0.180032367  1.637301029  0.841979852  1.059336008
##  [946]  1.100547911  0.777129380  0.589452865  1.403767843  1.187164838
##  [951]  0.181936872  1.066777124  1.030973148  1.052008573  2.098835815
##  [956]  1.235701844  0.891710639  0.906997273  0.064750450  0.924129790
##  [961] -0.506007572  0.292388432  1.320152921  0.213572911  0.893201362
##  [966]  1.388482238  0.996439063  1.254319014  0.634898009 -0.560188991
##  [971]  0.213868987  0.545049714  1.910362701  0.869039895  0.223694869
##  [976]  1.114185353  1.844303371  0.901122331  0.258479054 -0.574516565
##  [981]  0.362461125  1.114970903  1.218268292  1.927754766  1.165120902
##  [986] -0.100790171  1.004559653  0.648491630  1.026563492  1.004240158
##  [991]  1.000417833  0.654849163  0.521113942  0.323985909  0.214629404
##  [996]  0.574232267  0.626484967  1.056093785 -0.544994100  1.439168496
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
##   0.44537444   0.28442439 
##  (0.08994289) (0.06359584)
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
## [1] -0.25477678  0.16948403 -0.14932745  0.65322761  1.12751960 -0.08839328
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
## [1] 0.0033
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9184894
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
## t1*      4.5 0.003303303   0.9311462
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 6 7 8 
## 2 1 1 2 2 2
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
## [1] 0.0229
```

```r
se.boot
```

```
## [1] 0.9241426
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

