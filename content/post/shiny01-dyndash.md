+++
title = "Shiny Stage 4 - Dynamic Dashboards"

date = 2018-04-10
lastmod = 2018-04-10
draft = false


tags = ["shiny", "R", "visualisation"]
summary = "Shiny - UI customisation"


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



Dashboards extend the functionality of shiny. Shiny uses [Bootstrap](https://getbootstrap.com/) for layout whereas Shinydashboard uses [AdminLTE](https://adminlte.io/themes/AdminLTE/index2.html), which is a theme built on top of Bootstrap. Here is a basic template - every dashboard must have the header, sidebar and body. However, these can be hidden if necessary. 


```
library(shiny)
library(shinydashboard)

ui <- dashboardPage(
  dashboardHeader(),
  dashboardSidebar(),
  dashboardBody()
)

server <- function(input, output){

}
shinyApp(ui, server)

```

Before we go any further, I should note, the docs are [here](http://rstudio.github.io/shinydashboard/).

OK, so Shinydashboard has analogous layout controls to shiny, for example `fluidRow` and it uses `box` as the main container for elements. Let's add in some of the functionality from the Shiny 101 post.


```
library(shiny)
library(shinydashboard)
ui <- dashboardPage(
  dashboardHeader(),
  dashboardSidebar(),
  dashboardBody(
    fluidRow(
      box(width = 8, plotOutput("plot1", height = 250)),
      box(width = 4,
          title = "Control",
          sliderInput("slider", "Observations", 
                      min = 1, max = 100, value = 50)
        
      )
    )
  )
)

server <- function(input, output){
  output$plot1 <- renderPlot({
    n <- input$slider
    hist(rnorm(n))
  })
}
shinyApp(ui, server)
```

In addition to `box` there is `infoBox` and `valueBox` for presenting particular forms of data to the user.

## Sidebar & Header

We can place a menu in the sidebar using the `sidebarMenu` function in the UI. Within `sidebar` we define `menuItems` each of which corresponds to a tabItem in the dash board body. Note that the icons are obtained from *Font Awesome*, which ships with Shiny.


```
library(shiny)
library(shinydashboard)
ui <- dashboardPage(
  dashboardHeader(),
  dashboardSidebar(
    sidebarMenu(
      menuItem("First", tabName = "first", icon = icon("dashboard")),
      menuItem("Second", tabName = "second", icon = icon("th"))
    )
  ),
  dashboardBody(
    tabItems(
      tabItem(tabName = "first", tags$h2("First content")),
      tabItem(tabName = "second", tags$h2("Second content"))
    )
  )
)

server <- function(input, output){
  output$plot1 <- renderPlot({
    n <- input$slider
    hist(rnorm(n))
  })
}
shinyApp(ui, server)
```

We will talk about Headers later.

## Leaflet

This enables you to work with maps a little more easily. Leafletjs is a javascript library for working with interactive maps and you can interface that library using the CRAN leaflet package. You use it like this:

```
library(leaflet)
leaflet() %>%
  addTiles() %>%
  setView(-93.65, 42.0285, zoom = 17) %>%
  addPopups(-93.65, 42.0285, 'Stats Dept')
```

You can use the leaflet output within a Shiny app by calling `leafletOutput("map")` from the UI code and then using `renderLeaflet()` passing it the code that is shown above.

27 mins

https://www.rstudio.com/resources/webinars/dynamic-dashboards-with-shiny/
















