+++
title = "A brms implementation of the analysis presented in \"Bayesian linear mixed models using Stan: A tutorial for psychologists, linguists, and cognitive scientists\""

date = 2018-01-19
lastmod = 2018-01-19
draft = false


tags = ["bayesian", "stan", "R"]
summary = "Bayesian linear mixed models using brms."


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




The title was stolen directly from the excellent 2016 [paper](https://arxiv.org/abs/1506.06201) by Tanner Sorensen and Shravan Vasishth. Here I recreate their analysis using [brms](https://cran.r-project.org/web/packages/brms/index.html) R package, primarily as a self-teach exercise. I am going to very much assume that the basic ideas of Bayesian analysis are already understood. I will add some informtion on prior and posterior predictive checks because I think not doing so missing a large part of the point of a Bayesian analysis. The original tutorial provided a hands-on introduction to fitting LMMs in a Bayesian framework using the probabilistic programming language [Stan](http://mc-stan.org/).

Let's start with the data. In brief the we have reading times (`rt`) in milliseconds of the head noun of the relative clause recorded in two conditions with 37 subjects and 15 items. The data have some missing values, but the focus here was on a complete case analysis because missing values are a can of worms in Stan and deserve a tutorial of their own. In total we are looking at 547 data points. 

Quoting Sorensen: 

> A **subject relative** is a sentence like *"The senator who interrogated the journalist resigned"* where a noun (senator) is modified by a relative clause (who interrogated the journalist), and the modified noun is the grammatical subject of the relative clause. In an **object relative**, the noun modified by the relative clause is the grammatical object of the relative clause like *"The senator who the journalist interrogated resigned"*. In both cases, the noun that is modified (senator) is called the head noun.

You can pick up what you need [here](https://github.com/vasishth/BayesLMMTutorial/blob/master/data/gibsonwu2012data.txt) and save it to a local folder, but you can also read that file directly.  We only need a part of the data so lets wrap it up in a function.



{{< highlight r>}}


library(tidyverse)
library(brms)

get.data <- function(){
  df.r <- read.table('https://raw.githubusercontent.com/vasishth/BayesLMMTutorial/master/data/gibsonwu2012data.txt')  
  # head(df.r)
  
  df.r <- df.r %>%
    dplyr::filter(region == "headnoun") %>%
    dplyr::select(-word) %>%
    dplyr::mutate(subj = as.factor(subj),
                  item = as.factor(item),
                  so = ifelse(type == "subj-ext", -1, 1)) 
  # head(df.r)
  # sort(as.numeric(unique(df.r$subj)))
  # sort(as.numeric(unique(df.r$item)))
  # nrow(df.r)
  
  return(df.r)
}


df.r <- get.data()

# Distribution of reading times. 
plot(density(df.r$rt), main = "Distribution of reading times")

{{< /highlight >}}

In the above code you will note that the `so` variable contains an indicator of 'o' (object relative) and 's' (subject relative) that are coded as 1 and -1 respectively. When coding up Stan models you need to be a bit more careful with your data -- we will come back to this later. 



<!-- A quick look at the reading times shows us what we expect to see, namely a heavily skewed distribution. 
{{< figure src="/media/brms-tutorial-01.png" title="PDF of reading times" >}} -->

The [brms](https://cran.r-project.org/web/packages/brms/index.html) package supports a wide range of (non-)linear multivariate multilevel models using Stan for full Bayesian inference. Many distributional assumptions are supported. Additionally, brms provides the capability of extracting the underlying Stan code and so are useful starting point if you want to do something more complicated. As a starting point we ignore the (very likely) possibility of correlated measures and fit a fixed effect model. We use weakly informative priors, but not the current `stan-dev` [reccommendations](https://github.com/stan-dev/stan/wiki/Prior-Choice-Recommendations) nor a Cauchy[^1] distribution as per Gelman et al. 2008 [paper](https://arxiv.org/pdf/0901.4011.pdf). Here, we adopt Normal priors, simply because they are easy to think about and rationalise. For reference, the parameterisations of the brms supported distributions can be found [here](https://cran.r-project.org/web/packages/brms/vignettes/brms_families.html). 

Prior to looking at the data, what do we know about average reading speed? Well, obviously, the lower bound is a little be greater than zero and a high value might be a couple of seconds. So, lets just say a typcial value is about 1000 millisecs and you could expect anywhere between about 100 and 2000 milliseconds and that gives us some guidance on what the intercept looks like. We are pulling numbers out of the air here, but it gets us a rough idea of what the typical value might be and we can (should) conduct sensitivity analyses with uninformative priors (that typically correspond with frequentist results).

$$ rt_i \sim Lognormal(\mu_i, \sigma) \\\\\\ \mu_i = \beta_0 + \beta_1 so_i \\\\\\  \beta_0 \sim Normal(6, 1) \\\\\\  \beta_1 \sim Normal(0, 10) \\\\\\  \sigma \sim Student-t(3, 0, 10) $$



OK, let's fit a model. The brms package can have multi-dimensional formula so we specify the formula explicitly as `myf` and use this approach repeatedly.


{{< highlight r>}}
# brms formula, more later...
myf <- bf(rt ~ so)
priors <- get_prior(myf,
                    data = df.r,
                    family = lognormal())
priors$prior[1:2] <- "normal(0, 10)"
priors$prior[3] <- "normal(6, 1)"
priors

blm0 <- brms::brm(myf, 
            data = df.r,
            family = lognormal(),
            prior = priors,
            control = list(max_treedepth = 10),
            iter = 2000,
            chains = 2, cores = 6, seed = 5453, save_model = "brm1.txt")
summary(blm0, waic = TRUE)
{{< /highlight >}}

The `blm0` is an instance of a `brmsfit` object. The `summary` view gives the following output, which is similar to the fixed effects model presented by Sorensen. What are these results telling us? First, the Rhat values are equal to 1, so the chains look like they converged OK. Second, the reading time for subject-relative has a central value around $exp(6.06 + 0.04) = 446$ millisecs and the object-relative has a central value of around $exp(6.06 - 0.04) = 412$ millisecs. These values align to the exponentiated mean of the log reading times for the two groups. 

<!--We expect typical values to be $exp(2 \times 0.6) = 3.3$ -->


```
 Family: lognormal 
  Links: mu = identity; sigma = identity 
Formula: rt ~ so 
   Data: df.r (Number of observations: 547) 
Samples: 2 chains, each with iter = 2000; warmup = 1000; thin = 1; 
         total post-warmup samples = 2000
    ICs: LOO = NA; WAIC = 7625.74; R2 = NA
 
Population-Level Effects: 
          Estimate Est.Error l-95% CI u-95% CI Eff.Sample Rhat
Intercept     6.06      0.03     6.01     6.11       1917 1.00
so           -0.04      0.03    -0.09     0.01       2000 1.00

Family Specific Parameters: 
      Estimate Est.Error l-95% CI u-95% CI Eff.Sample Rhat
sigma     0.60      0.02     0.57     0.63       2000 1.00

Samples were drawn using sampling(NUTS). For each parameter, Eff.Sample 
is a crude measure of effective sample size, and Rhat is the potential 
scale reduction factor on split chains (at convergence, Rhat = 1).

```

The marginal posterior distribution for each of the three parameters are shown below. The distribution for `b_s0` is predominantly below zero suggesting that the object-relative is easier to read. Unlike frequentist analyses, the results give us a view on the uncertainty in the error term (`sigma`) which ranges from 0.57 to 0.63 on the log scale.

{{< figure src="/media/brms01-posterior1.png" title="Posterior distribution for estimated parameters" >}}

Here's a view of the observed data overlayed with the posterior distribution for reading times showing the estimated typical reading time. The right hand plot shows a **posterior predictive distribution**, which embodies both the uncertainty inherent in our distributional assumption for the response and the uncertainty in the estimated parameters. The idea behind examining the posterior predictive distribution is that it should generate data that looks similar to the observed data. [McElreath](http://xcelab.net/rm/statistical-rethinking/) provides the clearest exposition I have read on this concept. The posterior predictive is a simulation of our original data conditional on the observed values.


Posterior means                    |  Posterior predictive
:---------------------------------:|:-----------------------------------:
![](/media/brms01-posterior2.png)  |  ![](/media/brms01-posterior3.png)


Another option for model checking is to use the [bayesplot](https://cran.r-project.org/web/packages/bayesplot/index.html) package, which provides a swag-full of posterior predictive checks that are more sophisticated than the single generated dataset shown above. Below we can see that simulated data has a much lower maximum value than that observed in the original data and the median of the simulated values is actually quite a lot higher than we observed.


PPC (max)                    |  PPC (median)  
:---------------------------------:|:-----------------------------------:
![](/media/brms01-ppc4.png)  |  ![](/media/brms01-ppc5.png)


However, posterior predictive checks grouped by subject and object relative (see below) appear to show that the issues manifest to a greater extent in the subject relative group. An outline of the code for the last few plots is also shown below.


PPC (max)                    |  PPC (median)  
:---------------------------------:|:-----------------------------------:
![](/media/brms01-ppc6.png)  |  ![](/media/brms01-ppc7.png)


{{< highlight r>}}
# Look at the posterior distribution
m.post <- as.matrix(blm0)
nrow(m.post)
df.post1 <- data_frame(type = c(rep('subj-ext', 2000), rep('obj-ext', 2000)), 
                      rt = c(m.post[,1] - m.post[,2], m.post[,1] + m.post[,2])  )
set.seed(324)
idx <- base::sample(1:nrow(df.post1), 600, replace = F)
df.post1 <- df.post1[idx, ]

# Posterior
ggplot(data = df.r , aes(x = type, y = rt))+
  geom_jitter(width = 0.2, height = 0, colour = "red", alpha = 0.3)+
  geom_jitter(data = df.post1, aes(x = type, y = exp(rt) ), 
              width = 0.2, height = 0, colour = "blue", alpha = 0.2)+
  ylab("Reading time (millisec)") + xlab("")


# Single draw just for demonstration
pp.tmp <- brms::posterior_predict(blm0, newdata = data.frame(so = c(-1, 1)))
dim(pp.tmp)

df.post2 <- data_frame(type = c(rep('subj-ext', 2000), rep('obj-ext', 2000)), 
                      rt = c(pp.tmp[,1], pp.tmp[,2])  )
set.seed(284729)
idx <- base::sample(1:nrow(df.post2), 1000, replace = F)
df.post2 <- df.post2[idx, ]
str(df.post2)

ggplot(data = df.r , aes(x = type, y = rt))+
  geom_jitter(width = 0.2, height = 0, colour = "red", alpha = 0.3)+
  geom_jitter(data = df.post2, aes(x = type, y = rt ), 
              width = 0.2, height = 0, colour = "blue", alpha = 0.2)+
  ylab("Reading time (millisec)") + xlab("")


# More useful PPC
library(bayesplot)
ppc_dens_overlay(df.r$rt, yrep[1:50, ])

ppc_stat(df.r$rt, yrep, stat = "max")
ppc_stat(df.r$rt, yrep, stat = "median")

ppc_stat_grouped(df.r$rt, yrep, stat = "max", group = df.r$so)
ppc_stat_grouped(df.r$rt, yrep, stat = "median", group = df.r$so)
{{< /highlight >}}


While Sorensen do not explore this avenue, one possibility is that we may have reasonable distributional assumptions but the notion of a shared variance across groups may not be accurate. The brms package readily supports this refinement via its forumla interface that we used earlier. We do not nominate any priors for the `sigma` model parameters so they will just be assigned uninformative defaults.

{{< highlight r>}}
myf <- bf(rt ~ so, sigma ~ so)
priors <- get_prior(myf,
                    data = df.r,
                    family = lognormal())
priors$prior[1:2] <- "normal(0, 10)"
priors$prior[3] <- "normal(6, 1)"

blm1 <- brm(myf, 
            data = df.r,
            family = lognormal(),
            prior = priors,
            control = list(max_treedepth = 10),
            iter = 2000,
            chains = 2, cores = 6, seed = 5453, save_model = "brm1.txt")
summary(blm1, waic = TRUE)
{{< /highlight >}}

The results (below) suggest that the standard deviations for reading time are different across subject and object groups. However, posterior predictive checks on the maximum and median values (not shown) are still adrift from the observed values. We won't take this further yet, but we may return to it later.

```
 Family: lognormal 
  Links: mu = identity; sigma = log 
Formula: rt ~ so 
         sigma ~ so
   Data: df.r (Number of observations: 547) 
Samples: 2 chains, each with iter = 2000; warmup = 1000; thin = 1; 
         total post-warmup samples = 2000
    ICs: LOO = NA; WAIC = 7614.42; R2 = NA
 
Population-Level Effects: 
                Estimate Est.Error l-95% CI u-95% CI Eff.Sample Rhat
Intercept           6.06      0.02     6.01     6.11       1897 1.00
sigma_Intercept    -0.53      0.03    -0.58    -0.46       2000 1.00
so                 -0.04      0.03    -0.09     0.01       1768 1.00
sigma_so           -0.11      0.03    -0.17    -0.05       1892 1.00
```








## To do

1. Prior predictive - how sane are our priors?
1. Correlation between the covariates?
2. varying intercepts 45 mins
3. varying intercepts varying slopes 45 mins
4. correlation between the two 40 mins
5. conclusions


{{< gist t-student b93167cf529607038406>}}

{{< highlight html >}}
<section id="main">
  <div>
   <h1 id="title">{{ .Title }}</h1>
    {{ range .Data.Pages }}
        {{ .Render "summary"}}
    {{ end }}
  </div>
</section>
{{< /highlight >}}

{{< youtube w7Ft2ymGmfc >}}



[^1]: The [Cauchy](https://en.wikipedia.org/wiki/Cauchy_distribution) distribution is truly quite a bizarre, some would say pathological, distribution as neither the expeccted value nor variance are defined. It also ryhmes with grouchy - no wonder.
