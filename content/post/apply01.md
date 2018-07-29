+++
title = "WORK IN PROGRESS ALERT - Using apply functions in R"

date = 2018-05-22
lastmod = 2018-05-22
draft = false


tags = ["apply", "R"]
summary = "Use apply to implement loops"


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



Here is a simple for loop as might be used in a simulation. This is just the first layer and produces one instance of the data. We would run this function a large number of times in our simulation. The function takes some parameters and generates a series of values in each time period. All of data is accumulated in a `data.frame` which is returned at the end of the function.

{{< highlight r>}}

# Initialisation of parameters and constants
n.sim <- 1 
n.periods <- 10 
pois.lambda <- 33.4 
pois.se <- 1.8276
gamma.shape  <- 0.4719746  
gamma.shape.se  <- 0.029900 
gamma.rate  <- 0.0097803 
gamma.rate.se  <- 0.000983 
set.seed(3952)

create.dat <- function(n.periods,
                pois.lambda, pois.se,
                gamma.shape, gamma.shape.se,
                gamma.rate, gamma.rate.se){
  
  
  df <- data.frame(period = integer(),
                   y = numeric())

  
  # Create observations
  for(i in 1:n.periods){

    # create n - number of obs in this time period
    n.per <- rpois(1, lambda = rnorm(1, pois.lambda, pois.se))
    
    
    # create y values of the obs this period
    y <- rgamma(n.per, 
                shape = rnorm(1, gamma.shape, gamma.shape.se),
                rate = rnorm(1, gamma.rate, gamma.rate.se))
    
    
    # store period number and x
    df <- rbind(df, data.frame(period = i, x = x))
  }
  
  df
}

{{< /highlight >}}



An alternative to this is to use `lapply`. First we construct the function that generates the data for a single period. Then we 

{{< highlight r>}}
create.dat  <- function(x, pois.lambda, pois.se,
                        gamma.shape, gamma.shape.se,
                        gamma.rate, gamma.rate.se){
  
  
  # create n - number of obs in this time period
  n.per <- rpois(1, lambda = rnorm(1, pois.lambda, pois.se))
  
  
  # create x values of the obs this period
  y <- rgamma(n.per, 
              shape = rnorm(1, gamma.shape, gamma.shape.se),
              rate = rnorm(1, gamma.rate, gamma.rate.se))
  
  data.frame(period = x, y = y)
}

# Now run the function of the number of periods.
l1 <- lapply(1:n.periods, create.dat, pois.lambda, pois.se,
       gamma.shape, gamma.shape.se,
       gamma.rate, gamma.rate.se)

df <- do.call("rbind", l1)


{{< /highlight >}}