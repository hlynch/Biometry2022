Week 6 Lecture
========================================================

## Week 6 Readings

For this week, I suggest reading Aho Sections 6.5. In class this week, we will also discuss [Bender and Lange (2001)](https://github.com/hlynch/Biometry2022/tree/master/_data/Bender_Lange_2001.pdf), [Berger and Berry (1988)](https://github.com/hlynch/Biometry2022/tree/master/_data/Berger_Berry_1988.pdf), [Cohen (1994)](https://github.com/hlynch/Biometry2022/tree/master/_data/Cohen_1994.pdf), [Gawande (1999)](https://github.com/hlynch/Biometry2022/tree/master/_data/Gawande_1999.pdf), [Johnson (1999)](https://github.com/hlynch/Biometry2022/tree/master/_data/Johnson_1999.pdf), and [Nuzzo (2014)](https://github.com/hlynch/Biometry2022/tree/master/_data/Nuzzo_2014.pdf). Yes, there are a lot of papers this week, but I would encourage you to read efficiently - get the gist but don't get bogged down in the details. There is an overarching theme among these papers I want to make sure you really take to heart.

There are additional readings that I highlight here because they may be of interest, or may provide additional perspectives on the themes of the week, including [this paper on the problems of null hypothesis testing](https://github.com/hlynch/Biometry2022/tree/master/_data/Anderson_etal_2000.pdf), [this cheeky analysis of disease and astrological sign](https://github.com/hlynch/Biometry2022/tree/master/_data/Austin_etal_2006.pdf), [this paper on the hazards of sequential Bonferroni](https://github.com/hlynch/Biometry2022/tree/master/_data/Moran_2003.pdf), and [this review of the past, present, and future of null hypothesis testing](https://github.com/hlynch/Biometry2022/tree/master/_data/Robinson_Wainer_2002.pdf).

## Family-wise error rates

The ''family-wise error rate'' is

$$
\alpha = 1-(1-\alpha^{’})^k
$$

where $\alpha^{'}$ is the ''per-comparison error rate''. This is an important formula, don’t just memorize it - make sure you understand it!

Solution #1: One solution is to narrow down the model a priori to a single model that you think best captures the relevant biology of the system. 

Solution #2: Another work-around is to lower the threshold for significance so that the Type I error rate for the whole family of comparisons is still 5$\%$. There are several ways to do this, of which we will discuss two:

Method #1: The first method simply involves rearranging the formula above

$$
\alpha=1-(1-\alpha^{'})^{k}
$$
$$
\alpha^{'}=1-(1-\alpha)^{1/k}
$$

This method, called the Dunn-Šidák method, simply sets the per comparison error rate as above. This assumes that all the comparisons are independent.

Method #2: If the comparisons are not independent, then it is better to use a more conservative method, called Bonferroni’s method (of the Bonferroni correction)

$$
\alpha^{'}=\alpha⁄k
$$

However, this can set the per-comparison Type I error rate so low that it severely inflates the probability of a Type II error.

Solution #3: Sequential Bonferroni’s test – 

Let’s say you start with 10 comparisons. You would test each comparison, rank them in order of their p-values, and discard the least significant (highest p value) if it was not significant at the $\alpha$/10 level, the next least significant if it was not significant at the $\alpha$/9 level, and so forth until all remaining comparisons were significant using a Bonferroni adjusted critical value for the number of remaining comparisons.

Solution #4: Resampling-based corrected p-values – 

$$
P_{corrected} = P(min(p) \leq p-observed|H_{0})
$$

In other words, if you were to simulate data under the null hypothesis, what is the probability that the smallest p-value among the set of comparisons made is smaller than or equal to the p-value that you actually obtained. (In other words, the adjusted p-value asks “how extreme is my most extreme p-value when compared against the most extreme p-values I would expect under the null hypothesis?)


