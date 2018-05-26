+++
title = "Statistical Rethinking - Introduction and Sampling"

date = 2018-05-26
lastmod = 2018-05-26
draft = false


tags = ["bayesian", "binomial","R"]
summary = "First of a series of posts summarising Statistical Rethinking by McElreath"


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




Statistical Rethinking is a great book for learning about Bayesian Modelling. In this first post of quite a few I am going to summarise some key points from the first three chapters, but mostly chapter 2 and 3. It is mainly intended as a quick-ish reference so I will be skimming over a lot of content.

I like examples so let's dive in with the proportion of water covering the globe problem, hereafter the water/globe problem. In order to estimate this proportion we could take the globe throw it up in the air, catch it and record whether our index finger is on water or on land. If we do this enough times, we should be able to produce a reasonable estimate the proportion of water. Here is data story is:

1. The true proportion of water is $p$.
1. A single toss has probability $p$ of our finger being on water and $1-p$ of being on land.
1. We toss the globe the same way each time and all our tosses are independent of the others.

Bayesian modelling takes a prior knowledge and updates our understanding based on observed data. The appropriate lingua franca is that your *posterior* is proportional to the product of the *likelihood* and the *prior*. A few definitions:

## Likelihood

This gives you about the probability of any possible observation. The likelihood can be derived from the data story, here mapping to the binomial distribution. Specifically, we have the count of water observations $w$ is distributed binomially with probability $p$ over $n$ tosses of the globe.

$$
Pr(w|n, p) = \frac{n!}{w!(n-w)!}p^w (1-p)^{n-w}
$$


















