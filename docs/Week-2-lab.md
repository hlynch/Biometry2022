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
## 0 1 2 4 6 7 8 
## 1 2 1 2 1 1 2
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
## [1] 0.0352
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
## [1] 2.731672
```

```r
UL.boot
```

```
## [1] 6.338728
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
##    [1] 4.7 4.3 4.9 5.4 5.2 5.3 4.7 3.6 4.5 4.1 3.8 4.8 4.9 2.8 4.0 4.1 4.3 2.9
##   [19] 4.1 5.5 5.5 3.6 5.1 4.8 3.6 4.1 4.0 4.0 3.6 5.0 5.2 5.4 5.0 2.7 4.4 4.1
##   [37] 3.7 4.5 4.7 5.1 5.3 3.3 3.7 4.5 4.2 5.4 5.1 5.0 4.9 4.6 5.3 5.5 3.7 5.6
##   [55] 4.1 4.5 4.7 3.9 4.1 4.2 5.2 4.5 5.2 4.8 5.2 4.7 4.7 3.7 5.5 5.2 4.3 4.9
##   [73] 2.8 4.1 3.3 4.2 3.7 3.4 4.7 5.1 5.5 3.4 5.1 4.3 3.2 4.0 3.4 3.8 3.4 4.3
##   [91] 4.0 5.7 3.9 4.6 4.9 4.1 3.0 4.5 3.8 5.6 3.8 4.5 4.1 4.1 3.8 5.0 4.0 5.1
##  [109] 5.1 4.6 4.3 4.4 5.6 4.8 4.0 3.8 3.7 3.7 6.5 4.7 6.0 4.5 4.6 3.8 3.7 5.5
##  [127] 4.5 5.7 3.1 3.6 5.0 5.5 3.7 4.2 4.3 5.2 5.1 3.9 5.0 6.1 4.3 3.9 4.8 5.4
##  [145] 4.8 4.3 4.5 5.7 5.3 4.8 4.4 5.6 3.9 3.0 4.7 5.1 5.6 2.2 5.1 5.5 5.4 6.0
##  [163] 5.2 4.2 5.3 3.7 4.7 3.7 4.8 4.3 5.2 4.5 5.8 3.9 6.8 5.2 3.7 3.0 4.3 3.1
##  [181] 3.4 4.3 4.2 6.0 4.8 2.6 4.6 4.8 5.8 6.1 4.3 4.5 4.1 5.0 5.5 4.2 4.5 5.7
##  [199] 5.6 3.4 4.1 4.2 5.1 4.0 2.7 4.7 4.6 3.9 4.6 3.6 3.9 5.8 5.3 4.7 3.6 5.7
##  [217] 3.7 3.5 3.9 6.5 4.4 3.7 4.6 4.8 4.1 5.5 4.0 4.0 4.2 4.8 5.8 4.3 5.0 4.3
##  [235] 5.0 4.2 4.0 2.5 4.3 3.9 4.1 5.0 3.6 2.7 4.1 5.0 4.6 5.2 3.6 5.5 4.2 3.8
##  [253] 5.3 5.7 3.9 4.8 4.9 5.6 4.6 4.4 4.4 3.2 4.5 4.7 3.7 6.8 5.3 4.2 4.5 3.9
##  [271] 4.9 4.5 5.7 3.6 4.5 3.9 5.2 4.6 4.9 5.9 5.0 3.8 2.5 5.1 5.2 4.8 6.9 4.3
##  [289] 4.0 5.3 5.4 4.1 3.9 3.5 3.9 3.6 4.1 5.9 4.8 3.6 4.7 4.4 5.8 6.0 5.6 3.1
##  [307] 2.9 4.6 2.7 5.7 4.4 4.2 3.9 6.7 4.6 2.9 3.8 4.6 4.5 5.3 5.1 2.4 5.4 5.1
##  [325] 4.0 5.4 5.4 4.3 4.2 4.7 3.6 4.3 4.7 5.1 4.6 4.0 4.4 4.1 3.8 4.0 5.2 6.5
##  [343] 4.0 4.7 5.3 5.8 3.8 4.4 4.9 3.8 4.1 4.3 5.3 4.7 4.4 3.8 5.8 4.9 3.1 4.3
##  [361] 4.4 6.9 4.8 4.0 3.5 4.9 4.8 4.7 5.2 3.9 3.4 4.5 3.9 4.4 4.5 4.5 3.7 2.6
##  [379] 4.2 3.2 3.5 6.3 4.7 6.4 3.5 4.8 6.0 3.3 4.0 5.4 5.1 3.9 4.4 2.3 4.2 5.2
##  [397] 4.4 5.2 5.8 5.0 7.1 4.9 4.6 5.2 3.6 5.0 6.1 4.0 4.7 5.7 4.7 4.7 4.8 4.9
##  [415] 3.9 3.5 6.0 3.6 5.5 4.7 3.2 3.6 4.9 4.1 5.8 3.3 3.2 3.9 4.6 5.1 6.9 4.6
##  [433] 4.0 4.6 5.1 4.8 4.0 4.6 4.1 3.7 4.4 5.7 5.0 4.0 3.3 4.1 3.1 5.2 3.8 3.7
##  [451] 4.9 5.5 5.3 4.1 4.1 3.9 4.7 4.8 4.5 4.8 5.1 4.9 6.8 5.0 5.1 3.6 3.2 3.9
##  [469] 4.0 4.2 5.2 4.9 3.8 4.5 4.7 4.5 4.7 5.3 3.7 4.5 4.3 3.9 3.3 6.0 2.5 5.7
##  [487] 5.1 4.4 5.0 4.7 5.0 4.2 4.6 4.6 2.9 2.3 3.3 5.1 5.0 3.1 3.5 5.0 6.2 4.4
##  [505] 5.6 5.4 4.2 3.5 4.7 3.4 5.3 5.5 5.0 5.6 4.5 5.5 3.5 4.6 6.1 4.4 6.1 5.8
##  [523] 4.0 3.9 3.7 5.1 3.2 6.7 4.9 5.2 4.9 4.3 4.7 4.1 2.6 4.7 3.2 4.3 4.6 4.9
##  [541] 3.6 4.7 5.1 4.0 4.7 4.6 5.4 5.2 3.8 4.6 4.1 4.4 5.6 4.2 5.2 5.2 4.9 4.1
##  [559] 4.5 4.1 5.1 4.5 4.4 3.4 3.8 2.7 6.3 5.1 5.5 5.5 4.5 3.4 3.9 4.0 5.4 3.7
##  [577] 4.8 4.7 6.1 3.6 3.6 3.7 6.1 3.9 5.8 5.4 6.0 4.8 4.9 4.5 4.0 4.1 4.6 5.5
##  [595] 3.8 4.9 3.6 3.7 3.7 4.5 4.6 4.2 5.3 4.2 5.7 5.2 4.9 2.9 5.2 4.7 4.0 3.0
##  [613] 5.2 6.8 4.2 4.2 3.4 3.7 4.5 2.7 4.7 4.5 4.1 6.8 4.6 4.4 3.6 5.1 4.2 3.4
##  [631] 5.0 4.3 3.8 2.9 2.8 4.7 4.4 3.2 2.9 5.2 5.5 5.0 4.8 4.1 3.6 4.9 3.1 3.9
##  [649] 4.4 3.3 4.6 3.6 4.6 5.2 4.9 4.5 4.2 5.6 5.1 6.3 3.4 5.4 5.1 4.6 5.0 6.1
##  [667] 3.2 2.8 3.4 4.1 5.0 4.1 4.9 4.0 5.4 4.2 4.8 4.2 5.4 5.7 5.0 4.4 4.2 4.5
##  [685] 4.8 3.6 5.1 6.0 4.3 5.3 4.2 4.1 5.0 5.6 6.0 4.8 4.8 6.9 4.6 4.5 4.0 3.6
##  [703] 4.4 5.3 4.0 5.6 4.1 3.4 5.5 3.6 5.1 4.6 4.6 3.9 4.4 4.6 4.5 3.8 3.9 4.3
##  [721] 4.8 5.2 5.1 4.2 6.4 6.0 5.4 3.3 3.2 4.5 3.2 5.4 3.5 5.7 5.1 4.3 4.9 5.2
##  [739] 4.1 5.7 3.3 5.2 4.3 3.5 3.8 3.9 5.7 3.2 4.8 5.3 4.0 4.4 4.3 4.9 3.4 5.4
##  [757] 4.1 4.8 4.3 3.8 4.5 2.8 5.3 4.6 5.1 3.5 3.2 4.5 3.1 3.9 4.2 4.9 3.7 4.5
##  [775] 4.6 3.7 2.7 4.0 3.0 4.9 4.0 3.3 4.8 3.6 3.9 4.6 2.7 5.1 4.7 5.5 3.3 5.4
##  [793] 4.6 5.1 6.0 5.8 3.7 4.3 4.3 3.2 5.8 3.7 2.9 5.7 3.4 6.0 5.0 2.5 5.6 3.8
##  [811] 5.1 5.3 4.8 5.7 5.0 3.7 4.8 6.2 4.8 3.7 4.9 5.2 4.7 5.1 2.7 4.5 2.9 5.6
##  [829] 4.4 4.9 4.1 3.9 3.7 4.4 5.9 3.9 4.2 3.6 4.9 5.1 6.1 3.9 4.9 5.7 5.3 4.7
##  [847] 4.9 4.1 5.4 4.6 3.9 5.2 5.0 3.3 2.6 5.9 4.7 5.4 4.9 4.1 4.9 5.0 4.7 4.7
##  [865] 3.4 4.0 4.2 4.4 3.6 3.3 4.6 3.9 3.3 5.2 4.9 3.2 3.3 5.7 4.3 4.8 5.2 5.7
##  [883] 5.5 5.2 4.6 4.5 4.5 5.7 4.9 5.0 4.4 3.7 4.8 5.4 3.4 3.7 3.7 4.0 4.3 2.7
##  [901] 4.0 5.2 4.9 3.9 3.8 6.4 5.5 3.2 4.4 3.4 4.0 4.2 5.1 3.4 6.0 4.4 4.5 3.5
##  [919] 3.0 4.7 4.3 3.9 4.2 3.9 4.3 4.3 3.1 4.4 5.5 5.3 3.9 4.1 7.3 5.6 2.9 4.7
##  [937] 3.9 4.0 5.4 4.1 3.4 4.8 4.0 5.2 5.3 6.0 3.9 2.8 4.5 5.8 4.3 3.6 5.3 5.1
##  [955] 4.7 5.7 5.5 3.7 5.5 3.9 5.0 4.0 4.6 3.7 5.3 5.0 4.6 3.3 4.2 5.8 6.0 3.8
##  [973] 4.5 3.8 4.8 4.9 5.2 4.9 2.9 3.7 3.8 4.3 4.2 4.3 5.4 3.6 5.0 5.7 3.9 4.7
##  [991] 6.0 3.5 5.1 3.6 4.5 3.3 3.4 5.8 4.2 5.3
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
##   2.8   6.1
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
##    [1] 3.2 4.2 4.1 3.8 4.3 4.6 4.7 3.4 4.6 3.8 5.4 5.4 5.0 5.0 5.5 3.9 4.0 4.9
##   [19] 3.7 4.2 5.0 5.4 3.9 5.2 3.9 5.7 3.9 4.2 2.4 5.4 4.9 4.7 5.0 5.8 4.7 5.3
##   [37] 2.7 3.1 4.1 5.5 4.5 4.5 3.4 3.9 5.0 4.2 4.1 3.6 5.2 3.1 4.6 4.7 4.5 3.8
##   [55] 3.9 4.0 5.2 5.3 5.5 4.3 3.9 4.1 3.0 4.3 3.5 3.1 3.1 5.1 5.2 3.0 4.2 4.6
##   [73] 4.8 4.2 4.9 3.9 4.5 4.9 4.3 6.0 4.9 4.4 5.2 4.9 2.6 3.4 5.3 5.2 6.8 4.2
##   [91] 4.4 4.6 5.0 4.0 5.2 4.1 3.6 4.4 3.5 4.8 3.3 5.0 3.9 4.8 4.2 4.9 6.0 5.0
##  [109] 4.6 3.5 5.1 5.4 3.9 4.3 4.0 3.9 5.0 4.3 3.9 6.2 2.1 3.8 6.4 5.6 4.9 4.1
##  [127] 4.8 4.1 3.1 4.3 3.7 5.3 3.2 5.7 5.1 5.2 4.1 5.5 4.2 3.4 5.1 6.3 3.9 3.8
##  [145] 3.9 4.2 7.1 2.3 5.1 5.2 4.9 6.5 3.7 3.1 5.2 4.6 5.6 5.0 4.5 3.5 5.9 4.8
##  [163] 5.1 5.1 5.1 3.9 5.3 4.0 5.1 3.4 2.3 5.4 4.8 4.9 3.5 4.7 4.4 4.1 4.3 3.5
##  [181] 2.4 4.6 5.9 4.9 4.5 4.9 3.7 6.4 4.8 5.0 2.4 3.6 3.9 3.0 3.0 5.3 5.3 4.6
##  [199] 4.5 6.7 4.6 4.4 6.6 5.4 4.7 5.0 3.9 4.2 3.9 4.7 5.9 2.3 6.0 3.4 6.7 3.8
##  [217] 5.6 3.9 5.8 4.5 3.9 4.9 3.8 5.7 4.6 4.6 3.5 4.9 5.7 5.6 6.3 4.0 5.4 4.1
##  [235] 4.3 3.8 5.6 3.1 4.8 6.1 3.9 3.1 4.4 4.3 4.2 4.0 5.8 4.9 3.1 3.1 4.4 6.3
##  [253] 4.2 6.3 5.1 4.6 3.0 4.7 4.8 4.7 4.2 4.5 5.0 4.9 3.8 4.4 2.7 4.3 4.7 5.2
##  [271] 3.1 4.4 4.2 4.8 3.1 4.7 5.5 4.4 4.8 3.4 4.0 5.0 4.9 3.2 3.2 6.1 3.8 4.7
##  [289] 3.5 5.0 5.4 4.2 4.7 5.1 3.2 5.1 5.3 5.0 2.6 4.5 4.9 5.5 5.5 4.4 6.0 4.4
##  [307] 4.9 4.4 4.1 5.3 5.8 3.9 3.2 3.4 5.3 5.1 3.8 4.5 4.8 3.5 4.0 5.1 5.3 4.6
##  [325] 4.8 5.1 3.1 5.1 3.2 4.5 3.8 4.5 4.6 4.8 3.8 6.6 4.5 4.3 7.1 5.9 4.6 5.1
##  [343] 6.3 4.7 4.4 4.0 5.0 6.2 4.1 3.7 4.0 3.6 4.7 6.0 4.7 4.6 5.5 4.5 5.8 4.9
##  [361] 5.0 4.3 4.1 5.0 2.7 5.3 2.3 3.8 4.8 2.6 4.1 2.3 2.9 3.8 4.4 7.2 4.8 3.5
##  [379] 6.1 4.1 5.6 5.4 6.3 4.2 3.0 5.5 3.5 4.0 4.9 4.1 5.1 4.5 3.3 3.6 5.4 4.3
##  [397] 4.0 5.9 4.9 4.7 5.5 4.6 4.4 3.2 2.6 6.7 5.9 5.4 5.0 6.1 3.7 3.8 4.4 5.2
##  [415] 5.5 4.6 4.2 4.3 4.3 5.4 4.5 4.6 3.8 5.2 5.5 5.5 5.0 4.6 4.6 5.1 4.9 3.6
##  [433] 5.1 5.0 4.7 5.4 4.9 5.8 5.2 5.7 3.8 4.6 4.6 4.3 3.3 5.0 4.9 3.6 4.6 3.7
##  [451] 4.1 4.2 4.8 6.0 5.7 2.6 5.6 4.7 3.6 4.1 4.8 5.0 3.3 4.7 2.7 4.6 3.6 4.6
##  [469] 5.0 4.1 4.5 4.7 5.8 3.9 5.4 3.5 4.1 4.4 4.2 5.5 4.9 3.7 6.5 3.3 4.8 4.6
##  [487] 4.2 4.2 6.0 5.0 3.7 4.8 4.1 4.3 3.8 4.9 3.6 3.2 3.9 4.4 3.5 3.8 3.9 6.4
##  [505] 6.2 4.7 5.6 3.4 5.5 4.8 5.7 5.0 3.8 5.1 4.7 5.3 3.2 3.5 2.6 4.7 4.9 4.4
##  [523] 3.5 5.4 4.5 5.8 5.1 6.3 5.8 3.3 4.0 3.3 4.1 3.9 4.9 4.3 3.8 3.2 4.0 4.7
##  [541] 3.7 4.1 4.4 5.3 4.8 3.9 5.1 4.8 4.2 5.0 5.9 5.0 5.8 3.6 5.4 5.3 3.7 5.8
##  [559] 4.6 5.5 3.5 4.1 6.3 4.9 6.9 4.3 5.6 5.7 4.1 4.8 4.7 5.4 5.4 6.7 5.0 5.4
##  [577] 4.7 4.7 4.5 4.9 4.9 4.4 5.3 5.7 5.7 4.2 3.6 4.3 6.3 5.0 2.5 4.1 6.2 3.6
##  [595] 3.7 5.6 3.9 3.9 3.3 4.8 4.6 6.2 2.6 4.5 4.6 5.1 3.2 3.0 4.3 4.7 4.1 3.6
##  [613] 3.7 5.9 3.2 4.5 6.1 4.9 3.4 4.2 4.2 5.4 5.8 4.0 2.7 4.7 4.6 5.7 5.0 4.9
##  [631] 4.6 4.2 4.5 5.0 3.8 4.9 4.9 3.5 3.9 5.4 3.6 5.4 5.3 4.6 5.1 5.9 2.1 4.7
##  [649] 4.0 5.5 4.2 4.1 5.1 3.0 4.6 5.1 3.9 4.1 3.9 3.7 3.4 5.3 5.8 5.2 3.9 3.0
##  [667] 4.6 2.9 4.5 4.8 4.7 2.0 4.5 6.7 4.3 5.4 4.2 2.9 5.9 4.4 5.9 3.7 5.0 3.5
##  [685] 4.0 4.3 2.6 3.9 2.4 5.4 3.3 4.1 5.0 5.5 6.7 5.4 5.3 5.0 4.8 4.7 3.8 4.3
##  [703] 5.6 4.8 5.1 3.2 4.4 3.8 4.7 2.7 4.1 5.1 4.7 6.0 2.7 5.4 5.4 4.2 5.2 5.7
##  [721] 3.8 4.4 5.2 3.7 4.7 5.5 2.8 3.6 4.3 4.1 4.4 5.2 4.9 3.3 6.6 3.6 5.2 4.8
##  [739] 3.4 4.3 4.5 3.2 3.6 3.6 5.9 4.5 4.1 5.0 5.0 3.6 4.0 3.4 4.0 4.6 3.1 6.4
##  [757] 4.4 4.8 5.1 5.9 3.0 6.8 3.5 4.4 4.3 5.6 1.8 5.6 5.1 4.6 4.2 4.8 5.2 5.5
##  [775] 5.2 3.2 5.6 2.3 2.6 3.8 3.9 5.5 4.5 4.0 4.7 3.8 5.7 3.5 4.6 5.1 4.3 3.7
##  [793] 4.9 4.7 5.1 4.7 3.6 5.8 3.2 5.1 4.8 4.6 3.2 5.2 4.4 5.8 4.9 4.9 5.2 6.0
##  [811] 4.5 4.2 5.9 5.0 4.4 3.6 3.5 6.5 4.6 4.9 5.5 4.4 4.6 5.1 3.9 4.1 4.8 5.6
##  [829] 5.6 5.0 4.0 3.7 5.5 4.0 4.6 4.1 3.4 6.4 3.0 6.5 5.0 4.1 5.1 4.7 4.5 5.3
##  [847] 4.5 4.9 3.6 4.2 3.5 4.8 4.8 3.7 5.1 2.6 3.9 4.0 5.7 4.6 3.4 4.8 4.4 5.1
##  [865] 3.2 4.5 4.5 4.4 3.4 4.3 4.8 4.8 3.6 3.9 4.9 3.6 4.1 4.8 4.8 5.0 6.4 5.9
##  [883] 3.1 2.1 5.4 5.1 4.1 3.3 3.9 4.2 4.3 4.5 3.4 5.9 3.4 4.2 2.8 6.5 3.7 5.5
##  [901] 4.4 4.4 3.2 4.4 4.3 3.8 7.0 4.5 4.0 2.7 5.1 5.0 4.9 4.5 4.3 4.4 4.0 4.3
##  [919] 5.6 4.9 3.3 4.8 3.3 4.8 5.3 4.8 3.8 5.0 5.1 3.9 3.3 3.8 5.1 4.9 4.2 4.6
##  [937] 6.6 5.7 5.0 4.7 3.1 4.1 3.5 4.1 5.6 3.9 5.2 3.6 6.4 4.3 6.9 5.2 5.1 3.8
##  [955] 4.0 2.7 3.0 4.1 5.7 4.5 4.8 4.7 4.7 5.0 3.9 5.9 4.0 3.7 5.3 3.7 4.9 7.2
##  [973] 4.3 5.7 6.6 3.8 4.1 4.2 4.2 2.8 2.8 3.2 5.4 4.9 4.1 4.0 4.7 4.6 4.6 5.1
##  [991] 6.3 4.8 3.4 3.0 4.9 3.2 5.5 3.5 2.8 4.5
## 
## $func.thetastar
## [1] 0.0241
## 
## $jack.boot.val
##  [1]  0.64210526  0.42666667  0.29863014  0.23714286  0.08028571 -0.07134670
##  [7] -0.14327485 -0.27361111 -0.32080925 -0.53719512
## 
## $jack.boot.se
## [1] 1.050737
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
##    [1] 3.9 4.2 3.6 4.4 4.0 4.1 4.3 4.4 3.2 4.2 5.5 4.3 3.6 4.3 4.7 5.9 5.2 4.6
##   [19] 3.8 4.3 2.7 5.0 5.9 5.8 5.8 4.4 3.1 5.7 3.8 3.8 4.4 5.2 3.3 4.5 3.1 3.8
##   [37] 4.4 5.2 5.2 2.9 5.1 3.6 4.2 4.2 2.9 5.2 3.8 4.4 4.6 4.9 5.0 6.8 5.1 5.5
##   [55] 5.5 5.2 3.9 5.9 4.5 5.1 3.7 4.9 5.8 2.1 2.6 3.6 3.3 4.1 5.8 3.5 3.8 4.1
##   [73] 5.8 3.6 3.4 4.6 5.6 5.1 5.2 4.3 3.9 5.3 4.4 4.0 3.3 4.0 4.7 4.0 4.1 4.9
##   [91] 4.3 4.1 5.1 4.9 4.7 5.1 4.6 4.2 5.5 3.7 3.8 3.7 4.9 5.0 5.4 4.9 4.3 4.1
##  [109] 4.9 4.2 5.3 4.8 3.1 4.4 5.4 3.5 6.3 3.0 4.0 3.4 4.9 4.0 4.4 5.3 4.9 4.5
##  [127] 3.7 5.3 4.5 3.9 3.8 3.6 4.4 5.0 5.5 4.0 4.8 4.2 4.1 5.0 4.7 4.1 3.9 4.8
##  [145] 3.9 5.2 4.3 5.1 3.6 5.9 4.1 3.6 4.3 2.9 4.5 6.5 5.0 4.0 3.5 4.3 5.3 3.2
##  [163] 2.6 3.4 3.8 4.5 4.8 4.7 5.6 5.0 4.3 5.5 4.5 5.1 5.7 4.3 3.6 2.3 5.5 3.7
##  [181] 5.2 6.3 5.5 4.5 4.4 5.8 5.0 4.1 4.6 5.9 5.7 4.7 5.0 5.5 3.8 4.2 3.7 5.1
##  [199] 5.1 2.4 5.2 5.5 4.1 4.5 4.4 4.3 4.8 3.5 4.5 4.8 4.7 4.5 5.3 4.3 4.6 5.8
##  [217] 4.6 4.6 3.8 3.9 5.0 4.7 3.5 3.7 5.3 5.6 5.1 5.4 3.7 4.5 2.3 5.2 4.9 4.8
##  [235] 4.9 5.1 4.1 5.4 4.0 4.1 3.6 5.0 5.3 4.4 4.6 4.5 5.3 3.9 4.2 5.0 5.4 3.3
##  [253] 3.5 4.7 4.0 3.8 5.5 5.9 5.2 4.0 5.6 4.6 4.6 4.3 5.1 4.1 4.5 3.2 5.1 5.7
##  [271] 4.7 2.9 4.5 4.4 4.2 4.6 3.5 5.0 5.1 3.2 3.8 5.0 5.7 2.8 3.6 3.8 6.4 5.5
##  [289] 5.8 6.7 3.9 3.5 3.4 3.9 3.8 5.8 4.3 2.7 4.2 4.4 6.6 4.1 6.6 4.5 4.2 5.6
##  [307] 5.7 5.1 4.9 4.1 3.6 2.8 5.8 5.5 4.3 6.4 5.2 5.2 2.9 6.1 4.5 4.3 3.1 4.2
##  [325] 3.3 4.9 4.2 4.3 5.3 5.0 4.6 5.3 5.1 2.8 3.5 4.1 2.8 5.2 3.8 3.6 3.5 5.7
##  [343] 3.4 5.3 3.0 4.0 4.6 3.9 5.9 4.5 3.9 5.0 2.9 3.5 4.2 2.8 3.4 6.7 5.4 3.7
##  [361] 4.1 3.0 4.1 5.2 4.7 5.0 4.8 5.9 3.7 4.3 5.1 4.5 4.2 4.2 5.1 3.9 5.5 3.8
##  [379] 5.0 5.0 5.3 4.4 4.1 4.6 2.0 6.9 6.0 4.3 4.1 4.6 3.5 4.4 3.6 4.1 5.5 5.1
##  [397] 4.7 4.4 5.8 4.1 4.6 4.9 4.6 5.9 4.4 4.3 3.8 5.1 5.3 4.4 3.9 3.7 5.2 3.8
##  [415] 3.7 4.6 4.1 3.0 3.7 4.6 5.4 4.2 3.4 5.3 4.9 4.9 5.1 2.6 6.1 4.1 3.7 4.0
##  [433] 4.5 8.0 4.9 4.9 3.9 4.2 4.6 4.7 4.2 2.6 4.3 4.2 4.2 5.5 5.4 4.0 3.2 4.8
##  [451] 5.0 3.1 4.1 5.7 4.5 4.4 4.1 2.6 4.2 4.9 4.2 3.5 3.6 3.1 4.9 2.6 3.5 5.4
##  [469] 4.0 5.4 5.0 4.3 5.8 4.8 4.5 5.2 3.4 4.6 3.6 4.9 3.8 4.5 4.7 5.5 5.1 2.4
##  [487] 6.4 3.6 2.9 4.7 4.1 3.8 4.7 3.6 5.6 4.5 3.7 4.4 4.6 5.0 4.7 5.2 4.4 4.3
##  [505] 6.2 4.3 3.9 3.8 4.6 3.7 3.1 5.7 4.3 5.1 4.4 5.4 4.5 4.3 4.7 5.1 4.1 3.7
##  [523] 4.9 4.8 4.4 5.1 4.5 4.3 5.2 6.0 4.6 5.4 4.9 3.8 2.9 4.6 4.5 4.2 3.9 3.5
##  [541] 3.4 4.2 3.2 5.8 4.3 4.2 4.1 3.4 2.6 3.2 5.3 3.7 5.1 4.7 3.5 4.2 4.5 4.7
##  [559] 4.1 5.5 4.4 4.7 5.5 5.4 5.6 3.7 3.5 3.8 3.9 5.3 4.7 4.6 4.2 3.5 4.5 4.5
##  [577] 3.9 6.3 4.5 5.7 6.0 3.9 4.0 5.3 4.8 4.0 3.8 4.1 4.8 5.1 5.4 5.1 3.8 2.7
##  [595] 3.9 5.5 4.2 3.9 5.6 4.7 3.8 4.7 4.8 5.1 5.5 4.6 6.0 2.4 5.6 3.6 4.2 4.6
##  [613] 5.9 4.2 3.9 5.5 3.4 5.2 4.2 5.3 4.0 4.1 5.5 4.5 4.6 3.7 4.9 5.0 4.9 4.0
##  [631] 4.0 3.6 4.1 5.3 5.9 4.3 3.3 2.9 5.6 4.8 4.4 3.5 5.6 3.8 4.2 3.9 4.2 3.6
##  [649] 3.8 4.4 4.4 5.7 3.4 4.7 4.5 4.1 4.0 4.2 5.8 3.6 4.5 4.4 4.9 4.9 6.7 4.6
##  [667] 3.9 3.7 4.4 4.8 3.3 4.3 4.4 5.2 3.5 5.1 4.7 4.6 3.0 4.7 5.1 6.1 3.3 3.5
##  [685] 4.0 3.2 4.5 3.6 3.1 3.9 3.9 5.7 5.1 4.7 4.6 5.6 4.7 5.4 4.0 3.4 4.5 5.0
##  [703] 5.1 5.5 4.5 5.0 5.9 3.3 5.8 3.7 3.3 4.4 6.5 3.9 5.2 3.9 4.4 5.4 4.4 4.3
##  [721] 4.5 5.1 4.1 4.5 5.5 3.1 5.1 4.6 5.9 5.0 4.4 5.5 4.4 3.7 3.3 3.2 4.6 5.0
##  [739] 5.1 4.7 2.9 5.4 4.0 4.7 3.5 5.6 4.4 4.6 5.2 3.1 3.9 5.3 3.5 2.6 4.8 4.9
##  [757] 4.2 4.8 4.2 4.5 5.7 2.5 5.4 5.4 3.8 3.3 3.3 3.9 5.3 3.5 1.4 4.3 5.0 4.6
##  [775] 4.1 5.4 5.5 4.5 3.7 4.5 3.9 2.3 5.2 4.5 4.7 5.9 4.1 5.0 4.5 4.6 4.3 5.7
##  [793] 5.9 3.9 4.5 4.0 4.1 3.7 3.7 4.0 4.5 4.7 4.8 4.2 4.4 4.6 4.3 4.3 5.3 4.2
##  [811] 5.1 5.2 4.4 5.0 2.9 5.4 3.9 5.2 4.7 4.5 5.0 4.4 3.8 3.8 4.9 4.3 3.4 4.6
##  [829] 4.1 5.4 4.0 4.1 3.4 4.5 4.0 3.0 5.6 4.4 3.8 4.9 4.6 5.3 4.3 5.6 4.9 4.4
##  [847] 4.9 3.9 5.5 4.4 5.3 6.1 5.2 3.8 4.3 3.4 2.1 4.0 3.1 2.6 3.5 2.1 4.8 3.8
##  [865] 5.3 4.9 3.5 4.6 2.5 4.3 3.7 4.7 4.2 3.5 4.7 4.5 5.2 3.5 5.2 5.7 4.9 4.8
##  [883] 4.7 4.7 4.5 3.8 4.6 3.4 3.1 2.2 5.6 5.6 4.0 5.5 2.9 5.5 5.7 2.7 3.9 5.3
##  [901] 2.8 5.5 4.5 4.1 5.3 4.6 5.6 5.0 5.6 5.6 4.7 3.3 5.4 5.1 5.9 4.4 2.9 5.3
##  [919] 4.9 6.1 5.2 4.3 4.6 3.3 2.6 4.3 4.1 3.5 4.2 3.5 5.6 3.6 4.8 4.1 3.9 4.8
##  [937] 4.7 4.7 3.8 4.3 4.0 2.5 3.8 5.9 3.6 4.9 4.4 5.5 2.0 4.2 3.9 4.1 6.5 4.8
##  [955] 4.4 5.4 4.0 5.6 4.1 5.1 3.9 3.4 4.4 3.8 4.6 4.3 4.3 6.0 3.8 5.0 4.7 4.4
##  [973] 3.5 5.0 5.3 4.0 6.4 3.8 4.6 2.9 3.9 4.0 6.6 4.4 3.2 4.1 4.0 2.5 4.7 3.4
##  [991] 4.2 5.5 3.7 4.1 3.4 5.0 3.8 3.3 5.0 4.1
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.3 5.3 5.2 5.1 5.0 4.8 4.6 4.6 4.5
## 
## $jack.boot.se
## [1] 0.946784
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
## [1] 0.9605606
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
##   2.2796436   3.7778163 
##  (0.9543275) (1.7683960)
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
## [1]  0.6624708  0.7738608 -0.1198796  0.5162335  1.2254776  1.6977304
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
##    [1]  0.084632875  0.823036489  0.315008292  0.703540349  0.341679699
##    [6]  0.932618391  1.517541566  1.342042594  0.971321968  0.575283201
##   [11]  1.228370714  1.520987817  1.406614668  0.086460299  1.140735682
##   [16]  0.837108926  0.162209598  0.832586867  0.618491190  0.088591359
##   [21]  0.696832591  1.059762109  0.567788782  0.908911003  1.674695756
##   [26]  0.106055515  1.419930401  1.233640141  1.815079233  0.089411232
##   [31]  0.818534833  0.506071289  0.530233574 -0.057632398  1.068758933
##   [36]  0.200585848  1.288586188  0.232971597  1.976000320  1.193298568
##   [41]  1.285490774  1.501249886  0.523604760  1.049479546  0.407289439
##   [46]  0.385253890  1.076850703  0.511341486  1.122190124  0.987215555
##   [51]  0.260112166  0.540994084  1.810726308  0.846720385  0.619462160
##   [56]  0.976321507  0.086460299  0.836921754  0.150286398  0.825065871
##   [61]  0.631065616  1.553093273  0.437580912  1.294689983  0.676969634
##   [66]  1.014968215  0.713487508  0.766811101  0.250335874  0.939919959
##   [71]  1.213628772  1.421322195  0.088601441  0.935134914  1.682506980
##   [76]  0.036411108  1.137465041  1.083081537  1.304605187  0.493020343
##   [81]  0.820430491  1.228666801  1.549518052  0.265810385  0.781942817
##   [86]  1.054815666  0.488370147 -0.034766039  0.953729239  0.788816039
##   [91]  0.982227002  0.207149680  0.426705846  0.345342503  0.894867616
##   [96]  1.401117327  0.622445404  0.233593053  1.108437143  1.244803026
##  [101]  1.953127196  0.560543887  0.763122189  0.764347345  1.030026300
##  [106]  0.175125367 -0.240870529  0.205282743  0.044063733  0.582965104
##  [111]  1.674703283  0.053603873  1.130218071  0.549205382  0.766661914
##  [116]  0.345034469  0.647153849  0.975738758  0.754611115  1.000312281
##  [121]  0.142168533  0.687160832  0.773936491  0.855511440  0.853860887
##  [126]  0.089046591  1.277069419  0.619610354  1.160244989  0.044241472
##  [131]  1.633150211  0.689547054 -0.913519774  1.364633696  0.556871141
##  [136]  0.377327201  0.751178561  0.510757194 -0.191211612  0.970280499
##  [141]  1.399725645  1.013833535  0.986439636  1.385623699 -0.126401987
##  [146]  0.890397468  0.474297416  0.453981218  1.392885184  1.633352078
##  [151]  0.208971658  0.490882107  0.161680686  1.120145938  0.768476015
##  [156]  0.433409718  0.230835154  1.312380052  2.075473321 -2.558479689
##  [161]  0.560072377  0.355923459  0.660500173  1.908984907  0.670035201
##  [166]  1.226928940 -0.298660126  0.855956669  0.221209943  1.270523180
##  [171]  0.996313210  0.526374523  0.563107424  1.684598917  0.959648384
##  [176]  0.584532212  0.573245870  1.008058814  0.349202626  1.392169334
##  [181]  0.516987215 -0.447338437  1.430330049  0.954163494  1.203703341
##  [186]  0.447119664  0.982331052  1.482561024  0.868105199  0.074819688
##  [191]  0.542223816 -1.559995139  1.953936582  1.199585743  1.234484959
##  [196] -0.160293752  0.451028230  0.537368274  1.651932132  1.192039223
##  [201]  0.085207567  1.273189857  1.211476219  1.046965244  0.324493710
##  [206]  0.986038619 -0.206279989  0.414870963  0.769839439  0.571421699
##  [211]  0.700902849  0.406018296  1.604217761  0.555538654  0.437488725
##  [216]  0.345381248  0.799001581  0.112399347 -0.080096340  1.564155321
##  [221]  1.081094158  0.716553073  1.315585699  2.070369106  0.824562428
##  [226]  0.826742015  0.573363390  0.470419470 -0.205319427 -0.144033166
##  [231]  0.171494094  0.193565947  1.222422809  1.210398760  0.773973580
##  [236]  0.736744265  0.389112559  1.563570518  0.774721067  0.104896309
##  [241]  0.883379019  0.508803432  1.222466252  0.107398157  1.937378559
##  [246]  0.284561910  0.151358879 -0.179025477  0.468680801  0.863051160
##  [251]  0.931209924  0.139469665  1.043285490  0.512451433  0.731277456
##  [256]  0.238186347  0.534366403  0.133095290  0.466836089  0.759908299
##  [261]  0.696102056  1.976263307  1.404250446  1.002613530  0.176824913
##  [266]  1.374064602  0.170546439  0.289856772 -0.074396252  1.944035591
##  [271]  1.673793212  0.976314925  0.550953203  0.685731517  1.386654591
##  [276]  0.893800905  0.171893004  0.206969024  0.866411298  0.662499803
##  [281]  1.669221382  1.175584444  2.255545812  0.620069397  1.247087310
##  [286]  1.540537269  1.137023274 -0.117924616  1.157983312  1.023638597
##  [291]  0.387516434  1.511712565  1.302515432  0.699660171  1.112070415
##  [296]  0.949123677  0.245559453  1.113205908  0.659426930  0.727633050
##  [301]  0.422362408  0.833130136  0.873934392  0.452040272  0.859143236
##  [306]  0.767628604  0.783649381  1.039208457  0.100719595  0.590130237
##  [311]  0.397335721 -0.138178545  1.397012664  0.169462889  0.153971877
##  [316]  1.673172535  0.524857851  0.655817929  0.486792208  1.001043689
##  [321]  1.397797937  0.300243244  0.484942041  0.605578565  1.433407877
##  [326]  0.388401111 -0.403062502 -0.455510133  0.956814005  1.601902688
##  [331]  0.892594221 -0.142703623  0.326629866  0.919548562 -0.055926834
##  [336] -0.041090309 -0.454537437  0.611058592  0.747091305  0.759365222
##  [341]  1.369799050  0.874335341  0.819222804  1.050365243  0.957578172
##  [346]  0.884367528  0.192117600  0.220492215  0.616709181  0.150578424
##  [351]  0.942154354  0.418622680  0.894366977 -0.233129788  0.501377352
##  [356]  0.762567066  0.194876674  0.607404261  0.492291975  0.651783489
##  [361] -0.519882026  1.925196550  0.888491550  0.616577023  1.030026300
##  [366]  1.085950677  0.394955513  0.264560374  0.857401992  0.644060304
##  [371]  1.131227283  0.836561258  1.441484579  1.143895098  0.032349750
##  [376] -0.189491873  1.126928098  1.275208334  1.542454869  0.493430494
##  [381]  0.500732317  1.068832376  0.522696452  0.667592165  1.344154132
##  [386] -0.176938652 -0.098260487  0.598655968  0.589091869  0.119877943
##  [391]  0.416910767 -0.097224089  0.622182407  1.021053072  0.573497602
##  [396]  0.613017674  0.896654123  0.381019202  1.166069326  1.398577775
##  [401]  0.848758519 -0.832411528 -0.231020204  1.052010087  0.489069420
##  [406]  1.033071822  0.255614318  1.065952726 -0.466410911  0.576890535
##  [411]  0.564455321  0.806771951  1.601345439  1.031738827  1.178005515
##  [416]  0.391649117  0.183876638  0.097781384  1.268236755  0.995146546
##  [421]  0.149529182  0.175977809  1.312955205  0.482555547  0.631440154
##  [426]  0.383694827  0.937093591  0.817972786 -0.072033971 -0.466558292
##  [431]  1.329675486 -0.986297533  0.442587912 -0.168356122  0.359439927
##  [436]  0.376239543  1.525914254 -0.169935157  0.492166204  1.635104903
##  [441]  1.423263521  0.473249887  0.742763583  0.537273789  0.365250350
##  [446]  0.770076013  1.076716529  0.967723064  0.819145652  0.850240330
##  [451]  1.060597115  0.785538389  0.574087924  0.512318031  0.407449964
##  [456]  0.654064914  1.033841751 -1.055859496  0.558588722  0.453859805
##  [461]  1.639184213  0.843939771  0.723006172  0.213432362  1.852209111
##  [466]  1.618672669  0.970900499  1.128814408  1.289232380  0.233114239
##  [471] -0.062659644  1.092821950  0.641740911  1.302229435  0.553746253
##  [476]  0.207537852  1.030264947 -0.033125498  0.598039574  0.334158667
##  [481]  0.119774586 -0.072370943  1.345282662  1.124478267  1.953842152
##  [486]  1.264123253  0.199171674  0.458961830  0.542820176  1.245474963
##  [491]  0.442095562  0.590843867  0.851826544  0.563719487 -0.325240987
##  [496]  0.282375208  0.794620398  1.394938509 -0.169861822  1.035706759
##  [501]  0.332030257  0.448262157 -0.436152559  1.147397042 -0.154993918
##  [506] -0.527197872  0.301580083  0.555641174  0.971181418  0.143486621
##  [511]  1.352837585  1.187143697  0.509525467  0.676126113  1.359969388
##  [516]  0.886908347  1.172584903  1.686444917  0.942825447  1.631277599
##  [521]  0.187707445  1.018485797  0.269089654  1.008006757  0.479367384
##  [526]  0.407726406  1.371574711  0.337276770  0.490856796 -0.171882410
##  [531]  0.993006222  0.640913096  0.191575844  0.341874141  0.015146731
##  [536] -0.043975532  1.047471682  1.232558123  0.888225720  0.243745742
##  [541]  1.300935685  0.382343030  0.919337144  0.036629075  0.186086862
##  [546]  1.486872204  1.142035650  0.575876787  0.383499035  0.269397083
##  [551]  0.501361433  1.676183322  0.389783269  0.176551398  0.216667048
##  [556]  0.269357059  1.698034693  0.441139923  0.170528380  0.688370792
##  [561]  0.635674552  0.154172989  0.026695591  0.214479227  1.140867552
##  [566]  0.959022837  0.712700871  0.180773297  0.673459579  0.710826668
##  [571] -0.217902842  0.280198134 -0.084972113  1.036235592  0.963076571
##  [576]  0.475584683  0.601758611  1.144219131  1.598151061  0.730816268
##  [581]  0.518212472  1.553403762  0.563431693  0.715447329  1.803333862
##  [586]  1.031005238  0.874623319  1.059598921  0.216167264  0.032892282
##  [591] -0.185765949 -0.056578576 -0.231741277  0.716897054  1.313846426
##  [596]  0.500390309  1.790773345  0.355123624  0.476135928  0.942378685
##  [601]  1.019911711  1.621508998  0.644533248  0.150547774  1.130560051
##  [606]  0.461811098  0.615982716 -0.153552270  1.031870775  0.063659979
##  [611]  1.236523244  0.137155068  0.035667601  0.341166389  0.617813105
##  [616]  0.834382224  0.539082651  0.765308454  1.678950334  1.456562767
##  [621]  0.723671095  0.723217775 -0.232064600  0.395309236  0.270365052
##  [626]  0.031442047  0.500323142  0.507389652 -0.013464710  1.075297647
##  [631]  0.514054895  0.883379019  1.039842492  0.889345832  0.746858079
##  [636]  0.732043989  0.669552808  1.065314867  0.415872793  1.059182104
##  [641]  0.995317277  1.287001268  0.931468543  1.620620751  0.642759410
##  [646]  0.714292279  0.769175404  0.682580847  0.434668753  1.266850579
##  [651]  1.225319799  0.501208534  0.418930747  0.694877789  0.024485856
##  [656]  0.580480301  1.144360609  0.375553752  1.254715498  0.133037512
##  [661]  1.109199051  1.310637761  0.736669925  0.529803627  1.056909117
##  [666]  1.232147363  1.038117599  0.416232357  0.866133212 -0.130604598
##  [671]  0.689163798 -0.106461699  1.195998704  0.431518797  0.086444167
##  [676]  0.393823777  0.504936904  1.033234391  1.902056361  0.410745417
##  [681]  0.511890786  0.875623326  0.839804891  0.524063350  0.336031502
##  [686] -0.107153509  0.475130748  1.326595355  0.692644142  0.744829340
##  [691]  0.827891199  0.142708830  1.784887910  0.265858590  0.635600297
##  [696]  1.032052788  0.913564082  0.699660836  0.589940308  0.935004760
##  [701]  0.487771021  0.958882880  0.251884441  0.424854498  0.435292722
##  [706]  0.221777684  1.109065488  0.636569721  2.628470916  1.042585646
##  [711]  0.949389730  1.305637839  1.417607485 -0.233500140  0.673564495
##  [716]  0.175744896  0.107816006  0.054182346  0.913766962  1.295794180
##  [721]  0.500009849  2.327404720  0.748294495  0.763454335  0.200833746
##  [726]  0.833906791  1.674535453  0.850624965  0.568959865  1.943494815
##  [731]  0.276461319  0.301713426  1.137023274  0.198364080  0.817003702
##  [736]  0.443023619  0.604371468 -0.090158881  1.293768024  0.752453738
##  [741]  0.735871481  0.585734037  1.882001171  0.238935319  1.295794180
##  [746]  0.542635442  0.695124056  0.764042420  1.032716577  0.930066474
##  [751]  0.592541894  1.062025313  1.543787121  0.909719801  0.498053889
##  [756]  0.990019824 -0.056708120  0.584532212  0.040931936 -0.132035541
##  [761]  0.524689522  0.597362502  0.169062354  0.731893733  0.832064112
##  [766]  1.713735420  1.491150899  0.213669848 -0.149690841  1.221515290
##  [771]  0.649665799  0.609451572 -0.120316482  1.132134192 -0.130388565
##  [776]  0.992872913  0.799283944  1.536320890  1.187745244 -0.173679416
##  [781]  0.934233420  0.357879909  0.553848380  1.017152242  0.753287071
##  [786]  0.621592132  1.322832007  0.502652699  1.144219131  0.314053003
##  [791]  0.444091735  1.101956064  0.197995865  1.802290718  0.382343030
##  [796]  0.749078430  0.134592698  1.177353769  0.223438972  0.488527398
##  [801]  0.068243534 -0.846967814  0.771053710 -0.604011305  0.162838713
##  [806]  1.085950677  0.740218833  1.116440040  0.588026212  0.053721198
##  [811]  0.147112054  0.860349419 -0.937349041  0.217950399  0.881780848
##  [816]  0.380386172  1.251747875  0.535070027 -0.147776888 -0.043578672
##  [821]  0.774361252 -0.460012433  1.502215586  1.224072842  0.729696642
##  [826]  1.536666971  0.932221842  0.143261034  0.410184308 -0.553051681
##  [831] -0.447690570  0.886987050  0.593798068  0.799283944  0.475380568
##  [836]  0.068347672  1.372984406  1.033173897  0.303572465  0.107816006
##  [841]  0.354847869  0.896014047  0.632656681  0.292346131  0.780829379
##  [846]  1.304240407  0.543243794 -0.856300196  1.593035163  0.792872276
##  [851] -0.080017719  0.553586202  0.205086030  0.936712299  0.617112979
##  [856]  0.937216190  0.150179250  0.326312610  1.140742155 -0.462436807
##  [861]  0.873896792  1.701183077  0.984467978  0.736622703  2.077859023
##  [866]  0.828405554  0.120467007  0.867906499  1.657001165  0.266748394
##  [871]  0.556377950 -0.246467598  0.671006387  0.519402311  0.690121854
##  [876]  0.899153914  0.828184211  0.183876638  0.849108062 -0.259605487
##  [881]  0.122115632  0.629804757  1.026231807  0.764470000  0.718817099
##  [886]  0.748441475  1.133949232  1.247260041  0.912496267  0.832344648
##  [891]  1.189029104  1.658036997  0.829912847  0.128523957  0.647153849
##  [896]  0.912483724  0.833407309 -0.926617223  0.743679433  0.478923737
##  [901]  0.746670880  1.160198620  0.529550465  0.630490343  0.705987959
##  [906] -0.387209721  1.557147120 -0.145849312  0.871714729  1.854915186
##  [911]  0.797316183  0.661019179  0.672934026  0.975434893  0.148498534
##  [916]  1.215277734  0.219514142  1.055675005  0.211166745  1.168481558
##  [921]  1.032320924  0.277898995  0.401732732  0.752579163  0.685321280
##  [926]  0.820430491  0.669000644  0.932395392  0.753185279  1.033873228
##  [931]  1.330945883  0.335857272  1.031870775  0.587033231  0.892338958
##  [936]  0.506624821  0.579217100 -0.212392753  0.052820555 -0.238740585
##  [941]  1.624942573  0.291684101  0.722041349  0.671103775  0.766327221
##  [946]  1.290153532  0.973359544  1.210450154 -0.275564168 -0.286507684
##  [951]  1.715827406 -0.047833262  0.309200731  0.173972080  1.694352867
##  [956]  0.006296831 -0.467597781  0.506174202  0.686989165  0.669213752
##  [961]  0.356303183  0.487657018  0.833807401  0.786387783  0.185439688
##  [966] -0.008334546  0.592379503 -0.091357237  2.136395808  0.984718789
##  [971]  0.163998577  0.119125891  0.492822569  1.117990988  0.183876638
##  [976]  0.405723100  0.223626509  0.951002285  2.338653226  1.142035650
##  [981]  0.691559567  1.355165390  0.211636488  1.009695643  0.462845079
##  [986]  1.127794622  1.054984645  0.960066715  0.070597916 -0.110821239
##  [991]  0.169663219  0.484942041  1.312955205  0.482739316  1.254720101
##  [996] -0.189574206  1.311345298  1.453157619  0.658647763  1.314912620
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
##   0.60343601   0.40132283 
##  (0.12690942) (0.08973654)
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
## [1]  0.50495897  0.02338375 -1.57507432 -0.66044627  0.03622177 -0.72494540
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
## [1] 0.0136
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9372051
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
## t1*      4.5 0.01071071   0.8943574
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 5 7 9 
## 1 1 4 3 1
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
## [1] -0.0375
```

```r
se.boot
```

```
## [1] 0.9217665
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

