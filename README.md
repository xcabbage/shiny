# shiny
# new project / new dir / new shiny web appl
# daroczig@rapporter.net
library(shiny)
library(ggplot2)

--UI

# This is the user-interface definition of a Shiny web application.
# You can find out more about building applications with Shiny here:
#
# http://shiny.rstudio.com
# http://www.htmlwidgets.org/

shinyUI(fluidPage(

  # Application title
  titlePanel("Old Faithful Geyser Data"),

  # Sidebar with a slider input for number of bins
  sidebarLayout(
    sidebarPanel(
      sliderInput('exp',
                  'lab exp:',
                  min = 1,
                  max = 16,
                  value = 2),
    selectInput('x', 'lab x', choices = names(mtcars)),
    selectInput('y', 'lab y', choices = names(mtcars), selected = 'hp'),
    selectInput('c', 'lab c', choices = c('am','gear','carb'))
    ),
    # Show a plot of the generated distribution
    mainPanel(
      tabsetPanel(
        tabPanel('Plot1', plotOutput("distPlot")),
        tabPanel('Plot2', plotOutput("ggplot")),
        tabPanel('Coeff1', verbatimTextOutput('model1')),
        tabPanel('Coeff2', htmlOutput('model2'))
      )
    )
  )
))

---- server


# This is the server logic for a Shiny web application.
# You can find out more about building applications with Shiny here:
#
# http://shiny.rstudio.com
#
# daroczig@rapporter.net

shinyServer(function(input, output) {

  output$distPlot <- renderPlot({

    plot(mtcars[, input$x], mtcars[, input$y], col = mtcars[, input$c]+1)
    fit <- lm(mtcars[, input$y] ~ poly(mtcars[, input$x], degree = input$exp))
    points(mtcars[, input$x],predict(fit), pch = 19, col = 'orange')
    abline(fit)

  })
  
  # input x is a string, use aes_string
  output$ggplot <- renderPlot({
    mtcars[, input$c]<-as.factor(mtcars[, input$c])
    ggplot(mtcars, aes_string(x=input$x, y=input$y, color = input$c)) + geom_point()+
      geom_smooth(method='lm', 
                  se = TRUE, 
                  formula = y ~ poly(x,input$exp))
  })

  output$model1 <- renderPrint({
    fit <- lm(mtcars[, input$y] ~ mtcars[, input$x])
    # ?reactive -- reuse dif parts
    summary(fit)
  })

  output$model2 <- renderTable({
    fit <- lm(mtcars[, input$y] ~ poly(mtcars[, input$x], degree = input$exp))
    # ?reactive -- reuse dif parts
    summary(fit)$coeff
  })  

})


