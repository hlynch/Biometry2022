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
## 0 1 2 3 4 5 9 
## 1 3 1 1 1 1 2
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
## [1] 4e-04
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
## [1] 2.760556
```

```r
UL.boot
```

```
## [1] 6.240244
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
##    [1] 4.6 4.4 4.7 4.5 4.5 4.2 4.0 3.8 6.2 3.6 4.0 3.1 4.2 3.8 4.7 5.9 4.0 4.2
##   [19] 5.0 2.1 4.1 4.0 7.0 3.3 5.7 4.5 5.9 5.5 6.8 6.2 4.8 4.4 5.3 4.3 5.2 3.4
##   [37] 5.6 4.6 7.1 4.2 5.3 3.6 3.8 2.4 4.5 4.6 4.6 5.8 4.2 3.8 4.0 2.7 4.4 5.0
##   [55] 4.7 5.8 4.8 4.1 2.7 5.4 4.9 5.1 2.8 4.1 5.4 3.5 3.1 4.3 4.7 4.5 4.3 4.1
##   [73] 3.7 4.4 4.0 3.9 4.0 4.0 5.7 5.8 4.4 4.0 4.2 3.2 5.0 4.5 3.2 5.3 3.8 5.2
##   [91] 5.5 3.5 4.8 4.3 3.1 4.8 4.0 4.6 4.0 5.2 4.9 5.3 5.2 2.8 2.9 5.1 5.3 1.6
##  [109] 5.1 5.2 3.2 3.2 4.2 6.6 5.7 5.4 3.6 3.8 4.9 4.2 4.0 4.3 4.6 4.3 4.1 5.0
##  [127] 5.3 3.7 3.7 4.7 4.1 2.8 5.9 5.4 4.4 3.9 4.2 4.1 6.0 3.4 5.3 4.0 4.9 4.4
##  [145] 4.2 3.6 5.9 4.6 4.4 4.0 4.3 2.4 4.1 6.0 4.1 4.5 3.3 2.4 5.2 7.3 3.7 2.9
##  [163] 5.3 4.1 4.7 4.7 4.3 5.7 4.8 4.8 4.2 3.6 3.4 4.5 3.8 2.8 5.6 4.0 2.6 4.2
##  [181] 3.8 4.8 4.8 4.3 4.9 4.2 4.3 4.6 3.9 5.0 5.4 5.0 3.3 3.7 3.4 4.6 4.3 6.1
##  [199] 4.6 4.7 4.5 5.9 3.6 3.9 3.3 2.8 3.9 4.6 5.3 4.3 3.9 3.1 4.3 4.0 5.1 5.9
##  [217] 3.1 3.4 4.6 4.5 5.3 4.0 3.6 5.1 5.3 4.4 2.9 3.0 5.1 5.8 3.8 4.0 4.6 3.3
##  [235] 4.1 5.3 7.6 4.2 2.4 4.2 5.6 3.1 5.5 5.4 3.9 3.3 4.3 5.3 3.3 5.5 3.1 6.1
##  [253] 4.1 3.6 5.3 1.9 5.8 3.4 5.2 4.9 5.4 4.8 5.1 5.4 5.2 3.3 5.6 5.3 3.9 4.1
##  [271] 5.0 3.9 4.6 3.4 4.3 3.5 4.6 5.7 4.8 4.5 5.1 4.1 5.4 3.9 5.0 3.1 4.4 4.7
##  [289] 3.5 5.0 4.1 4.7 6.2 2.4 4.0 5.2 3.5 3.2 4.7 4.4 3.5 3.8 4.9 5.0 3.8 3.3
##  [307] 3.8 3.5 4.5 5.5 4.6 4.7 4.7 5.2 4.4 4.0 3.8 4.4 6.1 6.8 4.1 4.5 3.3 4.7
##  [325] 6.7 3.7 3.6 3.3 3.5 5.9 4.5 4.6 4.3 5.0 4.5 5.7 3.6 3.2 4.6 3.9 3.4 4.8
##  [343] 5.5 5.4 3.9 3.9 4.3 3.2 4.4 5.7 3.5 3.8 4.0 4.6 2.6 3.7 4.2 5.1 1.8 2.2
##  [361] 5.5 4.3 5.1 6.0 4.3 4.6 4.5 5.1 2.6 6.0 4.3 4.8 2.6 6.2 4.1 3.0 3.9 5.4
##  [379] 4.1 5.2 6.1 3.7 4.8 2.3 5.3 4.7 4.1 3.8 4.3 4.7 5.2 3.8 4.4 5.4 4.8 5.6
##  [397] 3.5 4.4 4.0 6.3 1.6 5.1 4.7 4.6 4.2 4.1 6.2 4.8 4.7 3.9 6.8 6.0 3.8 6.2
##  [415] 4.9 5.7 4.9 3.8 4.3 5.5 5.0 3.1 3.0 4.7 3.5 4.5 6.2 4.7 3.1 3.7 4.1 5.3
##  [433] 4.0 5.6 4.6 5.6 5.8 3.3 4.1 4.7 5.0 3.8 3.0 5.1 5.7 3.6 2.5 6.3 5.0 4.5
##  [451] 3.6 3.6 5.0 3.3 4.5 5.6 5.6 5.5 4.5 3.3 2.7 4.5 3.4 4.7 5.2 3.5 3.5 5.0
##  [469] 6.0 5.3 5.6 5.4 3.9 4.2 3.9 3.4 3.8 4.3 6.0 4.4 3.4 5.2 4.6 4.4 3.1 4.9
##  [487] 3.5 6.3 5.7 4.8 3.8 3.0 3.3 2.4 3.4 6.1 4.4 4.9 4.2 6.0 4.8 4.3 4.2 4.3
##  [505] 5.3 4.9 4.8 3.4 4.4 4.4 5.5 4.1 3.6 3.7 6.4 4.9 5.9 4.7 4.4 3.1 3.8 3.5
##  [523] 3.6 3.5 6.2 5.1 3.5 5.1 4.4 5.5 5.2 5.0 3.2 3.7 5.5 5.9 3.2 3.6 5.5 3.9
##  [541] 4.0 3.7 4.2 3.4 4.5 5.3 4.0 5.7 4.2 3.7 4.8 3.9 4.5 4.4 3.8 4.9 3.5 4.4
##  [559] 4.5 3.6 3.8 5.1 6.2 3.9 4.1 5.0 2.6 6.0 2.7 2.3 2.7 5.4 5.1 3.2 3.8 3.4
##  [577] 4.0 3.5 5.2 4.0 3.1 6.0 5.7 3.3 4.0 3.4 3.8 4.2 5.5 4.8 4.5 3.5 3.3 5.4
##  [595] 4.2 4.4 4.2 3.7 3.8 4.7 5.7 3.8 4.9 5.6 4.8 6.0 4.5 3.9 4.3 6.8 4.8 4.1
##  [613] 4.7 6.1 2.9 3.3 4.6 3.9 4.6 5.0 4.7 5.2 4.5 5.2 5.0 5.0 4.3 5.9 4.3 4.2
##  [631] 5.0 5.2 3.8 4.6 6.1 3.8 4.8 3.8 4.4 5.3 3.9 4.0 4.6 4.3 5.1 2.7 4.4 5.8
##  [649] 3.9 4.0 5.3 4.3 5.0 4.3 3.8 4.0 4.1 4.0 4.7 5.2 4.6 5.5 4.2 5.1 3.7 6.5
##  [667] 4.6 2.7 4.4 3.5 4.4 4.1 5.3 4.2 2.5 6.6 3.5 4.4 4.3 4.1 4.8 5.7 3.9 4.7
##  [685] 4.9 3.1 5.0 4.5 3.8 3.0 5.2 4.4 5.6 3.4 3.5 6.4 4.6 3.2 4.3 4.7 5.9 5.2
##  [703] 5.3 3.6 4.3 3.8 4.5 4.7 4.3 5.0 5.1 5.6 3.5 5.0 3.7 5.6 4.7 4.2 4.1 4.5
##  [721] 3.8 5.9 4.5 4.7 5.1 3.8 4.1 4.8 3.8 5.0 3.3 3.4 4.2 2.9 4.1 4.5 3.7 4.7
##  [739] 4.3 4.0 5.1 4.8 4.1 6.6 5.8 3.3 2.5 4.6 3.6 3.3 6.2 5.9 4.3 4.8 3.3 4.4
##  [757] 4.7 4.0 3.5 4.4 4.2 4.1 5.9 4.7 4.9 5.2 5.2 4.0 3.2 3.3 4.0 6.6 3.8 4.4
##  [775] 3.6 4.9 5.2 2.2 4.9 4.3 3.8 4.3 5.3 5.5 4.3 4.8 3.8 4.6 5.5 4.7 3.4 4.8
##  [793] 5.4 5.6 5.4 5.2 5.4 4.0 2.4 6.6 4.9 3.4 4.6 3.3 5.8 4.4 5.3 3.8 5.3 5.7
##  [811] 5.3 3.0 5.2 5.9 4.9 3.0 5.6 5.4 3.5 3.3 4.8 5.2 5.7 3.8 2.6 5.1 5.5 3.7
##  [829] 3.8 4.1 4.7 6.0 4.5 4.7 3.8 4.0 3.7 4.6 4.6 4.2 4.9 3.9 3.1 5.5 5.7 3.9
##  [847] 3.8 3.5 5.5 6.7 2.8 4.5 5.0 4.8 4.4 4.0 4.5 3.3 3.3 3.8 4.4 3.9 3.7 6.4
##  [865] 4.9 2.7 5.2 4.2 3.8 6.3 5.3 3.3 3.7 4.2 4.5 4.6 5.5 5.4 5.8 5.5 4.7 5.4
##  [883] 4.7 4.4 4.6 5.1 4.8 5.4 3.3 3.5 5.8 4.0 4.8 5.8 3.2 4.2 4.8 5.0 4.1 5.1
##  [901] 5.2 4.7 5.5 5.9 4.4 4.8 3.8 3.8 4.6 3.7 5.2 4.0 5.6 3.6 5.2 4.7 5.3 4.6
##  [919] 4.0 5.4 3.7 4.1 4.1 4.1 3.8 3.9 5.0 6.0 2.7 6.0 5.1 5.0 4.3 4.6 5.0 3.8
##  [937] 5.5 5.4 5.1 6.0 6.0 3.7 3.8 4.3 4.9 4.8 3.3 5.1 5.1 4.6 5.3 4.6 3.1 6.6
##  [955] 4.8 3.7 3.6 5.8 4.9 4.8 5.9 4.4 5.2 4.8 5.4 5.1 4.5 3.2 5.9 5.0 5.1 4.1
##  [973] 3.8 3.1 3.6 4.6 5.8 4.8 3.7 3.8 4.5 4.4 3.5 5.3 4.9 4.0 3.2 4.2 5.0 5.0
##  [991] 4.6 4.1 5.1 4.1 4.1 4.3 4.7 4.6 5.9 4.4
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
## 2.6975 6.2000
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
##    [1] 5.7 6.0 3.8 4.0 4.4 4.4 5.3 3.3 5.2 3.7 3.2 6.4 4.3 4.6 5.0 4.5 4.9 2.3
##   [19] 2.3 3.6 5.1 5.9 3.6 4.3 3.5 3.5 4.4 4.2 5.2 4.7 4.3 4.6 4.8 3.5 3.3 5.2
##   [37] 3.4 4.2 3.4 3.4 5.1 3.7 2.9 4.4 3.4 3.5 4.3 3.3 3.6 5.8 4.9 4.8 4.6 4.2
##   [55] 4.8 4.0 6.0 5.1 4.5 6.0 4.6 5.7 3.5 2.8 5.2 4.2 6.8 4.3 4.4 5.0 5.1 3.9
##   [73] 3.6 3.6 3.4 4.6 2.7 4.0 3.2 2.5 4.2 4.5 4.5 4.6 4.8 3.2 4.1 5.9 4.1 4.4
##   [91] 4.0 3.9 5.8 3.2 2.6 4.5 3.6 4.5 5.2 3.8 3.6 4.0 4.8 4.2 4.9 5.1 3.8 6.4
##  [109] 3.8 6.9 4.1 4.7 4.7 4.5 3.8 4.7 3.8 5.2 3.6 5.0 3.8 4.9 6.0 4.9 4.3 3.7
##  [127] 5.9 4.4 4.4 4.1 2.8 4.1 6.0 5.1 3.1 4.0 4.0 5.0 4.4 5.0 3.7 4.9 4.6 5.1
##  [145] 4.0 5.1 6.0 4.9 5.4 3.1 5.0 5.2 3.5 5.5 4.0 3.1 4.6 3.9 6.0 5.3 3.7 4.7
##  [163] 4.7 3.7 4.7 4.4 4.6 4.4 4.8 5.7 5.8 3.2 4.5 5.8 4.2 3.8 5.1 4.4 3.1 4.5
##  [181] 5.2 5.2 6.0 5.1 2.7 5.7 4.6 3.6 4.4 5.0 4.1 5.0 5.3 4.0 4.3 4.4 4.7 4.4
##  [199] 3.7 3.2 3.6 3.5 5.3 5.4 5.8 3.7 5.1 5.3 4.7 2.6 4.6 2.8 5.3 5.1 4.0 3.4
##  [217] 4.7 6.0 5.1 5.8 5.1 4.9 5.8 4.0 4.7 3.9 4.3 4.7 4.2 5.3 3.7 4.2 4.5 3.9
##  [235] 5.9 3.9 5.1 4.6 5.9 4.1 3.0 4.1 5.0 3.6 4.5 4.1 6.2 3.6 3.1 3.6 4.6 3.6
##  [253] 5.5 5.2 4.3 4.5 4.3 4.6 4.0 3.4 4.1 4.5 4.7 4.4 5.3 3.4 5.2 4.5 2.8 4.3
##  [271] 4.7 4.1 5.6 3.2 4.5 4.1 4.5 5.7 5.0 5.5 5.8 3.4 4.3 4.6 5.0 5.0 5.0 4.3
##  [289] 5.1 5.3 3.3 4.6 4.6 4.8 4.3 5.0 4.8 3.9 4.9 4.9 6.4 3.9 3.3 4.1 3.3 6.3
##  [307] 4.6 4.0 4.0 4.6 5.4 4.3 5.5 5.0 4.5 3.4 2.4 5.2 3.6 4.6 4.2 4.2 3.8 4.3
##  [325] 4.7 2.9 4.3 4.1 5.7 5.1 3.8 4.8 3.2 3.1 2.4 4.5 3.4 4.0 5.0 2.9 5.0 3.3
##  [343] 4.6 3.5 5.0 3.7 5.2 5.5 5.0 4.2 4.6 5.5 3.4 5.2 3.3 3.9 6.0 4.0 3.9 4.3
##  [361] 4.6 4.9 6.2 5.4 5.4 2.9 6.5 4.8 4.3 6.2 3.7 4.6 4.0 2.8 4.2 4.9 5.8 4.3
##  [379] 4.8 5.1 3.1 4.7 4.5 4.3 6.2 5.4 3.6 4.0 2.3 4.3 6.7 2.5 4.6 4.2 3.7 4.1
##  [397] 5.0 3.7 5.9 4.4 3.5 5.5 2.9 4.5 3.6 4.3 5.0 4.6 4.1 5.1 4.8 5.4 5.3 4.4
##  [415] 5.2 3.2 4.7 3.2 4.6 3.9 4.7 4.1 5.0 4.1 4.6 3.1 2.7 3.9 4.0 4.0 4.1 4.2
##  [433] 4.6 4.7 4.5 4.8 5.1 4.7 3.7 3.8 4.3 5.3 5.1 4.8 3.8 4.4 5.8 6.4 3.7 4.9
##  [451] 5.4 3.7 4.9 4.7 5.7 5.3 5.2 5.3 2.9 4.4 4.0 5.8 3.7 3.6 3.8 6.2 4.0 4.3
##  [469] 4.2 5.6 5.7 4.6 3.4 4.4 3.8 5.1 6.4 5.4 5.1 4.4 4.0 6.2 3.4 4.2 5.1 4.0
##  [487] 4.6 5.7 4.7 3.0 5.2 6.4 4.5 4.8 4.0 3.3 6.0 5.5 6.1 5.8 5.9 4.6 5.1 4.2
##  [505] 3.6 3.8 3.1 6.3 4.4 6.1 3.5 3.4 4.4 5.8 4.6 5.4 3.9 5.1 2.7 4.3 4.3 5.6
##  [523] 3.8 4.0 5.7 4.2 4.5 3.9 5.3 3.4 4.6 3.5 4.1 4.6 6.2 6.5 4.8 5.8 4.0 5.0
##  [541] 3.2 4.1 3.6 5.9 3.7 4.3 5.5 5.0 2.1 4.8 4.4 4.5 5.6 3.7 5.3 5.0 4.0 6.1
##  [559] 3.3 3.0 4.4 5.7 2.4 4.4 3.4 4.3 4.1 4.6 5.0 3.0 2.5 4.6 5.3 3.5 4.9 3.9
##  [577] 4.3 5.3 6.2 5.2 4.4 3.3 6.7 5.7 5.9 5.8 4.5 5.2 4.1 5.1 3.6 5.5 3.3 6.0
##  [595] 5.5 4.0 3.6 4.8 4.5 4.9 6.3 4.7 3.0 4.8 4.4 4.3 6.5 4.8 5.1 5.0 4.9 3.0
##  [613] 5.5 6.0 3.1 4.8 4.4 3.9 5.5 4.7 4.4 4.2 4.0 4.2 4.1 4.4 4.0 6.0 4.8 4.6
##  [631] 6.0 5.7 4.5 5.4 4.1 3.9 2.9 2.9 4.8 4.1 5.0 4.2 3.1 3.9 6.2 3.8 4.7 5.8
##  [649] 3.8 3.0 5.0 4.3 4.4 3.8 4.2 6.3 4.8 6.6 5.2 5.2 4.1 4.9 5.4 5.2 2.8 1.9
##  [667] 3.2 4.3 3.9 3.9 3.2 4.5 5.4 3.1 2.4 4.7 5.6 3.9 3.8 4.2 5.3 5.4 5.2 3.4
##  [685] 4.9 4.1 4.8 6.3 4.9 4.3 5.6 4.0 5.5 4.8 4.1 4.4 4.4 4.6 2.7 3.5 4.5 4.7
##  [703] 3.5 5.0 5.8 5.1 3.6 4.4 4.0 3.8 4.1 3.1 4.0 4.1 3.1 5.5 3.8 3.8 5.5 4.1
##  [721] 4.9 6.0 5.8 4.6 3.7 5.2 4.4 4.6 4.8 4.9 3.1 5.9 5.2 4.6 4.5 5.2 3.5 6.0
##  [739] 4.6 4.3 3.4 4.6 3.8 4.8 3.8 5.7 3.8 3.9 3.9 3.7 5.5 5.6 5.9 3.8 4.1 4.9
##  [757] 4.9 5.6 5.9 5.1 4.9 4.0 3.6 5.6 6.2 4.8 4.3 3.3 4.2 3.8 4.8 5.0 4.6 4.6
##  [775] 3.1 5.2 5.8 4.5 4.7 6.1 5.5 4.2 5.6 3.1 4.9 4.5 5.1 3.3 4.4 6.1 5.1 2.9
##  [793] 3.5 3.6 3.7 4.3 4.9 3.8 4.9 3.1 5.6 4.6 6.2 5.9 6.1 5.0 5.0 3.7 4.5 4.8
##  [811] 3.5 4.8 3.6 4.4 3.4 3.6 2.9 4.2 5.3 2.8 4.8 3.2 6.2 6.2 4.3 5.3 3.4 5.6
##  [829] 5.6 5.2 3.7 4.5 4.4 5.3 4.8 4.6 5.2 4.9 4.4 5.2 2.5 5.3 6.1 5.8 5.9 4.2
##  [847] 4.5 4.6 3.3 4.8 5.4 3.9 5.2 3.8 2.7 4.1 4.5 4.3 5.0 5.6 4.4 5.3 3.6 2.9
##  [865] 3.3 5.3 4.4 2.9 5.7 5.6 3.0 4.1 3.4 5.7 5.6 3.0 6.7 2.7 4.8 5.3 4.5 5.2
##  [883] 3.8 4.4 3.9 4.5 1.6 6.4 4.8 5.1 3.6 5.3 4.5 3.6 5.5 6.4 5.8 5.4 4.6 5.6
##  [901] 4.7 5.4 4.5 3.7 3.7 4.5 4.9 4.4 4.9 4.9 3.9 5.4 5.8 4.2 5.2 4.6 2.7 3.2
##  [919] 4.8 5.1 3.6 3.7 5.3 5.3 4.2 4.9 4.6 4.4 5.5 4.2 5.8 3.0 4.2 3.8 3.7 5.1
##  [937] 3.9 5.1 3.1 5.7 5.1 3.9 4.8 4.0 4.3 4.6 4.0 3.8 4.4 3.1 4.0 3.8 4.8 5.7
##  [955] 5.1 4.3 4.5 5.5 3.0 6.2 5.7 4.3 4.3 5.0 5.4 4.6 4.5 4.1 3.9 3.4 3.7 4.6
##  [973] 5.0 5.8 4.7 4.4 3.1 2.6 4.5 5.2 5.9 4.8 4.3 3.5 5.2 4.7 4.6 5.2 3.5 4.0
##  [991] 5.2 4.5 3.0 6.2 4.4 4.7 3.3 4.2 2.7 3.7
## 
## $func.thetastar
## [1] -0.0024
## 
## $jack.boot.val
##  [1]  0.49055556  0.36974790  0.28400000  0.18909091  0.03448276 -0.09226190
##  [7] -0.20575758 -0.30317003 -0.34252199 -0.49308357
## 
## $jack.boot.se
## [1] 0.9475902
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
##    [1] 5.1 3.3 4.2 3.9 3.4 4.9 3.7 5.4 4.0 4.5 4.7 3.8 4.3 4.3 2.9 4.1 3.6 3.8
##   [19] 4.0 5.1 4.6 6.5 4.0 3.4 3.3 3.8 6.7 4.8 3.5 4.6 5.3 4.9 5.5 5.1 6.1 3.1
##   [37] 5.8 4.2 3.6 3.4 4.7 4.2 4.8 3.8 3.1 4.2 3.3 4.4 4.1 4.2 4.3 5.6 2.6 3.0
##   [55] 3.5 4.8 5.2 5.1 3.5 5.0 4.6 4.7 4.2 4.3 3.5 5.1 6.3 4.3 4.0 5.9 4.9 5.4
##   [73] 2.9 5.1 4.5 4.8 5.6 4.7 3.8 5.6 5.4 4.1 4.4 4.4 5.0 6.1 5.1 3.9 2.9 5.4
##   [91] 2.8 3.3 5.1 4.0 4.2 6.0 5.4 3.8 4.0 5.5 5.1 3.1 5.0 4.5 3.1 6.6 3.3 5.5
##  [109] 4.7 3.0 4.8 3.1 4.9 5.6 3.3 5.0 4.3 4.1 5.2 4.7 5.1 5.3 6.0 5.5 4.5 5.1
##  [127] 3.7 2.9 5.8 5.4 5.2 7.1 5.5 4.7 4.9 3.9 4.8 3.0 4.3 5.6 3.1 5.0 4.9 4.6
##  [145] 5.8 2.8 5.5 4.3 4.1 4.9 3.4 3.1 4.0 4.3 3.3 4.7 4.9 3.5 4.3 4.6 4.7 3.5
##  [163] 5.1 5.5 4.7 5.6 4.6 5.6 4.1 4.7 4.4 5.1 4.5 4.6 4.3 4.6 4.8 4.5 5.7 4.0
##  [181] 5.6 5.0 3.1 3.5 5.5 6.2 2.2 5.8 4.3 3.8 4.7 3.8 4.7 4.4 4.5 4.8 4.2 4.3
##  [199] 5.9 4.8 4.8 6.3 5.3 3.8 3.7 2.7 3.4 6.3 4.6 5.3 5.3 4.5 5.2 5.1 4.4 4.0
##  [217] 3.3 4.6 3.5 4.0 4.8 3.6 3.1 3.5 2.8 5.9 3.2 2.9 5.7 3.5 4.4 4.6 6.1 3.3
##  [235] 2.9 5.9 5.6 4.6 3.6 3.1 5.1 4.8 4.7 3.5 4.6 4.7 4.4 3.7 4.0 4.6 6.1 5.5
##  [253] 5.6 2.6 4.1 5.6 5.5 3.8 4.8 5.0 4.9 6.0 3.7 5.1 5.1 3.7 4.3 4.3 2.9 4.3
##  [271] 5.0 4.4 3.2 3.9 4.8 3.6 3.8 3.9 4.4 3.7 3.6 4.1 3.9 3.8 5.5 4.5 5.2 4.6
##  [289] 3.2 3.9 5.5 4.0 3.4 4.8 4.2 5.8 5.1 4.0 5.9 4.8 5.5 5.2 5.4 5.7 6.0 4.0
##  [307] 4.2 4.5 4.8 3.7 4.2 2.8 3.7 2.9 4.5 3.9 3.8 3.8 3.8 4.1 4.8 4.1 4.8 3.3
##  [325] 3.6 4.1 5.2 5.3 3.9 3.6 3.3 6.3 4.3 3.1 5.8 5.4 3.8 5.5 3.7 4.4 4.3 3.9
##  [343] 6.1 3.8 4.6 4.5 5.0 4.2 3.4 4.7 4.5 3.3 4.0 4.9 6.2 4.2 3.6 4.7 4.8 4.5
##  [361] 4.5 4.0 5.0 4.2 6.0 5.9 5.4 4.0 3.7 4.5 4.3 4.7 4.7 4.2 5.7 3.6 4.4 2.8
##  [379] 5.0 4.8 3.5 4.6 4.1 4.3 4.8 5.0 3.5 3.7 3.1 5.0 4.3 5.2 4.5 4.3 4.7 4.9
##  [397] 5.1 3.4 4.1 3.7 4.4 4.7 5.9 4.2 4.0 4.4 4.1 5.5 5.7 6.2 2.8 4.1 4.1 4.1
##  [415] 6.3 5.2 4.8 4.5 6.0 4.1 4.2 5.7 4.3 5.1 5.8 4.8 4.5 4.0 4.0 3.0 5.7 4.0
##  [433] 5.1 3.1 4.1 4.4 4.8 3.6 5.4 3.5 3.6 4.7 5.4 4.5 4.8 4.1 5.3 4.1 4.5 4.0
##  [451] 5.4 6.2 3.6 5.0 4.9 4.6 5.2 4.1 4.8 4.6 5.8 6.1 4.8 3.8 4.9 5.1 4.1 3.6
##  [469] 3.3 5.3 5.2 3.9 5.9 6.4 3.6 3.3 4.8 5.2 3.3 2.7 4.7 3.8 4.7 3.6 5.5 5.6
##  [487] 6.9 3.2 2.7 3.9 5.4 4.6 4.5 5.1 4.1 4.3 4.4 2.5 4.4 3.2 2.9 5.7 5.7 4.4
##  [505] 4.3 5.1 3.8 5.1 3.9 4.1 3.7 5.4 4.6 4.5 4.0 3.9 4.9 5.7 5.3 3.1 4.3 3.7
##  [523] 4.5 3.8 3.9 5.1 5.1 4.6 4.5 4.1 5.1 3.9 3.5 5.3 5.2 5.3 3.4 5.2 4.4 2.8
##  [541] 6.6 3.2 5.3 4.4 3.3 5.2 3.6 3.3 4.0 4.1 5.3 4.9 5.2 5.1 5.5 5.6 5.1 6.8
##  [559] 4.1 6.3 3.5 4.1 4.9 3.6 5.0 4.0 3.3 6.1 6.8 4.7 3.5 5.0 4.3 4.3 4.2 2.2
##  [577] 4.9 4.5 4.4 4.9 5.1 4.9 5.5 5.2 5.1 4.7 4.2 3.7 4.3 5.5 4.4 4.2 6.0 5.0
##  [595] 4.6 4.4 3.3 5.3 5.7 5.1 5.0 3.3 5.5 3.6 3.1 4.2 5.1 4.1 5.9 4.7 4.5 4.4
##  [613] 4.7 3.7 6.1 4.9 4.0 5.3 3.4 3.4 5.9 3.7 5.4 4.8 4.7 5.7 4.4 3.5 3.2 6.3
##  [631] 4.9 5.7 3.6 4.3 2.2 4.7 4.5 5.0 5.0 7.3 3.8 4.7 4.4 3.5 5.2 4.6 5.2 3.2
##  [649] 5.2 5.1 4.9 5.6 2.8 3.8 4.1 3.4 5.0 3.3 4.7 4.9 4.3 3.7 5.8 3.8 4.7 4.2
##  [667] 4.0 4.9 6.2 3.7 4.2 6.0 4.9 3.4 3.8 4.3 5.3 4.4 5.8 4.0 4.2 4.1 4.5 4.3
##  [685] 4.8 6.3 4.3 5.3 4.8 4.7 3.5 4.3 4.5 3.9 5.5 3.6 5.5 4.9 5.5 4.6 4.5 3.0
##  [703] 3.1 4.7 3.3 4.0 3.8 4.0 5.0 2.8 5.8 4.1 5.2 4.3 4.6 2.3 4.9 4.2 4.0 5.1
##  [721] 4.0 3.6 3.8 5.2 4.8 4.5 4.0 3.5 4.8 2.6 4.8 4.1 5.1 3.0 3.3 4.0 4.3 4.5
##  [739] 5.5 5.3 6.0 4.0 4.8 2.9 5.3 5.0 1.5 3.6 4.9 3.5 3.2 4.7 3.4 2.5 3.5 5.4
##  [757] 4.3 6.0 3.7 4.7 5.2 4.3 4.7 3.4 3.6 5.2 4.7 3.3 4.2 4.8 4.2 4.7 4.5 4.9
##  [775] 3.5 3.5 5.2 4.1 4.4 5.9 4.9 3.9 5.2 4.5 5.6 3.9 3.7 4.6 4.2 4.6 5.5 4.5
##  [793] 3.5 4.8 4.9 4.3 5.1 4.3 3.4 5.3 4.5 4.9 5.0 2.8 4.1 5.6 4.9 3.6 2.5 4.1
##  [811] 3.4 5.5 4.3 5.1 3.9 5.4 3.4 4.2 3.9 3.1 2.6 5.4 4.0 3.9 4.6 4.5 5.5 4.5
##  [829] 5.6 5.8 3.7 5.3 3.6 3.2 4.1 4.3 4.4 4.7 5.3 4.1 3.2 4.8 4.6 4.2 4.8 6.3
##  [847] 4.5 5.5 4.1 5.1 4.8 4.1 5.0 4.4 4.8 4.9 4.4 4.1 3.0 4.3 3.8 3.9 4.4 4.8
##  [865] 3.5 5.1 4.2 5.3 2.9 4.7 4.7 3.8 4.7 5.2 5.0 3.9 5.0 5.9 4.5 3.6 3.9 2.9
##  [883] 6.4 5.3 5.8 5.3 2.6 5.5 4.3 4.0 6.6 5.8 5.5 4.7 4.4 3.3 4.2 4.9 5.1 5.0
##  [901] 4.3 3.7 4.1 4.6 5.0 3.3 4.9 5.3 4.4 3.2 3.3 4.5 2.8 4.3 4.4 5.6 4.0 6.9
##  [919] 5.3 2.9 2.9 5.3 5.1 4.8 4.7 4.0 4.3 4.4 4.4 4.7 4.4 3.9 4.3 7.4 3.3 5.0
##  [937] 5.7 3.9 4.4 3.5 4.1 3.5 5.2 4.1 5.2 5.4 3.3 3.6 5.8 4.4 3.4 3.3 2.6 5.2
##  [955] 3.8 3.4 4.2 4.2 5.2 5.2 6.1 4.6 4.5 5.7 1.5 6.3 2.9 3.2 4.0 3.5 4.4 4.0
##  [973] 3.8 4.2 4.2 4.5 4.0 5.2 3.8 4.7 6.5 3.6 4.7 3.7 4.8 3.9 3.9 5.1 5.6 3.6
##  [991] 4.3 6.0 3.4 3.3 5.5 4.3 4.1 4.6 3.9 3.7
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.40 5.34 5.20 5.20 5.10 5.00 4.80 4.80 4.50 4.40
## 
## $jack.boot.se
## [1] 0.971574
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
## [1] 0.4086842
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
##   3.570569   5.865201 
##  (1.528086) (2.695202)
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
## [1] 0.7407695 0.3115012 0.7568561 1.0640500 0.2577278 1.2036790
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
##    [1]  0.0012519775  0.1505423825  0.0461147983 -0.2856525268  0.1828103094
##    [6] -0.6041817901  0.8993492190  0.3314898523  0.6591587848 -0.1229812175
##   [11] -0.1404197649  0.6851456489  0.1126238107  1.0832108681  0.6509232083
##   [16]  0.4035227729  0.7342855607  0.6885833383  0.5410993292  0.6849872885
##   [21]  0.1965004353 -0.6082313961  0.2799743997  0.4082584021 -0.7398024988
##   [26]  0.3680939817  0.8283303795  0.6442449650  0.2309179905  0.4876910207
##   [31]  0.3004173692  1.0890804080  0.3929580673 -0.2436266030  0.4608298015
##   [36] -0.1420900944  0.3971955989 -0.3625894460 -0.4737947133  0.2620599223
##   [41]  1.0022538861  1.2372694860 -0.2198904367  0.2888500263  0.0501686986
##   [46] -0.4635670270  0.5573851645 -0.5382675902  0.9187314897 -0.1937612928
##   [51]  0.3698676841  0.1277114243 -1.0883322841 -0.4875285828  0.0784252389
##   [56]  0.0286425037  0.2255154195  0.2152724910  0.4942972771  0.0545347385
##   [61]  0.5732311141  0.1622709672  0.8959483317  1.2474791793  0.2487273621
##   [66] -0.4113703864  0.8942461661  1.1897091221  0.0213592042  0.3360954476
##   [71] -0.3429219346  0.2242483097 -0.9748781655  0.8030539009 -0.0601305304
##   [76]  0.6372219572  0.8252507947  0.8428368390  0.1676897716  0.3114548806
##   [81]  0.1269884903  0.7633047723  0.5469935653  0.8016381547  0.1558878171
##   [86]  0.1982336114  0.3842855063  0.3056654412  0.3417653849  0.2046312895
##   [91]  0.3264407147  0.3294796367  0.0476328539  0.3810636738  1.1104089030
##   [96]  0.9640748592  0.6999122252  0.3745153167 -0.4635670270  0.3512022782
##  [101] -0.1025667754  0.1836187630 -0.2684762966  0.4428497209  0.1995935715
##  [106]  0.6638920137  0.5837935424  0.0616060943  0.0734506040  0.0111991077
##  [111] -1.4095747889 -0.1604661291  0.5501169615  0.4359162328 -0.5647463533
##  [116]  0.5411421873 -0.9307320761  0.4695858977  0.8746621571  0.4290953009
##  [121]  0.2961685263 -0.7834704284  0.4791369042  0.4511350218  0.4427300544
##  [126]  0.7153589487 -0.1195309689  0.8481508249 -0.0981892538 -0.5198563719
##  [131] -0.0895225877 -0.4553349480  0.5107101143  0.5516241038  0.2451111234
##  [136]  0.3431178885 -0.0239595506  0.5529040488  0.1145615371  0.3889966938
##  [141]  0.5391689297 -0.3504381089 -0.4971492300  0.2526196799  0.0688162124
##  [146] -0.3250845014  0.7294631762  1.5278167678 -0.1017787704  0.3038224575
##  [151]  0.2096795237  0.2506882280  0.1571920274  0.4252764720  0.0585950653
##  [156]  0.7339137894 -0.2313899299  0.8826361646  0.0553940386  0.1918489690
##  [161]  0.0814536746  0.7239249787 -0.1923318194  0.6044280181  0.3050694456
##  [166]  0.5181878645  0.3350463602  0.1846722121  0.3408923790  0.8610462627
##  [171] -0.0559459603  0.1579966723  0.2764049508  0.7354197994  0.0933770175
##  [176] -0.4459099867 -0.0364063991 -0.5211679660  0.3315523662  0.6112306432
##  [181]  0.0717216968  1.2579432427 -0.0191373018  0.7258457134 -0.2126156112
##  [186]  0.0952503953  0.5943467056  1.0678199111  1.2315025670  0.5937161788
##  [191] -0.5615444543 -0.2248949249  0.0807430031  0.4430883426  0.4207252999
##  [196] -0.2949653834  0.7976266493  0.0337025924  0.9260452095 -0.3051744802
##  [201]  0.4273394993 -0.1212167230 -0.1113956058  0.2751233634 -0.2351430118
##  [206] -0.0182563007 -0.1809948411  0.1927214317  0.1444561685  0.7854276236
##  [211]  0.4744679440  0.3609747153 -0.0664880583  0.4598839863  0.2625013597
##  [216]  0.6309475363  0.6999122252  0.4134993058  0.1428886157  0.1717530131
##  [221]  0.7664354903  0.3095268731  0.1613732371 -0.4003729968  0.0369416572
##  [226] -0.6057410601 -0.7807238201 -0.2746951174 -0.0616804896  0.1978054550
##  [231] -0.0782477223  0.6894890964  0.6106719728  1.0184325784  0.2764049508
##  [236]  0.8764591690  0.4103721970  0.1649388512  0.2561271246 -0.3775807472
##  [241]  0.7893104738 -0.0202241587  0.2160880395  0.2741036504  0.2467041155
##  [246]  0.2215043721 -0.0417844005  0.2885178975  0.1264204703  0.8259930827
##  [251]  0.6469181793  0.3169321832  0.1424446824  0.7287233472  0.4916800788
##  [256]  0.6411199954  0.7297607996  0.3299098813  0.0439888018  0.0226762621
##  [261] -0.3381817073  0.5880324469  0.6296101720 -0.1956660277  0.0969273618
##  [266]  0.8291171663 -0.1482072326 -0.0499192649 -0.1158486676 -0.0010431613
##  [271]  0.0002178694  0.2244777613  0.6981873803  0.2707405259 -0.5316481950
##  [276] -0.4131854352  0.2210873947 -0.1127609298  0.1781290435  0.5480973497
##  [281]  0.0462944477  0.5094976456  0.4727025930  1.7321357692  0.6457912125
##  [286]  0.3497158467  0.1433498671  0.5011246291  0.1731555858  0.0685788396
##  [291] -0.3451487549 -0.5222133801  0.5201233373 -0.0267767330  0.9577319977
##  [296]  0.2689492928  0.1315936953  0.7991238597  0.1516712775  0.2373816960
##  [301] -0.0121230035  0.2777769565  0.2198877871  0.2324093557  0.2890336860
##  [306]  0.7201046309  0.4036919611  0.7518606236  0.4442966928 -0.2991155174
##  [311]  0.8116561744  0.4464336600  0.5389673299  0.1047140949 -0.1791472083
##  [316]  0.2140766524  0.1355982421  0.6106719728 -0.2495646441  1.0643980422
##  [321]  0.7736874460  0.5618029099  0.2985775001  0.3613121706  0.7671088388
##  [326]  1.2372694860  0.0026537482 -0.4703876454  0.4916484020  0.0103589821
##  [331] -0.1195309689  0.2061783890  0.2644236623  0.8392378444  0.8398815829
##  [336]  0.9075702995  0.5772477745  0.4275535835  0.1145615371 -0.2057446775
##  [341] -0.4153107845 -0.6970039211  0.4708356765  0.0734170957  0.2143956854
##  [346]  0.2583488489  0.7312617405  0.3661988441  0.4002570293  0.6125163846
##  [351]  0.7724175276 -0.2003043573 -0.1323754903  0.1334094231  0.6252048778
##  [356]  0.1159416183 -0.2815570937 -0.1329596168 -0.2288992930  0.1601270653
##  [361]  0.4796513135  0.4355024746  0.5458929087  0.4045481987 -0.1275013560
##  [366]  0.1288704989  0.7838189351  0.9599038682  1.1682709040  0.4034030609
##  [371] -0.1024815188  0.0613300654  0.6096496859  0.1420997493  0.1648318784
##  [376]  1.0247472787 -0.3774574499 -0.4230323447  0.8520620101 -0.4646398861
##  [381] -0.3848802846  0.7356126260 -0.0196567160  0.3647700847  0.6117106818
##  [386] -0.4884680599  0.1793106603  0.3247189136  0.1765372177  1.2428246297
##  [391]  1.9232499595 -0.3555542732  0.3971338670  0.2847448000  0.6245549097
##  [396]  0.3703692683  0.3341060318  0.4934942623 -0.0553052364  0.7346256704
##  [401]  0.6423396132  0.6116373005  0.5951222588  0.4342341772 -0.5447771465
##  [406]  0.0313723487  1.5425892545  0.3107160525 -0.1195500184  0.2968969851
##  [411]  1.4990605447 -0.5478513168 -0.1102442130  0.5411421873  0.1272964881
##  [416]  0.7077660606  0.7439651482  0.0104191510  0.3947032862  0.1033496203
##  [421] -1.1086226119  0.8586429635  0.3188222973 -0.1578798944  0.4338068080
##  [426]  0.7869122071  0.3721666764 -0.0745980177  0.1250519582  0.6498363692
##  [431]  0.1761889057 -0.2098820921 -0.3848328348  0.3401282830  0.5113457610
##  [436] -0.5723709396  0.7507190686  0.2910249993 -0.1239580311 -0.0810209870
##  [441]  0.6622385478 -0.1151379764  0.5919423988  0.1279647719  0.2526920527
##  [446]  1.0211667907  0.2153247534  0.2356004867  0.2201531499 -0.4003729968
##  [451]  0.7736874460  0.4128461124  0.7102321837  1.5875307729  0.3729775757
##  [456]  0.4989464938  0.1670016085  0.3772233427  0.1756232284 -0.0491408064
##  [461]  0.7685893545  0.1670843944  0.0547351657  0.6392622575  0.3370487267
##  [466]  0.5862966191  1.0243275592  0.2071684996  0.4464547331  0.2734448663
##  [471]  0.2758821668  1.1480631374  0.6285551117 -0.2380797076 -0.8308535668
##  [476]  0.4772896950  0.0285271931  0.4080098597  0.3279566409  0.5145081284
##  [481]  0.3513367881  0.2124291736  1.1897091221  0.4107915427  0.4890513899
##  [486]  0.1006479172  1.1015527496  0.7449621528 -0.0127039840 -0.2772434865
##  [491]  0.8114734077 -0.4139422645  0.3630011597  0.4830061133  1.2559509560
##  [496]  0.7214114871  0.2402366154  0.4792492769 -0.1611244571  0.4546015074
##  [501]  1.3147432487  0.4494164827  0.4730857279 -0.7109401667  0.1246904474
##  [506]  0.0601550828  0.9995906694  0.4780410314  1.3874279791  1.0486141362
##  [511] -0.1559505559  0.5906532594  0.1962743802  0.8924548690  0.4268537947
##  [516]  0.8489940236 -0.7870183283  0.5709822732  0.6764056295  0.7926429981
##  [521]  0.2749846412  0.0850151804  0.3745153167  0.4922943846  0.0498998362
##  [526]  0.6354090230  0.2478346626  0.3247869488  0.1043382397  0.7986958044
##  [531] -0.0863530097  0.0713316638  0.8399944303  0.5075587268 -0.4631329496
##  [536] -0.3839622140  0.2212837470  0.7928617136  0.2509833439  0.3175034327
##  [541] -0.2586912090  0.1807994830 -0.1068350978  0.0249853126  0.9734107572
##  [546]  0.3027587713  0.1666791240  0.6846751271  0.2760557764  0.5765649525
##  [551]  0.4550550537 -0.1330123750 -0.2252710290  0.4168254416  0.2600970312
##  [556] -0.0354823298 -0.0731025497 -0.5563603894  0.3632862647  0.5325949993
##  [561]  0.6963339733  0.3976237419 -0.1458177116  0.3274667038  0.9211682067
##  [566] -0.4188992320  0.4824759526  0.2201611815  0.8138989598  0.3167140836
##  [571]  0.5320623803  0.2496103331  0.0313723487 -0.1036899102  0.5391840300
##  [576]  0.0555351766  0.1182337314  0.5961415317  0.0849798468 -0.2681116209
##  [581]  0.1477804480  0.6806467087  0.8796062752 -0.3983977028  0.0741139141
##  [586]  0.6974757146  0.2391014771  0.4900081435  0.6915480799  0.8245475824
##  [591] -2.2770646526  0.1792962182  0.7030897528  0.6481262297  0.6832078028
##  [596]  0.4210148178  0.1531937816  0.7532149727  0.6960702602  0.2010128712
##  [601] -0.1246917114 -0.4282886191  1.2771710934  0.2242791247  0.4275319002
##  [606]  0.6713526493  0.4290524258  0.9294034884  0.7819000865 -0.2806673809
##  [611] -1.1211292691  0.7963771009 -0.0126927219  0.2740673872  0.2125347547
##  [616]  0.1347866140  0.8865825572 -0.4827128333  0.3042310731  0.7349877195
##  [621] -1.1873504468 -0.1271665622  0.9471341782  0.7862708196  0.3823473836
##  [626] -0.3082380878 -0.3853074184  0.0063106306  0.0847960324 -0.0046893478
##  [631] -1.0914999292  0.4799368114  0.2197438222 -0.1743712381  0.4219840726
##  [636]  0.5623384383  0.3525595986  1.0162623570 -0.3548010795  1.0626767543
##  [641]  0.7789874876  0.2569677674 -0.6302320259 -0.3296095821  0.2216206658
##  [646]  0.6955979937 -0.1952238176  0.4084190252 -0.0535328921  1.1868286312
##  [651]  1.4290419815  0.2478014903 -0.0457264244 -0.1251681070  0.5513234413
##  [656]  0.5197226814  0.6394279959  0.3766478497  0.6651745809  0.0883485511
##  [661]  0.1652332186  0.6146805221  1.2251865137 -0.2564867725  0.8929282234
##  [666]  0.4214956118  0.7631584831  0.0623230848  0.0854669955  0.2798034951
##  [671]  0.8929312896  0.4622258209  0.2769482109  0.7384226372 -0.3362343650
##  [676] -0.1623360141  0.7279477491  0.0475230409  1.0641762938  0.2551689132
##  [681]  0.3432446047  1.4216799182  0.0855948803  0.1433710169  0.3314248648
##  [686] -0.0330187610  0.3171751098 -0.4665334588  0.2997239785  1.9316828097
##  [691]  0.3060299227  0.1860816788 -0.0927054822  0.1295417652  0.4069844310
##  [696]  0.3325738163  1.7515317722 -0.9000515503  0.7174260937  0.4276033921
##  [701]  0.2133888348  0.5020447466  0.9273059687  0.5937826977  0.9612179841
##  [706]  0.0157907471  0.8945751666  0.3450132969  0.8184771961 -0.1560139691
##  [711] -0.1457462717  1.3195464058 -0.5972657439  0.1073018158 -0.7310785210
##  [716]  0.3480577408  0.2623869260  0.3246686237  0.0051918158 -0.2655227906
##  [721]  0.4080598766  0.0207952658 -0.0330454666  0.2106782147  0.0647090695
##  [726]  0.1145918300  0.3472840552  0.9728854558  0.3524342828  0.0390821094
##  [731]  0.3940558047  0.5380348537  0.5202776228  0.3008898834  1.2134821884
##  [736]  0.2522047015 -0.3703051858  0.0130188617  0.3077981499  0.4207252999
##  [741]  0.4422158697 -0.0983738143  0.4208886442  0.1840476376  0.7194717709
##  [746]  0.8348048847 -0.2053780198  0.7712157567  0.2157697773  0.4381527236
##  [751] -0.0563837599  0.2612676237 -0.2248761414  0.9241340410  0.5309479593
##  [756] -0.2456749613  0.7815385278 -0.3434139575 -0.0982860313  0.1581208536
##  [761]  0.5928787364  0.2014070518  1.0811993411  0.2368061610 -0.0188304746
##  [766]  0.5600955861 -1.0082766641  0.2301529442  0.7045858893  0.8371821849
##  [771] -0.6376628110 -0.0529902352  0.7133236627  0.6107383942  0.5067192618
##  [776]  0.5040932834  0.4312739977  0.1678997680  0.0826751180 -0.1579424945
##  [781]  0.3952196066  0.5015125358 -0.4446809401  0.6336414226  0.3840336807
##  [786]  0.8103526390  0.7081927224  0.7300838504 -0.0936202378  0.4538915363
##  [791]  0.1305922160  0.0591454305  0.6908232263 -0.9713473737  0.3265907774
##  [796]  0.3965373244  1.1299607397  0.9724502000  0.2533182750  1.0275772439
##  [801]  1.1051866157 -0.2633313508 -0.2744185512 -0.7151992733  1.2076879821
##  [806]  1.1233243737  0.9467097523  0.5686193040  0.4300441129  0.0597360388
##  [811]  0.2837735078  0.4987126871  0.1951291091  1.0040117473 -0.2387200008
##  [816]  0.3940202273  0.3050694456  0.7533185970  0.1539745193  0.8306037317
##  [821]  0.1715255221  0.8820074663  1.7041427960  0.7515174805  0.9880630184
##  [826] -0.2949653834  0.7007979565  0.1502255234  0.1971661152 -0.1482589590
##  [831]  0.2170550055  0.0899255486  0.1596791269  0.9963548655  0.5035613288
##  [836] -0.3707652721  0.1810321929  0.4391187406  0.8118044925  0.6836006177
##  [841]  0.6738585279  0.3433546618 -0.2822373451  0.4324016699  0.1892449792
##  [846] -0.2009542052  0.7642589294  0.6221536169  0.7313048838  0.3469626593
##  [851]  0.1732211884  0.4488505841  0.3096917372  0.3319369613  0.3926238135
##  [856]  0.7400530147  0.3077966659 -0.1165188417 -0.0858749490  0.6915480799
##  [861]  0.4791916420 -0.0850781990 -0.2324362864  0.8151246348  0.5263797259
##  [866]  0.5552577552  0.2829385148  0.3914501227 -0.4315768576  0.4805944422
##  [871]  0.2043080590  0.6274367873 -0.1190429121  0.6710527507  0.6321664400
##  [876]  0.1628305149 -0.3551038111  0.4131361583  0.1665747713  0.6541961014
##  [881]  0.4214063419  0.5872087750 -0.1560484046  0.0548221044  0.6711685003
##  [886]  0.7313298483 -0.3521931724 -0.8881271776  0.3505702689  0.5504544689
##  [891]  0.2599732013 -0.0735088657 -0.0982380990 -0.8881271776  0.2050612902
##  [896]  0.8382929081  0.6600545975 -0.6555913665  0.2092935893  0.8752044906
##  [901]  0.3313064631  0.7339419992  0.0085104368 -0.1682595117  0.4193232753
##  [906]  0.3838976665  0.6181114029  0.9115165593  0.5384821729 -0.4452009337
##  [911]  0.6618377196  0.1094330154  0.1856488404  0.6309082881 -0.0780652099
##  [916]  0.2114675005  0.2741051754  1.2715455135  0.7692219661  1.4166679077
##  [921]  0.3960770640  0.3596355059  0.1002120023 -0.2236480790 -0.2803121205
##  [926] -0.0719407776  0.2051533570  0.2545831038  0.4933828736 -0.0899683211
##  [931]  0.4033288395  0.9445197663  0.9049328624  0.1856876118  0.2724140164
##  [936]  0.4830061133  0.5714377859  0.0705203291 -0.1045266861  0.6916134369
##  [941]  0.0585673906  0.2214370353  0.6434009262  0.1638822516  0.0808997083
##  [946]  0.7262960371  0.6700003982  0.4190171162 -0.6134656709  0.7245294711
##  [951] -0.5967464523 -0.1807452575  0.4517108686 -0.1784739736  0.1583108092
##  [956] -0.1681979938 -0.0169748965  0.2909564843  0.1715014557 -0.0546969706
##  [961] -0.4661482563  0.6501348337  0.0727992604  0.2717500470 -0.8578089976
##  [966] -0.8194638578  1.6098908344  0.5666674759  0.7905627949 -0.8029012462
##  [971]  0.2993955656  0.7445901807  0.7671088388  0.7452127595  0.5711995195
##  [976]  0.6466646494  0.6711418474  0.0582087828  0.1360632780  0.6326586835
##  [981]  0.6072226607  0.7649659667 -0.2920597215  0.3194944594  0.2610766964
##  [986]  0.0997997684  0.7564788679 -0.4310633728  0.9987115408  0.6779805777
##  [991]  0.1755210233  0.3334907650  0.4841056872  0.5038314340  0.2686069552
##  [996]  0.7542719856  0.8398265416 -0.2358777321 -0.3091754354  0.4241953027
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
##   0.60877354   0.30622743 
##  (0.09683762) (0.06847127)
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
## [1] -0.95403552 -0.22817040 -0.06088793 -0.04333043  0.16765584 -0.10371355
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
## [1] -0.0059
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.894514
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
##     original       bias    std. error
## t1*      4.5 -0.008808809   0.8871621
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 4 6 7 9 
## 1 2 2 1 3 1
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
## [1] -0.0467
```

```r
se.boot
```

```
## [1] 0.9431854
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

