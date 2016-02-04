# shiny
--UI

library(shiny)

shinyUI(fluidPage(

  # Application title
  titlePanel("Old Faithful Geyser Data"),

  # Sidebar with a slider input for number of bins
  sidebarLayout(
    sidebarPanel(
      sliderInput("bins",
                  "Number of bins:",
                  min = 1,
                  max = 50,
                  value = 30),
    selectInput('x', 'lab x', choices = names(mtcars)),
    selectInput('y', 'lab y', choices = names(mtcars))                
    ),
    # Show a plot of the generated distribution
    mainPanel(
      plotOutput("distPlot")
    )
  )
))


---- server

library(shiny)

shinyServer(function(input, output) {

  output$distPlot <- renderPlot({
    plot(mtcars[, input$x], mtcars[, input$y])

  })

})
