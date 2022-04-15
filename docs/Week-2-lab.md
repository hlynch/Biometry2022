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
## 0 1 2 4 5 6 7 8 9 
## 1 2 1 1 1 1 1 1 1
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
## [1] -0.0028
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
## [1] 2.753898
```

```r
UL.boot
```

```
## [1] 6.240502
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.2
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
##    [1] 5.3 4.4 2.9 2.6 5.0 3.7 5.6 4.3 2.8 5.0 3.3 5.4 3.6 3.9 4.0 3.1 4.0 4.6
##   [19] 4.3 4.0 4.6 5.2 3.4 5.1 3.0 4.5 3.6 5.1 5.1 6.5 5.7 4.1 5.2 3.2 5.1 4.2
##   [37] 4.8 5.5 3.7 5.0 3.1 6.2 4.6 2.4 4.6 3.7 4.7 5.5 4.5 5.0 3.9 4.2 5.4 4.7
##   [55] 3.1 4.5 4.2 5.1 5.0 5.0 6.0 4.0 4.0 4.0 4.6 4.6 4.8 2.9 5.6 3.4 3.5 5.3
##   [73] 4.1 2.6 5.3 4.5 4.9 3.6 4.6 4.4 3.0 3.5 4.4 2.9 4.1 4.5 5.5 3.5 4.2 5.2
##   [91] 5.2 3.3 3.8 5.0 4.9 5.4 3.7 3.7 4.5 5.3 3.7 3.5 5.9 4.7 5.0 5.6 6.2 4.8
##  [109] 7.1 3.5 4.5 4.0 6.2 4.2 5.9 3.3 4.5 4.5 3.0 4.1 4.7 4.6 2.3 4.1 5.0 5.0
##  [127] 4.0 4.3 3.4 5.9 4.8 4.5 3.6 4.8 4.9 4.6 4.6 3.0 5.1 3.9 4.6 4.4 4.3 4.9
##  [145] 5.2 6.0 5.0 2.3 4.2 4.6 3.1 4.8 3.1 5.1 4.0 5.2 4.5 5.0 4.9 5.8 4.8 4.5
##  [163] 4.7 4.3 3.4 5.0 5.3 3.2 3.7 4.2 6.3 4.8 3.8 5.5 4.7 4.5 3.8 5.5 6.0 4.6
##  [181] 3.3 4.9 5.0 4.5 3.3 4.5 4.8 3.1 2.6 4.9 5.3 2.9 3.4 4.9 4.1 4.7 4.0 4.7
##  [199] 4.8 6.0 5.3 4.8 5.8 5.0 5.9 3.4 4.2 4.6 4.1 5.5 4.5 6.3 5.9 4.3 4.2 5.0
##  [217] 4.6 5.0 4.8 3.6 4.9 4.0 4.3 5.4 5.3 3.6 3.8 3.2 3.0 2.7 6.2 4.8 4.9 4.8
##  [235] 4.2 4.1 4.0 3.5 6.6 4.9 4.5 4.2 4.0 4.0 5.0 2.4 3.1 4.1 5.1 3.8 6.0 4.3
##  [253] 4.1 3.3 4.1 3.5 2.7 5.4 3.7 6.4 3.8 4.2 4.4 4.3 4.2 4.7 4.7 3.9 5.2 4.8
##  [271] 4.6 5.1 4.6 4.5 4.6 4.2 3.2 5.2 3.4 4.4 4.5 3.6 2.4 3.4 3.5 4.4 6.1 5.7
##  [289] 3.3 4.6 3.8 5.3 3.4 3.7 4.1 5.3 3.4 6.9 4.7 5.0 5.9 4.7 6.6 3.7 4.8 3.9
##  [307] 5.6 4.7 5.6 2.9 4.4 4.0 4.3 5.2 4.0 4.2 3.5 3.5 4.9 6.4 5.2 4.7 6.0 4.3
##  [325] 3.6 4.9 4.7 4.3 5.1 3.3 5.2 3.7 2.5 3.9 4.8 2.8 3.0 5.7 4.7 4.0 5.3 4.7
##  [343] 3.6 3.2 3.3 3.6 3.9 4.7 3.1 4.2 2.6 5.6 4.5 5.1 3.2 4.6 5.5 3.2 2.7 5.9
##  [361] 3.9 4.2 4.5 6.6 5.1 4.9 4.7 3.6 5.4 4.6 4.8 2.0 3.3 4.0 5.4 4.7 4.1 5.1
##  [379] 3.2 3.5 5.8 4.6 5.6 5.6 4.2 4.5 5.0 5.3 5.1 3.7 4.5 6.3 5.6 5.2 3.3 4.2
##  [397] 3.7 5.3 3.6 5.8 5.8 5.4 4.6 4.7 4.6 4.5 6.8 3.5 3.7 3.5 6.0 4.6 5.0 5.6
##  [415] 5.1 2.7 3.5 6.5 5.3 4.6 5.1 3.1 5.8 5.3 4.2 4.0 3.5 2.3 3.6 4.3 4.6 4.0
##  [433] 4.1 5.2 5.5 5.0 5.0 4.3 3.1 4.1 3.9 5.0 4.9 2.8 3.2 4.8 5.1 4.8 4.0 4.9
##  [451] 4.0 2.9 6.1 4.2 5.8 3.8 2.7 5.0 5.5 4.7 3.4 4.6 3.6 3.8 4.0 4.9 4.3 4.2
##  [469] 5.1 4.4 4.6 4.0 3.9 3.7 4.6 3.7 4.9 3.3 3.7 5.1 4.4 5.5 2.7 4.9 5.2 5.2
##  [487] 4.8 3.4 5.2 6.3 4.3 3.4 5.9 4.0 4.7 5.1 5.2 3.7 4.9 4.0 4.6 3.3 4.0 4.8
##  [505] 4.1 5.7 4.4 3.5 3.9 5.2 5.2 3.7 3.8 4.0 4.9 6.2 4.0 4.4 4.8 3.1 3.4 4.7
##  [523] 3.6 4.5 4.6 4.5 2.0 5.0 5.2 4.0 3.8 4.6 4.7 4.5 5.6 4.7 5.5 6.0 6.5 3.5
##  [541] 3.1 5.8 3.5 4.8 2.4 5.7 4.4 3.1 4.3 4.6 4.9 2.9 4.3 3.7 5.1 4.5 4.0 5.8
##  [559] 4.0 4.3 5.3 3.1 3.7 3.0 4.7 3.5 2.3 5.2 2.9 5.7 7.0 6.0 4.9 4.5 5.3 4.0
##  [577] 3.8 4.7 4.6 5.6 3.4 4.9 3.5 4.8 5.4 5.8 3.2 4.8 4.7 2.1 4.1 5.5 5.3 5.6
##  [595] 4.2 3.5 6.0 4.4 3.1 3.9 4.3 3.5 5.0 4.0 5.3 5.7 3.7 5.6 4.2 6.2 4.7 5.7
##  [613] 5.5 4.2 4.1 5.4 5.0 3.4 4.8 5.0 4.6 5.2 6.0 2.7 5.5 4.7 4.5 4.4 3.8 4.2
##  [631] 4.3 3.6 3.7 3.5 4.9 4.5 3.7 5.3 6.5 5.6 4.0 3.6 6.3 3.1 4.5 4.5 3.9 4.4
##  [649] 5.0 4.3 3.8 4.6 5.3 3.6 5.8 4.8 4.4 2.0 3.7 4.8 5.5 3.6 5.9 4.8 4.9 2.7
##  [667] 4.3 3.8 2.5 3.6 3.2 3.5 3.9 4.4 3.6 3.8 5.9 4.6 4.8 5.7 5.0 4.2 4.3 4.1
##  [685] 4.1 5.6 3.9 5.2 4.7 5.5 5.6 3.8 4.6 5.2 6.6 4.2 3.9 4.7 2.7 3.3 4.9 5.3
##  [703] 4.8 5.1 5.2 4.2 5.6 3.5 4.6 3.0 5.5 5.8 3.5 4.3 3.8 4.4 5.5 4.8 5.4 5.4
##  [721] 5.0 5.9 4.3 4.7 4.2 5.4 4.8 5.1 3.6 4.1 4.5 5.0 3.9 5.3 5.9 3.9 3.4 4.6
##  [739] 3.7 5.9 4.7 3.7 4.9 5.7 4.7 5.7 5.1 4.1 2.6 4.5 4.4 3.4 5.0 7.1 4.5 3.6
##  [757] 5.0 5.0 4.3 3.1 4.8 3.2 4.2 4.4 4.4 5.3 4.4 3.9 4.0 3.5 3.4 6.3 4.3 5.7
##  [775] 4.8 4.0 5.9 3.2 4.6 4.7 5.6 4.7 4.9 3.7 4.2 4.0 5.4 5.3 4.0 4.9 4.1 4.1
##  [793] 5.7 6.2 5.9 6.7 5.5 3.0 5.9 4.4 4.6 3.1 5.3 4.3 3.9 4.8 3.4 4.3 5.1 4.7
##  [811] 4.0 2.6 5.0 3.4 5.4 4.0 4.3 3.2 5.5 5.0 4.9 4.8 4.9 3.0 6.7 3.4 4.2 4.4
##  [829] 4.9 3.5 3.5 4.7 4.5 4.4 3.7 4.2 3.0 3.6 3.3 5.8 3.5 5.0 5.4 6.2 3.6 5.7
##  [847] 5.0 3.6 4.0 4.1 4.6 3.7 6.5 5.8 4.8 4.7 3.7 1.2 5.1 5.5 5.6 4.8 4.3 3.9
##  [865] 3.0 4.7 3.4 4.2 4.2 3.1 4.8 5.6 5.5 3.6 4.8 4.7 5.7 5.8 3.1 5.2 5.4 3.3
##  [883] 3.1 7.3 3.9 4.8 2.0 4.7 3.2 3.3 2.9 4.6 2.3 4.5 4.0 4.7 3.4 3.3 4.9 2.3
##  [901] 4.2 4.4 4.0 4.4 4.8 4.1 4.9 5.0 2.6 5.1 2.9 4.4 5.3 5.4 3.1 4.4 4.1 4.4
##  [919] 5.0 4.8 5.2 3.4 4.0 4.5 4.5 5.6 3.8 3.6 4.6 3.6 3.1 5.1 5.0 4.7 4.5 3.8
##  [937] 4.2 5.1 5.0 3.6 5.6 4.4 3.7 5.4 4.2 4.6 3.5 4.6 3.8 5.8 4.5 4.3 4.5 6.2
##  [955] 5.3 4.7 4.7 5.1 5.7 5.9 4.5 4.5 2.8 4.6 6.2 4.3 3.2 4.9 4.5 4.1 4.3 6.6
##  [973] 4.4 6.3 3.5 4.2 4.2 4.9 6.0 5.9 5.0 5.5 5.4 6.0 2.8 4.9 4.4 4.2 5.0 3.7
##  [991] 4.0 3.7 4.1 2.2 5.9 3.8 4.8 5.5 3.9 3.9
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
##   2.6   6.3
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
##    [1] 4.3 3.9 4.6 6.1 4.9 3.6 6.0 4.6 5.6 5.4 5.0 4.9 5.3 4.2 5.2 3.4 4.1 3.9
##   [19] 3.5 5.0 5.7 4.1 4.7 4.9 6.6 5.0 4.4 5.1 3.4 3.4 4.6 5.5 3.4 6.1 3.7 4.9
##   [37] 6.6 5.2 2.5 2.2 5.1 5.8 3.4 5.0 3.8 4.0 3.4 5.0 4.8 4.0 4.5 5.1 5.0 3.0
##   [55] 3.7 5.5 3.9 4.3 3.2 5.5 5.7 3.8 4.9 3.6 3.8 3.4 5.4 3.3 3.9 4.5 4.8 5.7
##   [73] 3.7 4.1 3.1 6.4 4.4 2.5 4.7 4.2 3.9 3.3 4.2 5.0 4.5 2.6 5.3 6.7 4.8 3.1
##   [91] 4.4 4.7 5.0 4.9 4.0 5.0 4.8 5.0 4.0 5.3 4.7 4.1 4.4 4.2 4.1 4.6 4.1 3.6
##  [109] 4.7 4.3 4.8 6.1 4.7 4.4 4.2 3.2 3.5 4.7 5.1 4.2 6.6 2.1 4.7 5.0 5.0 4.0
##  [127] 4.8 4.0 5.1 4.8 4.2 4.2 4.4 6.9 4.8 5.4 3.6 4.8 3.9 3.2 3.8 4.3 4.4 3.4
##  [145] 5.5 5.3 5.4 4.9 4.3 4.3 4.1 3.6 5.2 4.1 3.9 3.5 5.4 3.7 4.4 4.1 4.8 6.0
##  [163] 4.5 5.8 4.3 5.8 4.4 5.4 4.8 3.3 3.2 4.8 3.8 3.3 5.9 4.4 5.0 2.7 5.4 5.4
##  [181] 3.0 5.0 1.5 4.8 4.0 3.7 3.6 4.0 4.7 5.8 3.8 4.0 4.5 4.4 4.0 4.1 3.1 4.7
##  [199] 4.5 5.0 4.2 2.7 4.0 4.1 4.5 3.5 4.1 5.3 4.8 5.2 4.0 3.7 5.2 5.8 5.0 2.8
##  [217] 5.3 4.7 5.1 3.9 2.6 3.9 5.3 4.6 4.8 5.1 4.9 4.2 5.1 3.6 6.1 5.0 4.4 4.0
##  [235] 3.8 4.3 4.8 3.6 4.7 4.5 4.7 2.6 5.3 5.6 6.3 3.0 4.9 4.4 4.1 4.5 5.0 4.0
##  [253] 3.4 4.6 3.7 3.8 5.1 3.2 5.0 4.0 5.5 5.3 5.0 5.9 4.5 5.6 5.6 4.5 4.1 4.5
##  [271] 4.8 4.0 6.3 4.5 3.4 4.6 3.2 4.6 2.3 4.7 4.6 6.3 4.7 5.4 4.1 5.1 5.9 3.8
##  [289] 5.3 4.4 4.0 4.8 3.6 3.5 3.9 3.5 3.9 4.8 4.3 5.3 3.2 4.4 3.4 5.0 5.1 3.2
##  [307] 5.4 4.4 4.5 5.0 4.1 3.8 3.8 4.8 3.8 3.8 5.2 5.4 3.8 6.3 6.2 5.7 4.5 4.0
##  [325] 3.5 5.4 5.4 4.0 5.1 3.8 3.9 3.9 5.2 4.4 3.7 4.1 2.7 4.6 4.4 4.2 5.3 5.5
##  [343] 6.0 4.3 3.3 4.0 5.4 6.9 5.4 5.3 4.7 5.1 3.7 5.2 4.5 3.7 4.1 5.0 4.0 5.4
##  [361] 3.6 4.3 5.7 6.2 3.8 4.7 4.5 5.1 2.9 3.9 4.5 4.2 5.7 2.8 4.9 5.6 4.9 6.2
##  [379] 4.8 4.1 5.7 6.4 5.4 5.6 5.6 3.3 2.9 3.8 5.6 3.4 4.8 3.6 4.5 4.9 3.2 3.8
##  [397] 3.9 4.1 3.5 3.2 5.3 3.3 5.2 6.7 3.9 5.3 3.7 5.1 4.4 3.8 3.4 3.6 4.7 3.7
##  [415] 4.1 3.3 4.2 4.1 4.8 4.7 2.5 5.6 5.6 4.0 3.8 3.2 3.3 5.2 4.5 3.0 5.6 6.0
##  [433] 5.5 5.0 6.0 5.3 3.9 5.7 4.8 5.3 5.2 2.4 3.2 5.1 5.4 5.2 3.7 4.8 4.9 5.9
##  [451] 4.8 4.7 3.3 5.2 3.1 3.9 5.9 5.9 5.1 5.3 6.0 4.5 4.6 4.8 3.4 5.3 4.3 5.4
##  [469] 4.5 4.9 3.6 4.5 2.9 4.3 3.2 5.3 5.0 3.8 2.9 3.2 3.1 4.4 4.5 4.6 4.4 6.4
##  [487] 5.9 4.7 4.7 5.2 4.6 5.2 4.6 4.9 5.3 4.4 4.2 4.9 6.9 3.3 3.4 4.9 4.9 3.7
##  [505] 2.8 5.7 3.9 2.2 5.0 4.1 6.0 6.4 5.6 4.6 3.1 5.0 3.7 3.7 5.0 3.6 4.8 4.2
##  [523] 4.4 3.8 5.2 4.8 4.8 4.1 4.6 3.5 3.9 4.0 4.5 5.2 4.6 4.5 4.9 4.5 4.9 5.2
##  [541] 5.3 3.3 4.7 3.7 4.1 3.5 2.4 4.1 4.7 5.0 4.9 3.6 4.0 4.8 4.7 3.4 5.2 4.2
##  [559] 5.1 4.8 2.7 3.6 4.6 5.6 5.0 3.8 4.6 5.5 4.6 6.3 4.3 6.7 3.8 5.3 4.7 4.3
##  [577] 4.2 3.3 4.2 4.5 5.0 4.2 5.2 4.1 3.7 5.2 5.2 4.7 5.1 5.2 6.2 5.7 2.8 3.7
##  [595] 5.2 5.5 4.1 5.5 5.0 6.6 4.5 3.3 3.2 6.0 3.2 4.8 4.5 5.5 4.7 5.7 4.5 3.3
##  [613] 4.2 4.0 4.8 3.3 5.8 5.0 4.4 4.5 3.6 4.3 3.9 3.5 4.7 5.3 2.5 4.8 3.9 5.5
##  [631] 4.9 3.9 2.4 5.2 3.7 6.6 4.6 3.4 5.9 3.5 5.9 5.4 5.3 2.8 3.7 4.3 4.4 2.6
##  [649] 4.0 5.4 5.2 4.1 4.2 5.3 3.3 5.7 3.1 4.1 3.7 4.8 6.5 4.7 5.6 3.8 4.8 4.4
##  [667] 4.1 4.6 5.4 5.0 4.7 5.2 4.2 5.2 5.9 3.8 3.9 4.7 3.4 4.3 4.7 3.7 4.7 4.4
##  [685] 4.8 4.2 6.9 3.5 5.1 4.1 4.5 5.7 5.3 3.8 4.7 6.1 3.8 4.6 4.7 4.0 5.0 5.6
##  [703] 4.9 4.4 5.3 4.2 4.4 3.9 5.2 3.5 5.2 5.0 4.9 4.9 4.0 3.7 4.8 4.6 4.8 3.0
##  [721] 4.2 4.9 4.4 4.2 4.3 4.0 3.1 5.0 3.5 3.9 5.3 5.3 5.2 5.0 3.9 6.3 3.4 4.5
##  [739] 4.7 3.2 4.9 5.3 4.4 3.2 3.4 4.0 3.8 4.7 4.5 3.3 1.5 4.8 4.2 4.4 5.8 4.9
##  [757] 5.4 5.1 4.1 5.2 4.7 3.2 2.7 3.3 4.5 3.6 4.9 4.3 5.4 4.6 5.4 3.6 5.1 4.3
##  [775] 6.2 4.5 3.8 3.7 3.5 5.6 4.6 3.4 5.8 4.1 4.9 2.9 4.9 4.1 5.4 3.7 4.4 3.5
##  [793] 4.8 4.9 6.5 3.7 4.6 2.7 4.9 3.1 3.9 2.6 5.3 4.3 5.1 6.0 7.7 3.9 6.2 4.7
##  [811] 3.1 5.2 4.6 4.4 3.4 3.5 5.7 4.5 4.2 3.8 3.7 2.8 2.5 5.3 4.6 4.0 3.4 4.2
##  [829] 5.4 5.4 4.3 4.7 5.7 4.3 3.8 4.8 3.5 4.4 4.3 3.1 4.2 4.5 4.9 4.6 3.8 4.1
##  [847] 3.9 5.4 5.5 3.6 5.9 4.5 4.7 4.0 5.4 4.2 4.0 5.0 3.6 4.7 6.1 4.1 3.3 5.0
##  [865] 3.4 3.5 2.6 4.2 6.1 3.6 5.9 4.8 2.7 4.5 4.9 2.4 4.6 4.4 5.7 4.8 4.6 4.1
##  [883] 5.1 4.9 2.7 3.2 4.4 5.1 5.6 5.7 6.3 3.6 5.0 4.8 3.5 4.5 3.6 4.7 3.6 3.7
##  [901] 3.3 3.3 6.1 4.7 3.9 4.9 4.0 4.3 4.8 4.1 4.5 3.8 5.7 3.6 2.6 5.9 4.7 3.9
##  [919] 4.0 3.3 4.8 4.4 5.4 5.1 5.0 3.0 4.0 3.7 3.0 3.3 4.2 6.0 3.5 4.6 4.8 4.6
##  [937] 3.3 4.2 5.2 6.1 5.6 3.1 3.5 3.6 4.8 3.1 3.5 4.9 3.1 4.0 4.0 4.3 5.2 3.8
##  [955] 3.1 5.0 6.1 4.8 4.3 3.5 5.8 4.5 5.3 3.5 3.2 4.0 2.6 6.0 5.6 5.9 4.4 4.0
##  [973] 5.4 3.1 6.3 4.0 4.8 5.0 6.9 3.9 4.4 3.6 3.3 3.7 4.5 3.8 3.5 3.1 4.1 5.5
##  [991] 3.9 4.0 3.2 5.5 4.2 4.2 3.4 4.6 4.7 5.5
## 
## $func.thetastar
## [1] -0.0304
## 
## $jack.boot.val
##  [1]  0.488529412  0.378796562  0.236193029  0.135714286 -0.009063444
##  [6] -0.107288630 -0.094476744 -0.332840237 -0.427377522 -0.533424658
## 
## $jack.boot.se
## [1] 0.9710982
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
##    [1] 3.4 4.3 3.6 2.7 3.3 4.4 5.1 7.3 5.9 5.5 4.8 5.0 3.9 5.0 4.4 4.9 2.2 4.2
##   [19] 3.5 3.7 3.8 4.1 4.0 5.5 4.3 3.1 4.6 3.6 4.5 3.9 4.8 5.1 4.1 4.2 5.1 5.4
##   [37] 4.5 6.3 4.9 6.2 5.3 4.5 5.7 5.1 4.5 5.1 5.1 4.8 3.3 5.2 4.9 6.9 5.0 4.9
##   [55] 4.0 4.6 3.9 5.5 5.2 4.4 3.6 5.7 5.7 4.5 4.4 3.1 5.1 6.5 2.7 4.5 5.5 4.0
##   [73] 3.3 4.4 4.8 5.4 3.2 5.7 4.4 2.8 4.1 6.4 4.7 5.4 4.9 4.5 5.0 5.0 3.7 4.3
##   [91] 4.9 4.9 5.5 6.4 3.9 5.0 4.1 6.0 4.0 5.1 4.5 3.8 5.9 2.9 5.8 5.1 5.1 5.4
##  [109] 3.6 5.8 5.2 5.0 5.3 4.9 4.2 4.3 3.9 5.6 4.4 2.8 3.4 4.4 3.3 5.9 3.6 5.4
##  [127] 4.8 4.9 4.8 4.5 4.2 6.3 4.8 5.4 5.4 4.3 5.9 4.4 3.4 4.3 3.4 4.6 5.1 4.8
##  [145] 3.6 3.3 4.6 5.1 3.6 3.2 2.5 5.1 5.6 5.1 7.0 5.1 3.5 4.1 5.2 6.0 4.8 4.3
##  [163] 4.7 4.3 3.3 3.9 4.3 6.3 3.7 4.9 3.2 4.0 4.9 4.4 4.4 5.5 5.0 3.5 4.3 2.7
##  [181] 4.8 3.5 4.9 4.4 5.2 6.0 4.2 4.7 5.0 5.9 5.9 5.4 3.9 3.9 3.9 4.5 4.9 4.8
##  [199] 3.9 3.7 3.6 4.1 5.2 3.9 4.6 4.4 4.5 4.7 5.6 4.6 5.9 5.7 3.8 4.3 4.1 3.6
##  [217] 4.5 2.4 4.2 3.3 5.8 4.2 3.6 3.6 5.2 4.1 3.8 5.6 4.2 5.0 4.9 4.8 6.6 4.4
##  [235] 4.3 3.8 4.9 3.4 3.3 3.4 4.8 4.9 3.1 4.4 6.3 3.5 4.0 4.7 5.0 5.5 3.6 6.4
##  [253] 4.4 4.2 4.1 4.1 4.3 4.9 3.7 5.7 3.9 5.2 3.9 4.5 2.9 3.5 5.1 5.2 2.8 4.9
##  [271] 4.8 5.1 4.1 4.4 2.8 4.5 3.8 4.4 3.6 4.9 3.8 3.8 3.4 3.8 3.9 5.0 4.9 6.5
##  [289] 5.9 5.8 4.4 5.4 5.0 4.1 4.3 4.5 4.0 4.8 4.7 5.3 4.5 2.8 4.3 4.7 4.8 5.3
##  [307] 3.2 2.8 4.2 3.8 4.7 5.8 4.9 4.8 4.6 3.5 6.3 2.8 3.6 6.4 3.8 3.9 4.8 4.3
##  [325] 5.3 4.5 3.7 4.7 4.4 3.8 4.9 6.7 3.7 3.9 3.8 4.1 5.7 5.2 3.8 5.2 5.1 4.0
##  [343] 4.2 4.7 3.0 6.1 4.3 4.6 4.7 6.8 5.6 5.1 4.4 5.3 4.3 4.9 5.0 3.8 4.9 5.1
##  [361] 4.6 3.7 4.5 4.9 6.1 5.2 5.9 3.5 5.3 4.7 5.6 6.8 3.0 5.4 4.1 5.0 4.4 3.1
##  [379] 3.5 4.4 3.5 6.0 4.1 4.4 4.2 4.1 4.7 5.4 4.1 3.9 5.6 4.0 4.0 4.7 4.2 4.4
##  [397] 4.5 3.5 5.6 5.6 4.6 3.8 4.2 6.1 5.3 4.0 3.8 4.2 5.7 5.1 5.4 4.0 5.4 4.8
##  [415] 2.6 3.7 3.4 4.1 5.0 5.3 4.5 2.9 3.9 5.8 3.8 4.9 3.7 4.1 4.4 4.7 4.5 4.0
##  [433] 2.7 4.4 4.0 3.8 4.9 6.0 4.4 4.2 3.8 6.5 3.3 3.7 3.9 4.7 4.7 4.1 4.0 3.9
##  [451] 5.5 4.7 4.4 4.0 4.5 5.3 4.6 4.5 4.6 5.3 3.6 3.9 6.6 3.5 5.2 3.5 3.1 3.6
##  [469] 4.4 5.0 4.9 4.9 2.8 4.0 6.3 4.7 4.7 5.0 3.5 4.1 4.4 2.4 4.0 4.3 5.1 3.9
##  [487] 4.0 3.9 4.8 2.2 6.6 4.1 5.3 4.9 5.9 4.5 3.8 5.6 4.8 3.5 4.5 6.1 4.0 4.5
##  [505] 5.0 3.6 5.5 5.1 4.7 3.8 5.2 4.9 5.0 4.3 3.2 4.8 4.2 5.0 3.9 5.5 3.9 5.4
##  [523] 4.8 6.4 4.8 5.0 5.9 2.5 5.0 5.1 4.9 4.2 5.3 5.0 2.7 4.9 5.6 4.6 3.5 3.5
##  [541] 3.6 3.3 4.5 3.4 5.7 4.5 5.2 5.4 4.8 4.3 5.5 3.4 5.6 5.1 3.0 5.6 4.4 4.3
##  [559] 2.7 3.2 3.3 5.0 5.5 4.2 3.7 3.8 4.7 5.7 4.6 4.2 3.3 4.9 2.9 4.2 6.2 3.2
##  [577] 4.0 3.3 4.6 3.1 6.2 4.4 3.3 4.7 3.9 3.7 4.5 3.5 6.6 6.5 4.6 5.1 4.6 3.1
##  [595] 5.0 3.6 4.9 4.4 4.6 3.7 5.3 4.8 5.1 5.8 4.6 2.3 5.1 3.8 3.5 3.6 5.2 4.0
##  [613] 3.3 3.7 4.8 4.7 6.4 4.7 3.7 4.6 3.6 2.8 5.0 4.6 4.8 4.4 4.5 3.9 4.6 5.0
##  [631] 4.1 3.7 4.1 5.1 3.9 5.5 3.6 2.8 4.2 3.9 4.5 3.8 3.8 5.4 2.8 4.6 5.0 4.2
##  [649] 5.5 2.7 5.5 4.2 4.2 6.2 4.4 5.5 2.4 2.8 4.5 4.3 3.1 3.7 4.9 5.2 5.4 4.3
##  [667] 5.7 4.0 5.3 4.9 4.4 3.0 7.1 3.3 4.8 4.7 4.1 5.4 5.1 4.3 4.6 4.4 4.2 5.5
##  [685] 4.8 3.4 4.3 4.6 2.8 4.6 2.8 5.9 3.4 6.8 5.4 3.3 4.4 4.2 6.4 4.4 5.9 4.2
##  [703] 6.1 3.8 5.1 5.5 3.8 3.7 5.5 4.5 6.1 3.9 5.2 4.5 5.1 3.8 3.8 4.1 6.2 3.9
##  [721] 4.0 5.1 4.2 4.3 4.7 5.0 4.8 3.9 6.1 4.4 6.3 4.8 4.6 5.0 5.9 5.7 3.8 3.6
##  [739] 3.5 5.9 3.7 4.2 4.1 4.7 3.8 5.3 4.7 3.3 5.0 5.7 3.1 2.6 4.1 6.0 5.3 4.7
##  [757] 3.4 4.7 4.2 4.7 4.5 4.0 5.7 5.6 4.2 2.5 6.3 4.1 4.3 6.1 4.7 4.8 4.7 4.1
##  [775] 5.5 4.7 4.5 3.4 5.1 3.8 3.2 5.1 4.9 2.1 5.7 3.5 4.5 4.5 5.0 4.9 6.4 4.2
##  [793] 3.8 4.3 3.8 4.6 4.4 4.9 3.8 5.3 5.0 4.8 4.8 5.1 4.1 3.9 5.6 6.4 4.0 4.2
##  [811] 4.0 4.5 5.5 4.0 3.9 4.3 4.7 4.8 4.6 5.1 3.4 5.2 5.3 3.8 4.2 5.5 5.6 4.9
##  [829] 4.6 4.0 5.4 4.5 5.2 5.0 3.6 3.6 5.2 5.0 5.4 5.5 3.5 4.0 5.7 4.2 3.5 3.6
##  [847] 4.0 4.7 4.8 5.3 5.1 5.4 4.1 3.3 4.9 5.4 5.4 5.3 4.7 3.4 5.4 3.7 4.1 4.6
##  [865] 5.3 4.1 4.5 5.0 6.6 5.4 5.4 4.7 3.5 4.7 5.8 3.9 4.1 4.9 4.1 3.7 6.0 4.3
##  [883] 4.8 5.1 3.5 5.5 3.9 3.9 4.8 4.8 5.7 3.9 3.7 5.0 4.1 4.9 4.8 3.8 2.6 5.2
##  [901] 2.8 2.9 4.0 4.1 4.7 5.2 2.4 5.5 4.5 5.6 4.8 5.1 3.7 4.1 3.9 4.7 5.1 6.4
##  [919] 5.3 5.0 5.3 5.2 4.7 4.9 4.4 4.5 5.6 5.3 5.4 4.4 3.7 4.6 3.9 6.1 5.2 5.0
##  [937] 2.6 3.0 4.5 5.7 4.2 3.3 3.0 4.5 4.8 4.7 4.1 3.3 4.4 6.1 5.4 5.2 4.4 4.9
##  [955] 4.5 5.2 6.1 5.0 4.2 5.2 4.4 3.7 4.6 3.7 4.3 4.0 4.7 3.4 4.8 3.6 3.6 4.0
##  [973] 2.9 3.9 4.6 4.5 5.3 5.6 3.9 5.0 4.6 4.5 4.2 6.6 3.1 4.4 4.3 5.2 3.0 3.3
##  [991] 4.8 5.0 4.1 3.6 4.1 3.5 3.7 5.1 3.5 2.8
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.3 5.3 5.3 5.1 5.0 4.9 4.7 4.6 4.5
## 
## $jack.boot.se
## [1] 0.9656086
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
## [1] -0.04540039
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
##   3.687792   6.158713 
##  (1.580312) (2.827315)
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
## [1]  1.1794971  0.7359551 -0.1100427  0.9190746  0.6211005  0.1067631
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
##    [1] -0.373416997 -0.318158928  0.044991423 -0.197523994 -0.346509910
##    [6]  0.406392757 -0.425964384  0.430229473 -0.167036398 -0.644267720
##   [11]  0.548018436 -0.597367101  0.192027567 -0.907360368 -0.110956243
##   [16] -1.150296193 -0.432032423  0.335908996  0.761524647 -0.029566753
##   [21]  0.715205174 -1.064873530  0.109751544 -0.389399555 -0.427371841
##   [26] -0.561303433 -0.771487394  0.680494962 -0.227045875  0.032837558
##   [31] -0.840062076 -0.722495572 -0.349604891 -0.032974499 -0.157627044
##   [36] -0.909672744 -0.084711014  1.121334652 -0.465722269  0.067985934
##   [41]  0.569109459  0.066928300  0.659694074 -0.026851878 -0.164565124
##   [46] -0.687730757  0.380729390  0.193898702 -0.398093304 -0.212605118
##   [51] -0.425912326 -0.574161521 -0.008599652  0.523811536  1.175882431
##   [56] -0.748559311  0.004838736  0.086736350 -0.260610669 -0.186503341
##   [61]  0.765611075  0.131390044  0.776806434 -0.164781026  0.990770524
##   [66] -0.505967609 -0.509773910 -1.246697199  0.604423663  0.528351917
##   [71] -0.422186961 -0.774795307 -0.747076645 -0.642166256 -0.458630301
##   [76] -0.805823887 -0.288326553  0.228504443 -0.033887270 -0.166934374
##   [81] -0.191038178 -0.185211538  1.554240038 -0.010958055  0.974719982
##   [86] -1.686962217 -0.285340425 -0.532425614  0.453471535  0.007487593
##   [91] -0.165318821 -0.241815453 -0.432229385  0.101651572 -0.048401582
##   [96] -0.465617756  0.683754496 -0.958628176  0.026263341  0.218324151
##  [101] -0.002434100  0.694554275 -0.751156943 -0.402243803 -0.369865843
##  [106]  0.051921464 -0.537480152  0.180806305  0.130059927  0.090688819
##  [111]  1.152375088  0.677766382  0.055396409  0.895312992 -1.444019943
##  [116] -0.388046102 -0.155413779 -0.254648691  0.042203892  0.169708941
##  [121] -0.156494391  0.099366568  0.150539367 -0.107783236 -0.084263881
##  [126] -0.413531112  0.404280243  0.113995695  0.036628717 -1.105078826
##  [131] -0.089731727 -0.077523911 -0.253616821 -0.403663611 -0.023343979
##  [136]  0.518246714  0.700645246 -0.420053405  0.572309628  0.402913028
##  [141] -0.582469446 -0.493785707 -0.015369187 -0.618494643 -1.006388486
##  [146]  0.720115799  0.101751540  0.373405358 -0.447908659 -0.471086198
##  [151] -0.241625781  0.175789806  0.192104426  0.533779152 -0.717223631
##  [156]  0.079636757 -0.386902111  0.075693765  0.003978315 -0.588543388
##  [161]  0.692885200 -1.428119632 -0.535936121 -1.013312109  0.283417668
##  [166] -0.517207822 -0.854953564 -0.163680894 -0.937971401  0.503021367
##  [171] -0.834784791  0.420644433  0.165792510  0.270898682 -0.831879523
##  [176]  0.834756139 -0.258115151 -0.086781307 -0.385779389 -0.192683386
##  [181] -0.189772658  0.052605695 -0.567331542  0.165814820  0.647385753
##  [186] -0.442308681 -0.158182429 -1.746413650 -2.180099917 -1.322987607
##  [191]  0.207278200 -0.494988125 -0.330086528 -0.343634040  0.276552147
##  [196]  1.010153874 -0.447028556 -0.105417219 -0.424474010  0.334520870
##  [201]  0.019365997 -0.450555652  0.173943996  0.902395830  0.138925886
##  [206] -0.159469086 -0.289309294  0.337056948  0.152705626 -0.152384330
##  [211]  0.706002053 -2.049737693  0.100982805 -0.005516541  0.444876721
##  [216] -0.243619622  0.395579148 -0.302663861 -0.414806077  0.089574604
##  [221] -0.254395927 -0.325058547 -0.806142956  0.334025656 -0.798891551
##  [226]  0.191727172 -0.533250679 -0.286919440  0.902384571 -0.255409290
##  [231] -0.283077154 -0.476843568  0.013315280 -1.012278211 -0.750029017
##  [236]  0.185444657  0.574761380 -0.084598355 -0.180463028 -0.289106735
##  [241] -0.356900752  0.296949057 -0.715442396  0.023198144 -0.607215858
##  [246]  0.335141130  0.233216784 -1.054767614 -0.743208674 -0.064610750
##  [251] -0.589461069 -0.462890396 -0.888578797 -0.076273090 -0.046056345
##  [256] -0.561504737 -0.127760838  0.397307909 -0.400537799 -0.822138640
##  [261]  0.418505643 -0.240116343 -0.150595657 -0.182094128  1.170721355
##  [266]  0.058693479  0.315193374 -0.611703688  0.786855907 -0.370041377
##  [271]  0.761620999 -0.266049268  0.122200048 -0.807955238 -1.322430898
##  [276] -0.017098108 -0.087348463  0.911967098 -0.420573627  0.145393998
##  [281]  0.310963701 -0.319459018 -0.583060326 -0.432858916  0.199909853
##  [286]  0.725999846 -1.261294258  0.675484409  0.752621441 -0.161041448
##  [291]  0.342197372  0.311964446  0.587821514 -0.142537160  0.204140583
##  [296] -0.279274495 -0.144417249 -0.463913124 -1.513423142 -0.024064985
##  [301] -0.470420433 -0.281470597  0.210837031  0.177221436  0.500672023
##  [306]  0.097482846  0.528756413 -0.188331000  0.974428504  0.306024917
##  [311] -0.425910051 -0.229543098 -0.484623273 -0.584298780 -0.191123291
##  [316]  0.179405102 -0.511957377 -0.376047699  0.274202799  0.573816635
##  [321] -0.175473488 -0.143675816 -0.251170384 -0.075290212 -0.961274599
##  [326]  0.332213268 -0.226145125  1.144648833 -0.367267918  0.362895848
##  [331] -0.023933867 -0.098476253  0.173375714 -0.122139579  0.676963029
##  [336] -0.479083983 -0.472840661 -0.482971470 -0.764780394 -0.515363266
##  [341] -0.529151788 -0.620967955 -0.177523669 -0.439653384  0.340530086
##  [346]  0.338219849 -0.165395321  0.759991523 -0.308740680  1.515350976
##  [351] -0.538866327 -1.139109804 -0.247647878 -0.248168443 -0.508828270
##  [356]  0.177495081 -0.193038789 -0.166301153  0.061366393 -0.589769715
##  [361] -0.034946233 -1.129469430  0.029860507  0.128989883  0.984494699
##  [366]  0.501756507 -0.552922614 -0.088689782  0.800942489  0.349682889
##  [371] -0.522012016 -1.272490098 -0.009016071  0.386285349 -1.091506647
##  [376]  0.281539599 -0.494101731 -0.177772175  0.124914490 -0.511368826
##  [381] -0.260610669 -0.090804723 -0.297153687  0.206176280 -0.292506201
##  [386] -0.014117323  0.226208390 -0.083564342  0.104498850 -0.523832621
##  [391]  0.191996271  0.439614127  0.947507289 -0.274200842 -0.041664185
##  [396]  0.098854891  0.391250399  0.160432736  0.355864872  0.127323016
##  [401] -0.588584631 -0.011446989 -0.583509211  0.218917622  0.117891686
##  [406]  0.097392576  0.517998240 -0.152369095 -0.401924410  0.175252124
##  [411]  1.955971557 -0.448461880  0.856844323 -0.776417693 -0.469149739
##  [416] -1.123352167  0.109048748  0.206882184  0.523541153 -0.014263520
##  [421] -0.608246993 -2.507706943  0.532443852 -0.963068273 -0.697373799
##  [426]  0.067134386 -0.195616115  0.562826412 -0.033510402 -0.662545029
##  [431]  0.792749042  0.169561783  0.105726251 -0.504880441  0.479633350
##  [436] -1.289015774 -0.446905297 -0.350760082 -0.448917099  0.549325279
##  [441] -0.613108667 -0.222567348  0.241845214 -0.238522484  0.403556255
##  [446] -0.394776933 -0.100816087 -0.103291569 -1.320692680 -0.250944632
##  [451] -0.822818799 -0.400933708  0.078113451 -0.417212896  0.518611102
##  [456]  0.150044548  0.183336335  0.544321898  0.340511993 -1.385835239
##  [461]  0.665694725 -0.121419406 -0.008181080  0.644669649 -0.908630169
##  [466]  0.080860642 -0.412669922  0.278368102  0.345614481 -0.230627336
##  [471]  0.025473708  0.196715763  0.177161097 -0.519876502  0.528445491
##  [476] -0.332544519 -0.790768097  0.091633820 -1.361866400 -0.547659421
##  [481]  0.549303537  0.450451122 -0.104287300 -0.511135075 -0.496749726
##  [486] -0.507887485 -0.062167938  0.564318214  0.784592573 -0.334031284
##  [491] -0.868441582  0.291470517 -0.213370281  0.132100523  0.103326985
##  [496] -1.253706221 -0.527651035 -0.448330686  0.518085736 -0.468646528
##  [501]  0.102606705 -0.380342127 -0.847157870  0.839049415 -0.689582664
##  [506]  0.276715516 -0.312919517  0.115910535 -0.147069201 -0.518385297
##  [511]  0.542857924 -0.470095706  0.367323177  0.692158131  0.366808780
##  [516] -0.068028400  0.183753167  0.050037669  0.232478324  0.563562230
##  [521]  0.507383047 -0.304414771 -0.439860159  0.501598612  1.183798372
##  [526]  0.180467154 -0.705139438 -0.110690961 -1.297719215  0.708677858
##  [531] -0.405769417 -0.430366249  0.219748609 -0.938902992 -1.357844272
##  [536] -0.449788645  0.047004865  0.348937109 -0.361094264 -1.043315664
##  [541]  0.560405050 -0.271177440  0.740915419  0.522886136 -0.252907918
##  [546]  1.003230492  0.125367987 -0.530462947  0.580072597 -1.445640493
##  [551] -0.146942050 -0.133154300 -0.769795965  0.176861781 -0.640340970
##  [556] -0.615229487 -1.254986786  0.297600835  0.299222230  0.017712247
##  [561] -0.480834469  0.516497193  0.396335406 -0.995175584  0.120649372
##  [566] -0.875773797 -0.700213993 -0.215492659 -0.189484248 -0.159549197
##  [571]  0.324464002  0.441398969  0.204620507  0.544430153 -0.081297798
##  [576] -0.604947600  0.054608046 -0.405444540  0.125614918 -1.466527457
##  [581] -0.743725885  0.054557993 -0.336734517  0.324148297  0.320977520
##  [586] -0.481895854 -0.201675373 -0.309235194  0.240757147 -0.416922117
##  [591] -0.786228955 -1.121731894  0.615345716  0.087969392 -0.543529584
##  [596]  0.476842940 -0.039510872 -0.147303119  0.189373179 -0.090864293
##  [601] -1.101400555 -0.063300669  0.122262104 -0.043709356 -1.027423596
##  [606] -0.774732460  0.222729633 -1.146725783  1.373754761 -0.398658293
##  [611]  0.168394552 -0.059484537  0.074675487 -1.155875575  0.083685872
##  [616] -0.430327049 -1.186925549  0.326236056 -0.585239663 -0.022396047
##  [621] -0.206030791 -0.013075608 -0.337978313  0.033429888 -0.451013989
##  [626]  0.059330625  0.050264136  0.093458830 -0.439550193 -0.197727499
##  [631]  0.150635621  0.069990331  0.107652475  0.342481289 -1.710750703
##  [636]  0.481074869  0.203126043  0.518556424 -0.595625589 -0.585992852
##  [641] -1.375208545  0.262487445 -0.960664539 -0.026953359  0.455663730
##  [646]  0.638959821  0.363377339 -0.054769832 -0.738625013 -0.488907457
##  [651] -0.766794518 -0.815569414  0.214272968 -0.651335225 -0.367575122
##  [656]  0.328563651 -0.345344351 -0.087144977 -0.466999671 -0.590956269
##  [661] -0.460418451  0.531628370  0.516477966  1.200589561 -0.022822847
##  [666]  0.961858366 -0.436596545 -0.836499388 -0.313588504  0.936900259
##  [671]  1.662501589  0.281367365 -2.503008756 -0.272237041 -0.247516415
##  [676] -0.267695414  0.143450648  0.400214509  0.348133815 -0.475778575
##  [681]  0.503853992 -0.319130982  0.528353616  0.466844645 -0.691486504
##  [686]  0.519864082  0.218417262  0.120417134 -0.638175701 -0.003687369
##  [691] -0.859401361  0.119261363  0.301147399  0.619769596 -0.164084078
##  [696] -0.704838255 -0.722360698  0.310087305  0.871418335 -0.685212181
##  [701] -0.153916406  0.728222149  0.115179873  0.802056196  0.174878519
##  [706] -1.085735715 -2.540899770 -0.444786874 -0.079975197 -0.430620864
##  [711]  0.885540510 -0.155837059 -0.065185076  1.298891618  0.358107185
##  [716] -0.192141916 -0.283050529  0.103006605  0.155019273 -0.056799956
##  [721]  0.125925583  0.274803980  0.159225066 -0.803915637  0.491693416
##  [726] -0.151941762  0.113928648  0.121263083 -0.240559773  0.435918478
##  [731]  0.275588788 -0.456808046  0.321884230 -0.750030630 -0.337499189
##  [736]  0.339822877  0.534063028 -0.250154669  0.136045437  0.009597060
##  [741]  0.005295004 -0.455386077 -0.316464256  0.406105856 -0.076424966
##  [746] -0.651127552 -0.055841930 -0.333867451 -0.695557495 -0.194943676
##  [751]  0.517494836  0.341581788 -0.166413225  0.204872148 -0.577314310
##  [756] -0.287907464  0.068555588 -0.404229588 -0.586912535  0.127563916
##  [761] -0.493155664 -1.285602475 -0.029115216 -0.010369080  0.984058983
##  [766]  0.047941839 -0.335373572  0.256707014 -0.192144349  0.079410990
##  [771] -0.163584862  0.532819558 -0.893366454 -0.973708908  0.767560355
##  [776] -0.137306602 -0.514471525  0.165792510 -0.597530981 -0.250090237
##  [781] -0.129678238 -0.335650652 -0.163465530 -0.472627831 -0.245932796
##  [786] -0.023954037 -0.905543091  0.125322970 -0.191957851  0.072161369
##  [791] -0.963349901 -0.007493085  0.375323328  0.406237530  0.022578937
##  [796]  0.089169547 -0.427599426 -0.490228573 -0.293967034 -1.305271056
##  [801]  0.109346320  0.338504992 -0.010895335  0.192020351  0.775925428
##  [806] -0.760554569 -0.236164475 -0.224395050 -0.129093339 -0.017894981
##  [811] -0.369340030  0.981633445 -0.291009860 -0.044312622 -0.110426629
##  [816]  0.001284380 -1.937903409 -0.763486487 -0.781831706 -0.077523911
##  [821]  0.772682880 -1.493566372 -0.166775732 -0.881408742 -0.851798770
##  [826]  0.628330603 -0.189455274 -0.790882931  0.357813430 -0.362358476
##  [831] -0.109267303  0.909815101 -0.024442465  0.603429262 -0.722611157
##  [836]  0.248582360 -0.779809317 -0.151941762  0.308753584 -0.187014367
##  [841] -0.084296989 -0.280996117 -0.519425683 -0.142632288  0.585447753
##  [846] -0.367312549  0.192676717  0.027966075  0.117676938 -0.123215373
##  [851] -0.442308681 -0.844126881 -0.008181080  0.136817403 -0.294311141
##  [856]  0.160731503 -0.060241365  1.232974625 -0.285325874 -0.277085648
##  [861] -0.529733602 -0.319254667 -0.106791737 -0.045564862 -0.567223693
##  [866] -0.006075485  0.645860340 -0.450648008 -0.001011405  0.064860777
##  [871] -0.100718688  0.483227779 -0.502778827 -0.450196049 -0.197727499
##  [876]  0.286820805 -0.098749142  0.706613283  0.556547526 -0.229126425
##  [881] -0.726440090 -0.221206317 -0.330668261 -0.154817902 -0.737290360
##  [886]  0.075242411  0.247605250 -0.050382026 -1.269724372  0.131892358
##  [891] -0.836400636  0.610475890 -0.369584046  0.845878002 -0.563638500
##  [896] -0.288326553 -0.031238902  0.895159954 -0.314106702  0.351804973
##  [901] -0.071053660  0.743401542 -0.356717575  0.144371643 -0.374410051
##  [906] -1.138909226  0.007510192 -1.339893493 -0.089926796 -0.942644783
##  [911] -0.346668568 -0.301311994 -0.311739201 -1.001727492  0.467665165
##  [916]  0.187627609  0.034396681 -0.274563276 -0.238885507  0.640801157
##  [921] -0.178917058  0.029107503  0.490547221 -0.029859374 -0.018668438
##  [926]  0.670060932  0.055997693 -0.821214806 -0.250090237  0.700135590
##  [931] -0.466458285 -0.653958661 -0.273870318 -0.361164877 -0.994164709
##  [936]  0.724188548 -0.185446461 -1.070989373 -1.330146529  0.457351526
##  [941]  0.474027604  0.363833622 -0.352929156  0.295808709 -0.377839261
##  [946] -0.134475146 -0.291091974 -0.293758474  0.555163587 -0.133260552
##  [951]  0.126999171 -0.056881244 -0.310393408  0.457977975  1.309769420
##  [956]  0.396422454 -0.487685583  0.094460732  0.886237293 -0.920506945
##  [961] -1.127395425 -1.092307131 -0.658505408 -0.176333275 -0.227722258
##  [966]  2.201323771  0.589163026 -0.335152036  0.220234065 -0.849460346
##  [971] -0.276942371 -0.844126881  0.942468921  0.694554275  0.023558713
##  [976] -0.157480768 -0.359709419 -0.761544400  0.328383585 -0.440955035
##  [981] -1.064111384 -0.117298809  0.237192599  0.404248151 -0.584576604
##  [986] -0.459743329  0.138639981  1.409631044 -0.358486520 -0.167004099
##  [991] -0.054088544 -0.743709227 -0.256576825  0.501598612 -0.746901776
##  [996]  0.149736154 -0.765119168  1.327671186  0.657484043 -0.345344351
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
##   0.59877944   0.28413500 
##  (0.08985138) (0.06353035)
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
## [1] -0.51817165 -0.78802468 -0.24906791 -0.28792311 -0.21756215  0.02197831
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
## [1] 0.0114
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8901362
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
## t1*      4.5 -0.03493493   0.9272596
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 5 6 7 
## 1 1 1 2 3 1 1
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
## [1] 0.0124
```

```r
se.boot
```

```
## [1] 0.9025746
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

