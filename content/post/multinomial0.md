+++
title = "That is Multinomial Regression?"

date = 2018-04-15
lastmod = 2018-04-15
draft = false


tags = ["multinomial", "R"]
summary = "Explanation of what multinomial regression is, when to apply it and how to fit and check the models in R"


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

Ref: https://www3.nd.edu/~rwilliam/stats3/Mlogit1.pdf

Think about a sample of zombies (I know, sorry) that fall into mutually exclusive species[^1]. You know, maybe we have violently hairy zombnies, zombies that seem to make disturbing small talk at formal functions and zombies that you just wouldn't want to wake up next to. Let's label these zombie types as type I, II and III and lets call non-zombies type 0. Now, we have data on a biomarker (mesured on a continuous scale from 0 to 300) obtained from the sample and we think that the level of this biomarker can be used to identify type 0, I, II and III (non)-zombies. How do we address this question statistically?

Well, we could subset our data and just run three logistic regression models comparing 0 versus type I, 0 versus type II and 0 versus type III. 
But we don't do this. Instead we use a multinomial logistic model. Our dependent variable is zombie type and we use non-zombie as the reference level. We compute the probability of membership in the other categories compared to the probability of membership in the reference category. To do this we need to fit $M - 1$ models where $M$ is the number of levels in the dependent variable (4 here). We model the log odds, like this:

<div>
$$
log \frac{Pr(Y_i = m)}{Pr(Y_i = referant)} = \beta_{m,0} + \beta_{m,1} bioscore_i  = Z_{m,i}
$$
</div>

To compute the probabilities you need to do:

<div>
$$
Pr(Y_i = m) = \frac{exp(Z_{m,i})}{1 + \sum_{h=2}^M exp(Z_{h,i}) }
$$
</div>

and for the reference category you just replace the numerator with a 1.

After fitting the multinomial model you get a table showing the parameter estimates from each model. We are predicting the logit - this is the odds of membership in a given category of the outcome variable. The coefficients below give the change in the logit for each one unit change in the predictor.



```
--------------------------------------------------------------------------------
pathology0no~4 |      Coef.   Std. Err.      z    P>|z|     [95% Conf. Interval]
---------------+----------------------------------------------------------------
Benign         |  (base outcome)
---------------+----------------------------------------------------------------
Squamous       |
    Hscore_ngf |   .0991562   .0110688     8.96   0.000     .0774617    .1208507
         _cons |  -7.980021   .8811443    -9.06   0.000    -9.707032   -6.253009
---------------+----------------------------------------------------------------
Adenocarcinoma |
    Hscore_ngf |   .0682796   .0102071     6.69   0.000      .048274    .0882852
         _cons |  -5.596755   .7660139    -7.31   0.000    -7.098115   -4.095395
---------------+----------------------------------------------------------------
Small_Cell     |
    Hscore_ngf |   .0018791    .021452     0.09   0.930    -.0401661    .0439243
         _cons |  -2.827464   1.324856    -2.13   0.033    -5.424135    -.230793
--------------------------------------------------------------------------------

```

```
--------------------------------------------------------------------------------
pathology0no~4 |        RRR   Std. Err.      z    P>|z|     [95% Conf. Interval]
---------------+----------------------------------------------------------------
Benign         |  (base outcome)
---------------+----------------------------------------------------------------
Squamous       |
    Hscore_ngf |   1.104239   .0122226     8.96   0.000     1.080541    1.128456
         _cons |   .0003422   .0003016    -9.06   0.000     .0000609    .0019247
---------------+----------------------------------------------------------------
Adenocarcinoma |
    Hscore_ngf |   1.070665   .0109284     6.69   0.000     1.049458      1.0923
         _cons |   .0037099   .0028418    -7.31   0.000     .0008267    .0166492
---------------+----------------------------------------------------------------
Small_Cell     |
    Hscore_ngf |   1.001881   .0214924     0.09   0.930     .9606299    1.044903
         _cons |   .0591627   .0783821    -2.13   0.033     .0044089    .7939038
--------------------------------------------------------------------------------
```

Thus, the relative probability of being squamous rather than benign increases by about 10 percent for every unit increase in the hscore. (Relative probabilities are also called relative odds.)

A common mistake is to interpret the coefficient as meaning that the probability of squamous is higher for higher hscores. It is only the relative probability of squamous over benign that is higher. To obtain a fuller picture we need to consider the second equation as well. The coefficient of black in the home equation is 0.813. Exponentiating, we obtain



Detouring just slightly let's note first that within the context of a *polytomous response* we might be considering an *ordinal* response or we could be dealing with and unordered or *nomianal* response. Ordinal data implicitly contains more information than nominal data and by using this information we can usually construct simpler models.

Below I construct some dummy zombie data by assuming a 



{{< highlight r>}}
get.data <- function(n.per.grp = 50, 
                     p.ctl = 0.3,
                     or = c(or.i = 1.1, 
                            or.ii = 1.3, 
                            or.111 = 1.6)) {
  
  # Baseline odds:
  b0 <- log(p.ctl / (1 - p.ctl))
  bgrp <- log(or)
  beta <- matrix(c(b0, bgrp), ncol = 1)
  
  grp <- as.factor(rep(c(0, 1, 2, 3), each = n.per.grp))
  dd <- data.frame(grp = grp)
  mm <- model.matrix(~ grp, dd)
  
  # Linear predictor for the simple group comparison
  logit.y <- mm %*% beta

  p.y <- exp(logit.y)/(1 + exp(logit.y)) 
  y <- rbinom(n=nrow(mm), 1, prob = p.y)
  
  # Our pseudo sample.
  df.dat <- data.frame(id = 1:nrow(mm), 
                       z = grp,
                       z1 = mm[,2],
                       z2 = mm[,3],
                       z3 = mm[,4],
                       p.y = p.y,
                       y = y)
  
  return(df.dat)
}

set.seed(23452)
df.1 <- get.data()

df.2 <- droplevels(df.1[df.1$z == 0 | df.1$z == 1,])
summary(lm1 <- glm(y ~ z, data = df.2, family = binomial))
predict(lm1, type = "response",
  newdata = data.frame(z = unique(df.2$z)))


{{< /highlight >}}





[^1]: I think it is fair to generalise Darwin to the undead.








