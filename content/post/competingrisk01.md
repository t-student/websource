+++
title = "WORK IN PROGRESS ALERT - Competing Risks in R"

date = 2018-07-16
lastmod = 2018-07-16
draft = false


tags = ["R", "competing risk", "survival"]
summary = "Survival analysis in the presence of competing risks"


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


Competing risk happen when you have multiple risks that can distort your analysis of some measure. For example, if you are trying to estimate the time to relapse of a disease some patients will be censored by loss to follow up or the study ending, others may die. Time to relapse and death are competing risks.

When analysing CR we use cumulative incidence. Cumulative incidence is just the probability of an event by a particular time. You shouldn't use the Kaplan-Meier estimate of survival for each event because it will over estimate the incidence rates.

One approach applied to competing risks is to model the cause-specific hazard (rates). The hazard rate is just the instantaneous risk of the event. Assuming we have two competing events -- call them $A$ and $B$ -- the cumulative incidence of $A$ is the probability of it happening before a given point in time and is computed from the cause specific hazards and the overall survival function[^1]. Modelling  cause-specific hazards leads to a complex nonlinear relationship for cumulative incidence for whic it is difficult to summarise the covariate effects and the time varying effect.

The `timereg` and `cmprsk` packages let you estimate the cumulative incidence curve. 







[^1]: Which is actually the exponentiated negative sum of all the cause-specific hazards.
