
<!-- README.md is generated from README.Rmd. Please edit that file -->

# vfinputs

<!-- badges: start -->

[![Travis build
status](https://travis-ci.org/rhenkin/vfinputs.svg?branch=main)](https://travis-ci.org/rhenkin/vfinputs)
<!-- badges: end -->

`vfinputs` provides **v**isual **f**ilter **inputs** for Shiny apps. The
current version includes color legends that enable users of Shiny apps
to interactively set numeric and categorical filters. Shiny programmers
can use the values returned by the inputs to coordinate filtering across
multiple outputs, using a unified color legend rather than those
rendered by a plotting package.

## Installation

You can install vfinputs with:

``` r
remotes::install_github("rhenkin/vfinputs")
```

## Usage

The inputs should be used as any other Shiny input, added in the UI (or
created using renderUI) and accessed in the server function. At the
moment, there are two numeric legends (continuous and discrete) and one
categorical one. The numeric legends include a brush interaction to
select a range of values. The categorical legend includes a
click-to-toggle interaction, with single or multiple selections being
possible.

``` r
library(vfinputs)

ui <- fluidPage(
  
   # Using minValue, maxValue and colors
   continuousColorFilter("filter", minValue = 0, maxValue = 200, colors = c("#ad2a2a", "#3f91e8")),
   
   # Using data and colors
   continuousColorFilter("filter2", data = mtcars$mpg, colors = c("#ad2a2a", "#3f91e8")),
   
   # Using data and palette
   continuousColorFilter("filter3", data = mtcars$mpg, palette = scales::viridis_pal())
  
   # Defining the number of colors through n for a discrete selection
   discreteColorFilter("filter4", minValue = 0, maxValue = 200, colors = c("#ad2a2a", "#3f91e8"), n = 5),
   
   # Categorical legend
   categoricalColorFilter("filter5", values = c("Yes", "No"), colors = c("#FF0000", "#FF00FF"))
   
   # Single selection
   categoricalColorFilter("filter5", values = c("Yes", "No"), colors = c("#FF0000", "#FF00FF"), multiple = FALSE)
 )
```

The numeric legends return `start` and `end` values of the brushed
interval. The categorical legend returns a list with the selected
values.

``` r
server <- function(input, output) {
   output$value <- output$selection <- renderPrint({
   if (!is.null(input$filter)) {
     paste0(input$filter$start, ",", input$filter$end)
     }
   })
 }
```

For detailed examples of how to use the different arguments see the
`visualtests` folder in this repository.

### Customizing and extending

**Ticks and labels**

Under the hood, the axes and brushes are implemented using
[D3.js](https://d3js.org). The `options` argument for the numeric scales
can contain custom `"format"` for the labels and `"ticks"` for deciding
which/how many ticks to show. For the supported format strings, please
see <https://github.com/d3/d3-format#locale_format>. Ticks can be a
number or a list with custom tick values to display.

**CSS**

The style of the legends, including axis and brush, can easily
customized with CSS, just like any other Shiny input. The key classes to
look for are `"div.continuous-color-filter"`,
`"div.discrete-color-filter"` and `"div.categorical-color-filter"`.
Additional elements can be found and customized using the browser
inspecting tool.

**Custom axis and interaction**

The key purpose of the UI functions is to connect the rendering of the
color legend with the D3-powered interaction. Thus, the package also
exports the base functions that creates the color legends ,
`numericLegend()` and `categoricalLegend()`, and which can be combined
with custom JavaScript code to re-implement the axes and brushes.
