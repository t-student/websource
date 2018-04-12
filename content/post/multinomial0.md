+++
title = "The N'mare that is Multinomial Regression"

date = 2018-04-11
lastmod = 2018-04-11
draft = true


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

Think about a sample of zombies that fall into mutually exclusive species[^1]. You know, maybe we have violently hairy zombnies, zombies that make particularly disturbing dinner guest noises and zombies that you just wouldn't want to hang out with. Let's label these zombie types as type I, II and III and lets call non-zombies type 0. Now, we have data on a biomarker (mesured on a continuous scale from 0 to 300) obtained from the sample and we think that the level of this biomarker can be used to identify type 0, I, II and III (non)-zombies. How do we address this question statistically?

Well, we could subset our data and just run three logistic regression models comparing 0 versus type I, 0 versus type II and 0 versus type III. Below I construct some dummy zombie data by assuming a 



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








