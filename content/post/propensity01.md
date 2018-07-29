+++
title = "Propensity Scores 101"

date = 2018-04-19
lastmod = 2018-04-19
draft = false


tags = ["propensity", "R", "matching", "observational"]
summary = "Introduction to Propensity Scores, what, why and how?"


[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-default.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-ocean.png"
caption = "Ocean"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-dark.png"
caption = "Dark"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-forest.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-coffee-playfair.png"
caption = "Coffee theme with Playfair font"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-1950s.png"
caption = "1950s"
+++

A propensity score is just the probability of receiving treatment - we have:

$$
Propensity \ Score = \pi_i = Pr(trt = 1|X_i)
$$

So if we had a single (pre-treatment) covariate, say age, on which the propensity score was computed we may have that older people were more likely to receive the treatment.

Now, it can be the case that two people with different covariate values have the same probability of treatment (i.e. the same propensity score). This means that those two people are just as likely to be in the treatment group.

Conveniently, it also means that if you restrict your sample to those individuals with the same propensity score you will get balance. In other words, conditional on propensity score, the distribution of the $X$'s will be balanced and hence the propensity score is a way to achieve **balance** between groups. BTW, we are assuming ignorability - treatment is randomised given $X$.

$$
Pr(X=x|\pi_i(X)=p, trt = 1) = Pr(X=x|\pi_i(X)=p, trt = 0)
$$

## Estimating the Propensity Score

In an RCT the $Pr(trt = 1| X) = Pr(trt = 0| X) = 0.5$ but in an observational study we do not know what $Pr(trt = 1| X)$ is. However, we observe the treatment status ($trt$) and we observe the covariates ($X$) so theoretically we should be able to estimate the propensity score?

We start by treating the $trt$ as the response and we identify a suitable method for estimating the probability of $trt$. Typically/commonly logistic regression is used but you can choose any method that gives us a probability of $trt$ for each subject.

In order to achieve a good outcome in terms of balance we need to have **overlap** in the propensity scores and we should also see a difference in where the peaks of the probability distribution are, like this:

![](/media/propensity01.jpg)

If you do not see something like the above but rather you see very little overlap then it is likely that the *positivity* assumption is violated. Additionally, if you have people that effectively had no chance of getting the treatment then we cannot hope to learn about the treatment effect by considering these people. An option here is to simply remove the people in the sample that do not overlap.

After we have got sufficient overlap we match on propensity score using nearest neighbour or optimal matching. However, prior to this it is common to take the *logit* of the propensity score (in order to spread out the score) which makes it a bit easier to match. Additionally, we can construct a maximum distance between propensity scores that we are will to tolerate. One method is to take the standard deviation of the logit transformed propensity score and set a threshold of 0.2 times the sd.

Once all this is done we can proceed with a standard analysis.





