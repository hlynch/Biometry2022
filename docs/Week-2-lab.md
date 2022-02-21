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
## 0 2 4 5 7 8 9 
## 2 1 1 2 2 1 1
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
## [1] -0.0021
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
## [1] 2.784867
```

```r
UL.boot
```

```
## [1] 6.210933
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
##    [1] 4.4 5.5 4.7 4.4 3.4 4.0 2.8 5.3 4.9 3.1 5.2 6.4 5.0 5.0 4.2 4.5 3.9 5.8
##   [19] 4.7 5.2 5.7 5.5 3.9 4.6 3.5 5.0 4.0 4.9 4.7 5.6 5.2 5.2 5.0 3.7 6.2 6.2
##   [37] 4.1 4.0 4.6 2.6 5.2 6.2 4.1 4.4 4.3 5.5 3.7 4.8 5.5 4.3 4.9 3.1 5.8 6.4
##   [55] 3.7 2.3 5.3 3.1 3.4 4.4 5.3 4.3 5.6 6.1 5.4 2.6 6.0 4.6 5.1 5.1 4.5 3.4
##   [73] 3.7 4.8 3.0 4.7 4.4 4.7 5.0 4.6 4.6 4.7 3.8 4.4 6.6 5.7 4.8 4.6 4.5 3.9
##   [91] 4.2 4.4 2.4 3.0 5.2 3.9 5.9 4.1 4.5 3.0 5.2 5.0 5.1 4.7 3.0 3.0 3.6 6.4
##  [109] 4.5 3.5 4.2 2.3 3.7 4.6 5.0 3.9 3.9 4.8 5.0 4.7 3.6 4.9 5.1 5.1 4.3 3.9
##  [127] 3.6 5.7 4.4 4.5 3.1 4.7 6.3 3.9 4.9 4.3 4.6 3.7 4.3 5.9 4.3 3.8 4.1 5.0
##  [145] 6.4 5.4 3.9 4.4 4.8 4.9 3.6 4.1 4.9 3.3 4.5 3.5 4.2 4.8 5.1 5.2 5.5 3.0
##  [163] 5.7 4.4 5.4 3.9 4.4 4.1 2.8 5.5 2.9 3.8 3.7 4.5 5.2 3.1 5.1 4.8 5.4 4.0
##  [181] 4.1 3.8 5.2 3.3 4.6 4.3 4.8 5.9 5.0 3.3 5.3 4.1 3.4 3.7 4.2 5.1 4.6 3.7
##  [199] 5.2 4.6 4.6 4.5 6.5 3.9 2.2 5.1 4.3 3.8 3.0 2.7 3.9 4.9 5.0 4.3 3.7 3.7
##  [217] 4.9 6.3 4.2 3.0 5.1 4.1 4.5 5.7 5.5 4.0 3.2 5.2 4.7 4.8 3.7 6.2 4.3 5.4
##  [235] 4.4 3.1 3.7 4.6 5.4 6.4 3.2 5.9 3.7 4.3 5.1 3.5 5.3 3.9 4.5 5.1 4.7 5.4
##  [253] 4.7 3.2 2.2 4.4 4.9 4.6 4.6 3.1 4.7 4.5 3.1 5.3 4.9 4.0 4.5 3.7 3.6 4.5
##  [271] 5.2 5.7 4.5 3.8 3.6 5.1 4.3 5.3 4.2 4.6 4.7 4.4 3.5 3.1 5.0 4.8 4.1 3.7
##  [289] 5.6 4.2 4.3 5.1 3.5 5.5 4.6 3.8 3.7 4.7 4.4 4.2 4.4 4.6 3.2 4.1 4.5 4.9
##  [307] 5.7 4.8 5.0 3.9 5.0 4.6 5.0 4.9 3.8 6.1 4.6 5.8 4.4 5.5 3.1 4.0 4.5 2.9
##  [325] 3.9 3.8 4.4 4.9 5.3 5.5 3.8 5.0 4.2 6.4 5.3 4.4 4.3 4.0 5.2 3.9 4.8 5.1
##  [343] 2.7 4.0 4.2 3.3 3.8 4.9 3.7 4.3 5.1 5.7 6.7 4.3 4.8 3.9 4.7 4.5 5.2 4.4
##  [361] 5.0 5.0 5.0 5.8 5.2 4.8 4.8 4.4 4.2 4.3 4.9 3.5 3.5 3.9 5.3 5.4 4.7 4.8
##  [379] 5.5 4.5 4.1 3.4 5.0 4.5 4.6 3.7 5.5 4.2 4.2 3.6 5.2 3.3 5.1 3.7 4.9 4.2
##  [397] 5.3 3.7 5.2 3.5 5.4 5.0 6.3 4.5 5.3 5.8 3.9 5.2 4.7 4.7 4.5 4.9 4.4 6.1
##  [415] 4.6 4.1 4.1 4.0 2.9 5.6 4.6 5.8 2.8 4.7 4.2 4.7 5.7 4.5 3.3 3.4 4.4 5.0
##  [433] 4.1 4.1 4.3 4.3 4.2 3.7 4.4 5.2 2.2 5.3 6.1 3.9 4.5 5.0 4.3 4.5 3.8 5.6
##  [451] 5.5 4.6 5.2 4.8 5.7 5.0 4.0 5.9 5.1 2.2 5.9 3.3 3.7 3.8 3.9 4.9 3.3 4.6
##  [469] 6.2 4.0 5.3 4.6 3.6 5.1 6.3 3.3 4.2 5.8 5.2 5.7 3.4 3.0 5.0 4.3 2.7 3.9
##  [487] 4.7 4.6 5.2 6.3 4.2 4.9 6.2 5.5 4.6 4.9 3.3 3.9 5.2 4.4 4.4 7.3 5.4 5.1
##  [505] 3.3 3.9 5.1 4.9 3.2 3.4 4.4 4.6 3.9 2.0 5.2 4.1 5.8 4.8 3.5 2.4 4.3 4.2
##  [523] 3.6 3.7 4.5 4.4 2.6 5.4 4.9 3.7 3.2 4.7 2.9 4.4 5.7 4.2 5.4 4.8 4.1 5.5
##  [541] 4.3 4.1 4.3 3.9 3.6 4.6 3.4 5.2 5.3 4.4 6.4 3.1 3.4 4.6 5.0 5.9 4.1 3.4
##  [559] 5.1 4.9 4.1 5.0 5.5 3.6 3.9 3.2 5.8 3.0 3.8 3.8 4.3 3.0 6.7 4.2 4.4 6.0
##  [577] 4.9 4.9 4.5 3.2 4.5 5.1 5.2 4.2 3.3 4.5 5.2 4.5 3.6 5.4 5.5 5.0 5.1 5.6
##  [595] 3.6 4.4 4.6 3.9 6.4 4.4 4.0 4.4 4.0 3.9 4.5 5.4 3.8 5.1 4.4 4.6 6.2 3.0
##  [613] 4.3 4.1 3.6 6.5 4.8 4.9 4.3 5.0 3.3 4.2 3.2 4.2 4.9 5.0 4.6 5.1 6.5 4.9
##  [631] 4.1 3.2 5.5 5.1 2.4 5.5 2.8 5.0 4.5 5.6 3.6 4.9 3.6 4.2 2.7 5.4 5.2 4.7
##  [649] 3.5 5.0 5.9 4.6 5.5 5.1 5.6 5.1 3.2 4.9 5.4 5.1 5.4 4.9 3.4 4.8 5.7 6.1
##  [667] 5.3 4.5 4.7 4.6 5.1 2.4 3.4 4.7 5.1 4.1 4.1 4.2 5.5 5.3 4.7 5.0 5.5 3.8
##  [685] 4.8 4.5 2.4 4.1 3.1 4.6 4.3 3.6 4.4 5.1 5.6 3.5 4.6 4.9 5.7 5.7 3.1 3.6
##  [703] 4.9 4.5 4.5 6.2 3.3 4.0 3.0 4.8 3.6 4.9 4.2 3.0 5.7 4.1 4.1 4.9 4.1 4.5
##  [721] 5.2 5.2 4.4 4.4 2.1 4.9 4.3 2.8 3.4 3.4 5.3 5.1 5.1 4.6 3.8 4.3 5.1 4.4
##  [739] 4.5 5.4 4.3 4.5 3.7 2.8 4.9 4.7 3.8 5.1 6.6 4.6 4.5 4.1 3.9 3.3 3.9 2.7
##  [757] 3.8 5.3 4.5 4.6 5.8 3.9 5.0 5.0 4.1 4.8 3.7 4.5 4.6 4.4 4.1 5.2 4.9 4.3
##  [775] 3.6 3.9 3.2 3.6 5.2 5.0 3.3 4.0 6.0 5.3 3.9 4.0 4.4 4.1 3.3 4.4 3.5 4.1
##  [793] 3.9 5.9 4.3 4.9 3.7 4.2 5.3 3.5 5.9 4.0 4.9 4.1 3.4 7.6 4.9 5.1 5.7 4.1
##  [811] 5.5 5.7 4.1 5.3 4.2 5.3 4.0 5.3 2.8 6.2 5.3 4.8 4.3 3.3 5.6 4.5 3.3 3.3
##  [829] 5.9 4.3 4.7 6.5 4.3 4.2 5.2 4.8 4.7 3.4 5.3 4.0 3.5 1.7 4.1 4.8 5.0 4.2
##  [847] 3.4 5.5 4.3 5.5 2.7 5.6 3.9 3.8 3.4 4.7 4.3 5.1 4.7 7.0 3.2 5.5 5.2 3.2
##  [865] 5.6 4.5 4.4 5.3 2.4 4.7 4.0 5.0 4.4 5.2 4.2 3.7 4.2 4.0 5.1 2.9 5.3 4.2
##  [883] 5.4 3.7 5.7 4.6 4.6 4.6 4.1 5.0 4.6 5.3 6.0 4.5 4.3 6.0 4.7 4.1 4.5 4.3
##  [901] 4.5 4.8 2.7 5.0 6.0 4.4 4.7 3.6 5.1 3.4 5.2 3.2 4.5 4.1 5.5 4.1 4.3 3.0
##  [919] 4.2 6.0 3.9 4.1 5.1 4.1 4.6 5.5 5.3 3.5 5.1 4.3 5.0 5.4 4.4 4.0 5.1 5.8
##  [937] 4.0 4.1 6.3 4.9 5.2 4.6 5.3 5.1 6.7 4.2 3.8 4.1 5.5 3.9 5.3 4.4 4.7 2.7
##  [955] 3.8 5.0 3.3 4.6 5.8 3.1 3.5 3.7 5.8 4.9 4.0 4.3 4.7 4.0 4.1 3.4 4.6 5.9
##  [973] 2.8 5.4 4.6 5.5 3.4 6.9 5.6 3.1 5.3 4.1 4.0 5.8 2.5 5.3 4.3 5.0 6.3 4.8
##  [991] 6.2 4.1 5.1 5.4 4.7 3.3 5.0 3.5 5.0 5.8
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
##    [1] 3.6 5.0 4.4 4.5 4.0 5.5 3.3 2.1 4.0 5.0 4.5 3.0 7.0 4.3 4.2 3.1 3.2 4.5
##   [19] 3.6 5.0 5.0 3.6 5.5 6.0 5.1 3.8 4.2 5.2 4.6 3.6 4.1 4.6 3.7 5.2 3.3 4.6
##   [37] 4.4 5.1 4.1 3.8 3.4 4.7 4.9 4.8 4.9 4.7 3.8 3.8 4.1 3.3 5.0 5.1 4.8 3.1
##   [55] 5.3 5.3 3.4 6.2 4.5 5.3 5.6 4.3 4.7 5.1 4.2 2.3 2.8 4.9 4.5 3.9 3.4 3.3
##   [73] 4.6 3.5 3.2 5.3 4.5 5.0 4.3 2.8 5.8 4.3 4.0 5.8 4.5 4.4 3.6 4.4 3.0 4.6
##   [91] 4.2 4.5 4.2 5.1 4.1 3.6 4.2 4.4 4.1 3.1 6.2 3.8 3.9 4.8 5.4 3.8 4.2 2.9
##  [109] 3.9 3.5 4.4 4.1 3.9 5.4 4.6 4.5 5.7 3.2 5.5 3.7 3.5 4.9 3.4 4.3 4.4 5.4
##  [127] 3.6 3.9 3.6 7.6 5.1 4.6 3.1 4.7 3.4 4.3 5.4 5.0 5.6 5.0 4.7 4.2 4.6 5.2
##  [145] 5.9 4.3 3.8 5.0 6.0 4.9 4.4 3.6 3.9 2.9 4.4 4.4 4.5 3.7 5.2 3.9 3.5 5.5
##  [163] 4.3 4.5 4.4 5.3 5.0 4.7 4.3 3.9 4.5 6.3 4.3 4.4 5.2 4.3 4.0 2.1 4.0 5.2
##  [181] 3.7 6.6 6.2 3.5 4.7 5.0 5.4 3.9 5.3 5.3 5.8 3.6 4.6 3.7 5.6 2.7 2.8 3.6
##  [199] 3.4 5.2 3.3 3.3 5.2 1.8 4.3 5.6 5.4 4.1 5.6 3.5 4.1 4.3 5.3 5.0 3.2 5.4
##  [217] 3.9 5.0 3.6 3.8 5.6 4.8 3.0 4.4 4.3 4.9 4.3 5.7 4.4 3.7 4.7 4.4 4.6 4.6
##  [235] 2.9 5.4 5.1 3.4 3.1 3.5 4.2 3.3 3.5 4.2 3.4 4.1 4.7 5.0 3.6 4.1 6.1 4.2
##  [253] 6.2 6.3 6.1 5.1 6.7 4.6 5.0 6.0 3.9 4.8 3.5 4.9 5.0 4.4 4.0 3.6 4.3 5.5
##  [271] 4.0 6.7 3.2 3.5 4.2 5.0 4.5 4.3 4.2 4.1 4.5 4.6 4.7 4.2 3.9 4.8 4.6 3.5
##  [289] 4.2 3.8 4.1 3.3 4.9 4.7 4.3 5.1 3.7 5.4 4.4 3.3 4.8 4.6 5.0 3.3 3.1 4.5
##  [307] 4.2 4.9 4.3 4.9 3.7 3.8 5.1 4.2 4.6 3.3 4.4 5.8 4.4 4.6 4.6 5.8 5.1 4.2
##  [325] 4.8 4.6 5.1 5.6 4.0 5.1 4.5 4.7 4.5 5.9 3.0 6.6 4.7 3.6 4.1 5.0 4.2 2.2
##  [343] 4.8 3.7 3.3 4.2 3.5 5.2 5.7 3.7 2.0 4.0 5.4 4.8 4.7 3.1 5.6 4.3 4.4 5.1
##  [361] 3.7 3.4 5.4 5.7 4.6 4.8 3.6 3.8 4.2 4.6 3.7 4.9 4.6 3.8 3.6 3.7 4.2 4.4
##  [379] 3.7 3.3 4.0 3.1 4.0 3.4 4.3 3.1 6.0 4.7 3.2 3.5 5.0 4.1 3.5 3.6 6.1 5.4
##  [397] 5.7 4.1 5.6 3.9 4.0 3.9 3.7 3.0 3.8 4.8 5.5 6.0 4.1 4.5 4.1 5.6 5.0 4.8
##  [415] 5.4 4.5 6.0 5.0 5.1 4.8 3.6 3.8 4.9 5.6 4.7 5.6 4.3 3.4 4.1 4.8 4.1 3.0
##  [433] 3.8 4.6 3.6 5.0 5.7 4.7 3.7 2.4 4.8 5.6 4.8 5.6 4.7 2.4 3.7 5.1 4.9 5.1
##  [451] 3.6 3.8 3.7 5.0 5.3 5.2 5.0 4.5 5.8 5.2 3.0 4.1 5.0 4.1 3.1 4.5 3.2 4.9
##  [469] 5.0 3.5 3.8 4.8 4.2 5.9 5.0 4.1 4.0 7.0 5.4 7.3 4.2 4.7 5.4 3.1 5.4 4.0
##  [487] 4.9 3.4 3.4 5.5 3.6 4.0 4.4 3.8 3.6 5.4 4.6 5.0 4.3 4.0 3.9 4.8 4.6 4.5
##  [505] 4.7 4.4 3.9 4.0 4.3 4.9 4.3 3.7 3.7 5.6 3.8 5.5 4.9 3.9 4.9 3.0 5.5 3.1
##  [523] 4.1 3.6 4.4 3.6 4.0 4.6 4.4 4.7 3.6 5.9 3.5 3.7 3.8 5.7 5.0 3.5 4.1 5.0
##  [541] 5.1 5.3 4.8 4.6 3.9 3.5 5.4 4.2 6.3 4.6 3.7 3.9 6.0 4.0 5.5 4.6 5.5 2.9
##  [559] 3.5 4.3 4.4 5.8 4.3 7.1 5.8 4.9 4.1 5.6 5.6 4.3 5.7 4.7 5.4 5.7 5.4 5.1
##  [577] 3.6 5.0 4.2 5.1 4.6 5.9 3.0 5.6 2.9 4.1 3.8 4.5 5.6 4.4 3.8 4.1 5.0 6.3
##  [595] 3.7 4.3 6.7 4.8 5.9 4.8 2.5 4.4 5.2 4.5 5.9 4.3 4.8 4.5 4.3 5.0 4.1 5.2
##  [613] 4.8 4.0 4.5 5.1 4.0 4.8 5.4 3.2 5.3 5.3 2.8 3.9 4.4 4.8 4.6 4.5 4.6 4.4
##  [631] 3.2 3.6 4.1 3.7 6.3 3.9 4.5 5.8 3.2 4.2 4.6 4.6 4.4 5.3 4.2 2.9 4.6 4.0
##  [649] 4.0 4.6 3.6 4.5 4.4 5.8 4.0 6.2 4.9 5.3 5.0 3.4 5.4 5.9 5.6 4.6 3.8 4.2
##  [667] 5.3 6.3 3.4 3.5 4.6 3.4 4.6 6.0 5.2 3.7 5.2 6.1 2.5 5.5 4.2 5.6 3.8 4.8
##  [685] 2.5 3.3 5.8 4.1 5.8 5.1 4.0 4.5 4.8 5.3 4.6 5.3 5.7 3.4 4.4 3.6 4.5 5.6
##  [703] 4.2 4.4 3.8 3.6 4.0 2.4 3.8 5.2 4.5 4.1 6.8 2.6 3.3 5.5 4.2 3.9 4.5 3.7
##  [721] 5.4 3.4 6.1 4.1 3.3 4.8 5.1 5.7 4.1 5.0 4.8 4.6 3.1 5.6 3.8 4.7 3.2 4.9
##  [739] 4.2 5.0 4.6 5.1 4.2 4.5 4.3 4.0 5.0 3.7 7.0 5.7 6.5 3.9 3.7 3.6 3.8 4.4
##  [757] 5.2 5.3 4.2 5.2 5.4 3.6 5.0 4.3 5.5 2.9 3.6 4.4 3.9 2.5 2.9 4.7 3.6 4.6
##  [775] 5.4 3.2 5.4 5.4 4.8 4.4 4.5 4.6 3.9 5.2 3.5 2.9 5.3 5.6 4.1 2.9 4.8 5.5
##  [793] 5.0 4.8 4.3 5.4 4.0 5.6 3.1 4.7 4.4 3.0 4.9 5.9 3.7 3.4 4.5 4.8 5.6 3.8
##  [811] 5.2 5.2 5.1 5.4 4.6 3.7 5.6 4.6 3.7 3.8 4.2 3.8 2.1 6.2 4.6 5.7 4.6 3.6
##  [829] 5.9 3.9 5.0 3.4 4.2 4.5 3.7 4.9 5.4 4.2 5.3 5.1 4.5 4.5 4.9 3.4 4.6 5.6
##  [847] 4.6 3.7 5.6 5.3 3.8 4.5 5.3 3.4 5.0 4.4 5.9 3.0 2.4 4.4 4.4 5.0 3.0 3.9
##  [865] 5.2 3.6 3.9 4.8 3.8 4.9 5.0 3.4 4.3 3.5 5.5 4.7 2.7 6.1 4.4 4.1 3.5 5.7
##  [883] 4.6 4.4 3.4 4.1 4.1 4.9 4.2 4.0 6.2 3.3 4.9 2.4 3.2 4.6 3.3 4.7 5.4 3.8
##  [901] 5.3 4.4 4.1 4.8 3.4 5.5 4.5 4.4 5.1 5.1 3.0 5.4 4.0 5.6 3.3 5.3 4.5 5.1
##  [919] 3.1 4.0 5.1 4.6 5.2 5.6 5.0 4.4 5.0 5.3 3.8 2.4 4.1 4.2 3.8 4.3 3.0 5.1
##  [937] 5.0 3.5 4.8 5.4 3.4 4.7 4.2 4.1 3.9 2.7 4.2 4.0 4.8 6.3 5.2 3.7 3.7 4.8
##  [955] 4.2 3.1 4.6 3.9 5.4 5.3 4.1 4.4 4.7 2.7 3.5 6.2 4.1 6.0 5.5 4.4 3.8 4.0
##  [973] 4.7 6.9 4.8 4.7 3.9 4.1 1.7 3.8 4.3 4.3 3.3 4.2 4.9 5.9 5.6 3.8 4.5 4.5
##  [991] 4.7 2.5 3.3 2.7 4.7 1.7 3.0 5.8 3.9 4.2
## 
## $func.thetastar
## [1] -0.0548
## 
## $jack.boot.val
##  [1]  0.48073394  0.40560472  0.30088757  0.07365439 -0.03250000 -0.09752747
##  [7] -0.24208955 -0.32212389 -0.44630137 -0.53005618
## 
## $jack.boot.se
## [1] 1.007516
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
##    [1] 4.5 4.7 3.5 5.5 5.7 4.7 5.3 3.5 4.1 6.0 4.7 4.1 3.0 4.8 4.1 4.5 4.7 3.5
##   [19] 5.0 3.8 3.5 6.1 5.7 4.6 5.1 5.0 4.9 4.4 5.0 3.2 2.8 7.0 4.2 3.4 3.9 5.5
##   [37] 6.6 4.9 4.8 5.1 5.1 4.0 5.5 5.9 4.0 5.0 4.5 3.6 5.2 4.2 4.2 6.0 5.2 5.7
##   [55] 4.5 4.8 2.7 4.0 3.3 5.7 4.9 6.7 4.6 4.6 3.2 4.3 3.6 3.5 3.4 5.6 4.7 3.9
##   [73] 6.3 4.3 5.5 3.7 4.3 3.9 5.6 6.2 4.6 3.4 3.7 4.7 4.0 4.4 4.5 4.1 4.1 3.2
##   [91] 4.2 4.6 3.8 5.4 5.0 4.4 3.8 4.9 4.6 3.8 4.3 5.4 3.9 3.7 5.2 4.6 4.0 2.6
##  [109] 3.9 4.2 4.8 4.7 3.3 4.3 4.0 4.9 4.0 3.1 3.6 4.6 3.3 3.4 5.2 4.2 3.9 4.7
##  [127] 3.7 3.4 4.9 4.9 5.3 4.2 4.2 4.8 5.6 4.7 3.3 4.2 3.4 3.5 6.3 4.1 5.9 4.5
##  [145] 4.8 4.7 4.8 4.7 4.4 3.4 4.4 5.8 5.7 3.9 3.7 2.7 4.3 5.3 5.3 4.4 4.2 3.5
##  [163] 4.4 4.9 5.3 4.5 5.0 3.6 5.2 3.5 4.7 5.6 5.0 5.2 3.1 3.4 4.5 5.9 3.7 4.5
##  [181] 3.9 3.7 6.4 5.2 5.2 3.7 4.8 4.4 5.9 3.7 6.2 4.0 4.3 3.9 5.6 6.4 4.7 5.2
##  [199] 3.1 2.3 3.4 3.3 4.0 3.4 5.1 1.9 3.8 4.2 3.0 3.0 4.1 3.8 5.2 3.5 4.0 4.3
##  [217] 3.7 4.8 4.0 4.2 4.6 3.7 4.4 3.2 4.2 3.4 4.6 5.2 3.2 4.8 4.5 4.1 3.9 4.6
##  [235] 4.2 4.0 4.6 6.1 4.5 4.8 4.6 5.8 3.7 4.5 4.0 4.0 4.5 4.7 5.8 4.5 5.8 4.8
##  [253] 2.9 5.3 5.2 4.2 5.1 4.0 3.3 2.9 6.1 6.3 2.9 3.6 2.8 2.8 4.6 3.9 3.1 3.4
##  [271] 4.1 2.6 4.6 5.2 3.9 5.5 3.6 3.7 2.9 5.3 3.8 4.5 4.9 4.2 5.1 5.0 4.3 4.0
##  [289] 3.8 5.2 5.5 4.5 3.6 3.7 6.0 3.7 5.7 3.8 3.8 3.8 5.3 4.5 2.8 3.7 5.4 3.7
##  [307] 4.2 5.3 5.2 7.1 3.6 4.2 3.5 4.7 5.9 4.0 4.6 3.1 4.4 5.2 4.5 5.9 3.3 4.4
##  [325] 5.2 4.2 5.2 3.8 5.2 4.6 4.7 5.2 4.4 3.8 3.2 2.7 5.0 4.6 5.0 6.8 4.2 4.0
##  [343] 3.1 3.4 4.5 4.1 3.7 5.3 5.7 4.7 4.5 4.9 3.1 5.1 4.6 5.0 5.5 3.7 4.1 4.3
##  [361] 4.9 5.9 3.3 3.8 4.0 4.1 3.7 3.9 6.7 4.9 3.3 3.5 5.1 4.1 5.1 2.8 4.7 5.1
##  [379] 3.3 5.0 3.2 3.3 4.7 3.5 5.3 4.0 5.1 5.7 3.8 5.0 3.4 2.9 6.5 5.7 5.0 5.1
##  [397] 6.3 4.3 3.2 5.2 4.6 4.7 4.2 5.3 5.0 4.6 4.7 5.4 3.4 4.6 5.3 4.9 5.6 2.7
##  [415] 3.8 3.6 4.2 4.3 3.6 4.9 5.9 4.4 3.7 4.2 5.4 4.7 4.0 4.5 4.8 5.3 4.9 3.5
##  [433] 3.2 3.9 3.7 3.9 4.8 3.3 6.3 2.9 2.7 2.8 3.4 3.1 4.2 5.3 3.0 5.2 3.7 5.1
##  [451] 4.3 2.6 2.9 6.1 4.5 5.4 3.1 4.3 6.0 4.1 4.6 4.3 5.9 3.7 4.9 2.5 3.4 4.9
##  [469] 3.9 5.6 5.0 5.5 3.3 4.3 4.4 4.9 4.4 5.7 4.5 2.5 4.9 4.6 4.2 5.5 5.6 4.7
##  [487] 4.8 5.0 3.4 4.4 2.9 4.9 5.6 4.0 4.7 5.6 5.9 4.0 2.5 4.1 5.3 5.0 5.2 2.4
##  [505] 5.4 4.2 4.9 5.7 4.0 4.9 4.8 6.0 6.1 2.9 3.7 5.7 5.1 4.0 4.2 4.9 5.0 3.9
##  [523] 4.2 5.4 5.4 4.1 5.2 3.9 5.2 5.4 4.0 3.5 6.6 4.9 6.0 3.4 3.4 4.3 5.1 4.3
##  [541] 2.4 4.8 3.6 3.2 2.6 3.7 5.8 5.7 4.1 4.2 4.4 3.5 4.9 5.4 6.0 3.6 3.0 4.4
##  [559] 6.7 4.4 5.7 4.0 5.0 6.3 3.0 4.8 5.9 4.5 3.9 5.6 4.0 2.8 5.8 3.8 3.6 5.9
##  [577] 6.1 5.0 4.7 5.3 4.2 3.8 6.0 5.1 3.7 3.0 6.1 3.3 6.8 4.1 3.4 5.1 4.2 5.4
##  [595] 5.5 3.9 3.9 3.4 4.8 4.9 4.2 4.9 3.9 4.6 4.6 3.8 5.7 5.0 4.4 5.8 4.5 5.9
##  [613] 4.1 3.0 4.9 6.2 4.1 3.3 3.3 5.4 5.6 5.3 3.5 5.5 4.9 4.8 5.6 4.7 4.4 5.5
##  [631] 3.3 5.8 4.9 4.0 4.3 3.3 4.1 4.0 3.4 1.8 5.2 3.7 3.2 4.6 6.2 5.5 3.8 3.9
##  [649] 4.8 4.5 4.3 4.8 4.9 5.2 5.2 5.8 4.9 4.7 4.8 3.4 2.4 3.4 4.3 5.5 3.3 5.3
##  [667] 5.1 3.7 5.7 6.3 3.8 3.6 4.6 4.8 6.7 3.5 6.0 4.7 4.3 4.7 2.6 4.9 3.9 3.9
##  [685] 5.2 4.7 3.0 5.1 4.2 3.5 4.8 2.1 4.4 3.5 4.4 5.2 4.8 5.8 5.3 5.2 4.4 4.5
##  [703] 3.7 3.8 5.5 5.2 4.9 3.1 5.8 3.6 4.9 5.7 6.1 4.9 3.8 5.5 4.4 4.6 3.9 3.9
##  [721] 4.8 3.7 3.1 5.9 5.4 4.3 5.2 3.6 5.5 4.1 4.6 3.2 4.8 3.9 5.8 4.9 4.1 4.7
##  [739] 5.3 5.2 4.5 3.4 3.2 4.8 4.9 5.6 3.9 5.2 5.1 5.2 4.5 5.2 5.5 4.4 4.4 2.6
##  [757] 4.4 3.3 4.0 4.2 3.8 5.1 4.7 5.1 2.6 4.6 4.4 5.0 3.7 5.1 3.1 4.5 6.1 4.0
##  [775] 5.7 5.4 4.0 4.5 3.2 3.7 5.6 4.9 6.6 4.6 5.0 3.9 4.6 4.1 4.6 5.1 4.7 3.9
##  [793] 3.5 6.1 3.1 3.7 4.9 3.4 3.9 5.4 4.4 5.5 4.5 4.1 4.7 5.4 4.2 3.9 4.6 4.9
##  [811] 5.3 4.2 5.3 3.9 5.4 3.6 6.1 4.1 3.4 5.7 4.7 4.5 5.1 3.7 5.3 6.3 3.8 3.4
##  [829] 3.6 4.2 4.6 3.4 4.3 3.6 5.3 4.2 5.4 5.8 4.4 4.6 5.5 4.8 3.4 4.1 3.9 4.3
##  [847] 6.2 4.4 2.6 4.4 6.6 2.8 5.2 4.7 4.4 4.2 3.4 4.2 3.3 4.0 4.4 6.6 4.3 4.5
##  [865] 5.0 4.6 5.4 4.6 4.7 5.3 4.8 5.3 3.7 4.5 4.2 5.4 2.4 4.4 3.2 5.8 4.1 3.5
##  [883] 4.7 4.9 5.0 5.0 4.4 3.1 3.8 5.0 5.2 4.4 3.3 2.9 5.5 5.8 3.5 3.3 5.0 5.5
##  [901] 4.8 5.0 5.8 5.7 5.5 4.6 4.9 4.1 4.3 4.7 5.5 3.6 6.1 5.0 5.2 5.0 5.9 4.2
##  [919] 5.2 3.3 3.1 5.5 5.5 3.9 4.2 4.7 3.5 5.7 5.9 4.4 4.1 4.9 5.9 3.3 4.5 5.1
##  [937] 3.6 3.8 3.9 3.9 4.6 5.6 3.7 4.7 4.0 5.7 5.6 4.0 3.8 4.9 4.8 4.0 5.2 5.4
##  [955] 3.6 5.2 3.6 5.3 3.8 5.2 5.1 4.8 3.5 4.2 5.4 4.8 4.8 3.8 4.7 6.5 4.5 6.3
##  [973] 5.2 3.4 3.6 4.1 3.5 4.4 5.3 4.3 5.2 5.1 3.7 5.2 4.2 4.7 4.7 3.2 4.4 4.5
##  [991] 4.2 3.4 5.9 4.4 4.5 4.4 2.6 5.4 3.8 4.0
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.2 5.2 5.1 4.9 4.7 4.6 4.4
## 
## $jack.boot.se
## [1] 1.039663
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
## [1] 0.9032437
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
##   2.697484   4.016818 
##  (1.139661) (1.864988)
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
## [1]  0.41112642  1.45708568  1.22345396 -0.07784998  1.07346009  0.43494951
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
##    [1] -0.198488040  1.149498852  1.175430023  1.142686669  0.863244717
##    [6]  0.366865373  0.521650013  0.013484985  0.475139174  0.130538587
##   [11]  1.348750960  0.908878053  0.583414192  0.415516159  0.807354737
##   [16]  0.407524472  0.769369407  0.779890659  1.278643255  0.627933988
##   [21] -0.770112806  0.471222320  1.809216320  0.479752180  0.049819643
##   [26]  1.183275148  0.868965687  1.306732849 -0.044182955  0.853379765
##   [31]  1.029685113  1.325528723 -0.209726907  0.344437882  0.319552869
##   [36]  0.634070024  1.064680310  0.435300128  1.344767143  1.178299924
##   [41]  0.302970983  0.658391570  0.772566148  0.937525072  0.363894761
##   [46]  1.040496581  0.979301982 -1.114949797  0.698064014  1.399023988
##   [51]  0.919656791  0.726711029  0.459650663  0.219307363  1.998880216
##   [56]  0.649555210  1.149971617  0.690343640  0.819227106  0.491322900
##   [61]  0.641975189  0.924197401  1.453650428  0.771427945  1.429464565
##   [66]  0.325324224  0.059254129  0.098615945  0.324106903  1.015999550
##   [71] -0.674525162  1.126620632  0.595567411  1.833282376  1.202192983
##   [76]  0.889434464  1.287680505  0.316620457  1.425761029  0.533831819
##   [81] -0.408409519 -0.429021358  1.029041861  0.345323584  0.095309518
##   [86]  0.247011656  1.166630737  0.699007695  0.082068929  1.670065988
##   [91]  1.314019437  0.698078377  0.407405510  0.728350712  0.505468632
##   [96]  0.894687386  0.028768180  1.220367124  1.052917569  0.816507072
##  [101]  0.983916657  0.854250566  0.875206784  0.846004731  0.483055357
##  [106]  0.676271040  0.579340818  0.315028386  1.140781722  0.430149006
##  [111]  0.293280981  0.162592575  1.430318492  0.546896952  0.591346646
##  [116]  0.308002843  0.252763701  2.041060135  0.570653991  0.520029447
##  [121]  1.785223097  0.692166155  0.690717955 -0.085326144  0.449099690
##  [126]  1.585895699  0.263275158  1.158371884  0.891038334  0.406195657
##  [131]  0.897001154  1.594626921  0.871890752  0.479655178  1.404490038
##  [136]  0.438220556  1.826853962  1.728297382  0.650061931  0.537949026
##  [141]  1.810306513  0.079599687  0.944128640  0.364325523  0.507922839
##  [146]  0.689841098  1.570271504  1.006836782  0.755190296  0.612408064
##  [151]  0.410839818  0.397776442  0.129080517 -0.278314375  0.811949825
##  [156]  0.234212497  0.195101600  0.512256075  0.736265031  1.663856987
##  [161]  0.418676936  0.555302170  1.401898342  0.258839836  0.488894780
##  [166]  1.232198412  1.969703750  0.789651861  0.854818920 -0.356727296
##  [171]  0.921419204  0.829790455  1.254290462  0.304923192  1.616387501
##  [176]  1.077135418  0.913991611  1.586162473  0.376578627  0.811165589
##  [181]  0.929213902  0.555771236  0.616181507  1.689607666 -0.228440095
##  [186]  0.616591623  0.284253221  0.623954563  1.241251392  0.774255981
##  [191]  1.113894938  0.276350054  1.533847567  0.581466966  0.196597802
##  [196]  1.137911048  0.158974203  0.107407024  0.112550025  1.021585308
##  [201]  2.004829931  1.526855398  1.034780062  0.459332580  1.262573161
##  [206]  0.011584252  1.092317458  1.242554842  0.557704867  1.184574954
##  [211] -0.262731627  0.514323364  0.716052565  0.785874546  1.923775432
##  [216]  1.748732388  1.873576327  0.964380569  0.870443849  2.209578682
##  [221]  0.837899221  0.165092425  0.295441798  1.167708163  0.386850959
##  [226]  0.141153637  0.495766495 -0.072790110  0.610915854  0.901025455
##  [231]  0.766196205  1.251707381  0.360559926  0.843965461  0.824381356
##  [236]  0.491156018  0.623230167  2.234615861  0.279354046  0.476999595
##  [241]  0.081048151 -0.188485074  0.105436898 -0.368655957  0.637170021
##  [246]  0.257613142  0.943062598 -0.278540228  0.339276345 -0.039320069
##  [251]  0.761168367  0.436646699  0.347222554  1.053600489  1.258852292
##  [256]  1.035698950  0.004149930  0.878085346  1.713616958  1.860968969
##  [261]  1.301107595  0.733116352  0.180920469 -0.161182911  0.245072520
##  [266]  1.246608990  0.538729191 -0.408369566  1.262071314  0.555059156
##  [271]  1.644823561  1.442456928  1.115124783  0.505879231  0.041958097
##  [276] -0.168768179  0.612359814  1.434781503  1.584069034  0.638095984
##  [281]  0.469133013  1.249268797  1.126338932  0.302408271  0.138591485
##  [286]  0.116121276  0.441686348 -0.074193197  0.464230712  0.054061801
##  [291]  0.547404217  1.504885088  1.220473637  0.788816147  0.462879541
##  [296]  1.283535344  1.441473927  1.099629599  2.190163258  0.232268535
##  [301]  0.737222627  0.598357324  0.565588739  1.043299172  0.815881886
##  [306]  0.829528577  0.203715035  0.894489480  1.486205275  0.663922132
##  [311]  0.500381484  1.876294899  0.349826177  0.863279390  0.557392590
##  [316]  0.607004025  1.570271504  0.816272162  0.868724252  0.797913498
##  [321] -0.571210603  1.731082019  1.035440590  0.992459496  0.519375960
##  [326]  0.173392188  0.662231878  0.897254793  0.883243898  1.048473142
##  [331]  0.468441390  0.332600350  0.741152142  1.760658966  0.921419204
##  [336]  0.161019702  0.186991587  2.054900469  0.694939014  0.734089251
##  [341]  1.504885088  0.682504088 -0.089528342  0.655402248  0.732840711
##  [346]  1.880194843  0.291471075 -0.002464253  1.755597395  0.964192487
##  [351]  0.942496253 -1.709972174  1.300617617  0.630714381  0.911149620
##  [356]  1.924402804  0.626140243  0.789745764  0.698078847  0.693215908
##  [361]  0.985238913  0.609731304 -0.696678392  0.710872593  0.908666339
##  [366]  0.170751782 -0.513590916  0.854592254  0.988310513  1.483099826
##  [371]  0.104398092  0.431445764  0.323981266  0.719318371  0.925977602
##  [376] -0.054593286  0.385653560  1.466551463  1.613206566  0.107734491
##  [381]  0.374948469  1.289305420  1.035698950  1.149834925  1.429584787
##  [386] -0.298159711 -0.219917546  0.913167721  0.173678763  0.708744669
##  [391]  1.761078583  1.968641901  0.022885863  0.659248983  1.366075576
##  [396]  0.763120213  0.342580745  0.886631177  0.775377428  0.573544892
##  [401]  0.405778576  0.362985848  1.342175138  1.086924771  0.104006877
##  [406]  1.925331839  1.476143754  0.094704236  0.596422764  0.556278373
##  [411]  0.080703769 -0.049059402  0.316051753  0.292733584  0.480135684
##  [416]  0.760037374  0.371638868  0.338694206  0.908175818  1.158163074
##  [421]  1.001206826  0.924908177  0.679771813  0.663020679  0.670022737
##  [426] -0.137362783  0.582145316  0.497634315  1.819912957  0.592321111
##  [431]  0.189974312  0.640746870 -0.432068567  0.888987610  0.468463638
##  [436]  1.246608990  1.603838847  1.451054991  0.315001054  0.925813958
##  [441]  0.355144226  0.440088542  0.164921852  1.157381542  0.141068983
##  [446]  0.625601073  0.046196986  0.885171022  2.062309847  0.832979084
##  [451]  0.348676305  1.435554015  1.020590666  0.562447272  0.818707938
##  [456]  1.183151229  0.214691399  0.598869344  0.617090265  0.574202012
##  [461]  0.902124850  1.585056489  0.446162561  1.136856054  0.935881138
##  [466]  0.981501454  0.760438832  1.120635827 -0.102957299  2.008106546
##  [471]  0.707697550  1.563844793  0.847695967  0.759662372  1.992237631
##  [476]  0.138578475  0.541344993  0.378193935  0.968787294  0.912642382
##  [481]  1.204962070 -0.125598173  0.208385901  0.951585270  0.273227621
##  [486]  0.716774525  0.377052172  1.184425146  0.155894571  0.422538496
##  [491]  1.052780572 -0.275437790  0.239715254  0.339194660  0.250488285
##  [496]  0.388873668  0.822834335  0.216341016 -0.271912711  1.035508803
##  [501]  0.760498713  0.965721682 -0.048922959  0.451298504  0.615795785
##  [506]  0.534232548  0.356520084  1.630786346  0.739351738 -0.378504907
##  [511]  0.772382071  0.390120766  0.585558120  1.170579186  1.635842837
##  [516]  1.182514768  0.408270549  1.554941311  0.306240170  1.238792285
##  [521]  1.507375031  0.888881007  0.609143255  1.514649344  0.756739476
##  [526]  0.377414024  0.893418847  1.401898342  0.183410427  0.927930883
##  [531]  1.781224700  0.549839446  0.922621345  0.927530563  0.995322843
##  [536]  0.440071130  0.505010906  0.664271792  0.596002223  1.016235000
##  [541]  1.716403595  0.668227536  0.246557131  1.772872485  0.484974374
##  [546]  1.096864569  0.937611228  1.288747798  0.899418468  0.080843867
##  [551]  0.733455767  0.296595464  1.807587131  0.528770891  0.353988527
##  [556]  0.688220664  1.343113745  1.220501553  0.461620729  0.353203579
##  [561]  1.123256448  1.220634221  0.654496100  0.766010718  0.560261556
##  [566]  0.300161574  1.687713881  1.245298295  1.121762930  0.482564183
##  [571]  0.910917771  1.028058787  0.604602990  1.266659945  0.201036204
##  [576]  0.687400139  1.759736748 -0.068007335  0.997311225  0.542431367
##  [581]  0.718226318  0.315990141  0.368786969  0.871163251  0.717970419
##  [586]  1.268439481  0.519471634  0.676249247  0.888120334  1.191068878
##  [591]  0.890951407  0.968429343  0.118418523  1.132146373  1.613327524
##  [596]  1.217382343  0.259612524 -0.032617657  0.415317893  0.997273242
##  [601]  0.042499298  0.436164227  0.469045524  0.635896288  0.947026226
##  [606]  1.126959108  0.964643602 -0.560987582  0.881235515  0.751543459
##  [611]  0.939298405  0.641930836  0.682102431  0.973835571  1.564620954
##  [616]  1.093109395  0.623317792  0.487479625  0.860209165  0.714215422
##  [621]  1.197337451  1.025836920  0.775141528  0.759689908  0.504445670
##  [626]  0.285219886  1.128741815  0.948256961  0.188429519  0.514322351
##  [631]  0.654332220  0.965429149  1.157986676  1.401368024  0.824678875
##  [636]  0.068139866  0.661037742  0.365250146  1.140628692  0.142633509
##  [641]  0.145886266  0.562322237  1.061267537  0.616604649  0.958842724
##  [646]  0.599515423  0.172017724  0.925223469  0.525684957  1.283403248
##  [651]  0.281666301  0.802038779  0.063129166  1.236581297  1.055766865
##  [656]  1.070706296  0.687206839  0.541127311  0.738804959  0.355387948
##  [661] -0.338679766  0.329350073  0.175204497  1.567699533  0.963081109
##  [666]  0.837828827  1.439981082  0.257701926  0.049917971  0.514923148
##  [671]  1.166231727  0.367458165  0.134878884  1.094528788  0.688641444
##  [676]  0.947867312  1.462753596  0.239715254  1.267841624  0.141578556
##  [681]  1.130249601  0.590433907  2.195870874  1.257193171  0.435540391
##  [686]  1.206016285  0.905801880  0.319624933  0.734097103  1.209622797
##  [691] -0.033534147  1.192739096  0.386915787  0.197538739  1.077211902
##  [696]  1.714640409  0.509038538 -0.024728606  1.032126852  0.031056430
##  [701]  1.484890683  1.455194339  1.016599739  0.914313666  0.714546796
##  [706]  1.317696117  0.483685965 -0.012324531  0.739728972  0.073641098
##  [711]  0.860197490  0.569557335  1.502412659  0.641495243  0.588384572
##  [716]  0.467098454 -0.706960888  0.741375162  1.017046670  0.805604590
##  [721]  1.406528974  1.444710592  1.130736948  1.285601972  0.812445433
##  [726]  1.249380045  1.117197020  1.057804346  0.502333290  0.730292330
##  [731]  1.137317600  0.558034431  0.284682891 -0.195910515  1.598216326
##  [736]  1.450562147  1.184973939  0.320621409  0.547412208  0.873740689
##  [741]  1.054713767  0.413168427  0.212375270  0.545018738  0.649878708
##  [746]  0.045153260  0.764256737  0.889015007  0.803771956  0.362985848
##  [751]  0.137427239  1.234572002  1.265049879  1.205293040  0.036172560
##  [756]  0.945558017  1.646084719  0.403119428  0.924240406  1.424514218
##  [761]  1.277537416  0.860209165 -0.341952087  0.769551427  1.408282829
##  [766]  0.623183428  0.721424874  1.266659945  1.529092627  0.927333823
##  [771]  0.629744430  0.439078231  0.458354344  1.442456928 -0.039007983
##  [776]  0.881246557  1.205207770  1.257976787  0.926413186  0.820343438
##  [781]  0.540555195  1.175666918 -0.769334102  0.714506849  0.567290983
##  [786]  0.980893647  0.299189703  0.209202447  0.597722351  0.979690247
##  [791]  0.567757422  1.171675291  0.919419849 -0.188677417  0.102089790
##  [796]  0.512306198  0.753345836  0.425921580  0.375226468  0.799042381
##  [801]  0.720590852  0.360596292  0.435373793  0.802038779  0.680127478
##  [806]  0.345245319  0.734601040  0.747005481  0.295920726  0.362985848
##  [811]  0.555148807 -0.351506275  0.915693739  0.866901601  0.869736617
##  [816]  2.116678984  0.760651822  1.146920861 -0.060666430  0.627579726
##  [821]  1.302614242  0.887141052  0.345159833  1.456205939  0.283165084
##  [826]  0.514708131  0.924321261  0.907771927  1.326888640  0.257657242
##  [831] -0.122156803  0.191360228  1.364157621  0.359441330  0.020056896
##  [836]  0.142568723  0.475675214  1.159807009  0.068150011  1.149443941
##  [841] -0.554392320  0.371009693  0.678898255  0.132631058  1.526856307
##  [846] -0.058298919  0.705786102  1.039785956  0.615937790  0.386375619
##  [851]  0.575886437  0.216519160 -0.473031804  0.568987380  1.532799038
##  [856]  1.554941311  0.382740479  0.006355929  0.366709076  0.299173857
##  [861]  0.628266340  1.379654074  0.551443355  1.771933682  1.386411765
##  [866]  1.223047284  0.341724940  0.723030861 -0.036745261  1.012325605
##  [871]  0.528429189  0.414342464  1.425296948  0.281253317  0.722197359
##  [876]  0.843965461  2.095808947  0.383255991  0.823734189  0.032370823
##  [881] -0.200280793  0.693709469  0.525676717  1.368033220  0.799452960
##  [886]  0.905847777  1.390362857  0.466568778  0.465798388  1.217274186
##  [891]  0.411376893  0.219654487  0.701028775  0.696597282  0.201036204
##  [896]  1.427857643  1.038942044  0.042393837  0.871695489  0.865286239
##  [901]  0.174067685  1.898692513  0.725326652  0.679896567  0.714028624
##  [906]  1.562826355  0.892355633  0.779256858  1.156195225  0.633535417
##  [911]  0.266032535 -0.307811044  0.952212987  1.111651710  1.276007880
##  [916]  0.720058179  0.551576604  0.448818243  0.376625448  1.428898587
##  [921]  1.352947540  0.742114101  1.514295649  0.629864283  0.083010256
##  [926]  0.930478022  0.147492887  1.492207008  1.096136002  0.731315024
##  [931]  1.236833887  0.316199207  0.642446265  0.052823782  0.878281708
##  [936]  1.236864181  0.963486426  0.355701033  1.336622856  0.672143796
##  [941]  0.660609990  0.460226150  1.650517278  0.933588279  0.266781235
##  [946]  1.307130410  1.039100312  1.207567412  0.689019679 -0.177686559
##  [951] -0.048931426  0.196042083  0.193050249  0.155623513  0.544499162
##  [956]  1.005532291  0.964385304  0.749714419  0.569218451  1.406877075
##  [961]  1.462273426  0.393173415  0.576693466  0.460226150  1.100696728
##  [966]  0.993287673  0.462556262  0.225230690  0.285332065  1.469053011
##  [971]  0.484438090  1.203566100  2.045948073  0.394543470  1.098938639
##  [976]  0.654943420  0.953326746  0.800378717  0.537901868  0.672225015
##  [981]  0.363346713  0.439864682  0.545908895  2.124521930  1.954713140
##  [986]  0.928378913  1.219271246  0.327031292  0.688262016  0.149895213
##  [991] -0.591191366  0.720665571  0.670798456  1.752709498  1.005661906
##  [996]  2.024587501  0.742256817  0.365788079  0.770124897  0.360349749
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
##   0.67156151   0.42157121 
##  (0.13331252) (0.09426387)
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
## [1] -0.5101272  0.6002034 -0.6645057 -0.5481810  0.2784209  0.5010535
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
## [1] 0.0104
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9104069
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
## t1*      4.5 0.01941942   0.9318261
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 4 5 6 8 9 
## 2 2 3 1 1 1
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
## [1] 0.0139
```

```r
se.boot
```

```
## [1] 0.9098817
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

