# Assigment 5: Under (blood) pressure

## Instructions

Open this GitHub repository, which contains a starter file called `pressure.Rmd`, in RStudio.
This assignment will guide you through the process of building and analyzing linear regression models.

Read the [About the dataset](#about-the-dataset) section to get some background information on the dataset that you'll be working with.
Each of the below [exercises](#exercises) are to be completed in the provided spaces within your starter file `pressure.Rmd`.
Then, when you're ready to submit, follow the directions in the **[How to submit](#how-to-submit)** section below.

## About the dataset

The dataset we are using in this assignment comes from a study conducted by anthropologists to determine the long-term effects of a change in environment on blood pressure.

They measured the blood pressure of individuals who had migrated from a pre-modern environment high in the Andes mountains of Peru into the mainstream of Peruvian society at a much lower altitude.
The motivation for looking at blood pressure for this group of individuals comes from a prior study in Africa, which suggested that migration from a simpler society to a more technologically complex one might increase blood pressure at first, but that the blood pressure would tend to decrease back to normal over time.

The anthropologists recorded systolic blood pressure measurements as it is often a more sensitive indicator compared to diastolic measurements, as well as each participant's height, weight, and a number of other characteristics.
All these data are for males over 21 who were born at a high altitude and whose parents were born at a high altitude.

Using this collected data, we aim to answer the research question,

> All else kept equal, does the amount of time spent in an urban environment affect the blood pressure of individuals that recently migrated from a pre-modern society?

If we want to predict things based on what we see in the dataset and analyze the relative importance of different variables, we will need to build a model.
There are many types of models out there, and for this assignment we will focus on building a *linear regression model* for this dataset.

The dataset `blood_pressure` contains 9 variables and 39 rows,

| Variable  | Description             |
| --------  | ----------------------- |
| `Systol`  | systolic blood pressure |
| `Age`     | age                     |
| `Years`   | years in urban area     |
| `Weight`  | weight (kg)             |
| `Height`  | height (mm)             |
| `Chin`    | chin skinfold           |
| `Forearm` | forearm skinfold        |
| `Calf`    | calf skinfold           |
| `Pulse`   | resting pulse rate      |

## Exercises

**As always, make sure you run the setup code chunk at the top of the starter Rmd file before you begin. This will load the data.**

**Remember to commit your answers after every exercise.**

1.
    As always, we begin by creating visualizations to help familiarize ourselves with the dataset.
    Since we are interested in how the variable `Systol` is affected by the other variables in the dataset, let's use `facet_wrap()` to create a sequence of scatter plots that will show us which variables correlate with `Systol`.
    
    i. Copy the code chunk below to create the visualizations (make sure the individual graphs are legible - see the Hint box below for instructions on how to adjust the graph to an appropriate size),
    
      ```r
      blood_pressure %>%
        pivot_longer(cols = Age:Pulse, names_to = "measurement", values_to = "value") %>%
        ggplot() +
          geom_point(mapping = aes(x = value, y = Systol)) +
          facet_wrap(~ measurement, scales = "free_x")
      ```

    > #### Hint
    >
    > You can control the appearance of faceted graphs by customizing the scale of figures produced by a code block, i.e. like this:
    >
    >     ```{r, fig.asp = 1, fig.width = 8, out.width = "100%"}
    >     # Your code here
    >     ```
    
    ii. To identify trends, you will find if helpful to plot trend lines through the data. 
    
      We will do this by adding the `geom_smooth()` geom function to the end of the ggplot graph code from part (i). Copy the code from part (i) into a new code chunk and add the following code to the end (note the `+` symbol at the end of the preceding line):
       
      ```r
      ... + 
          geom_smooth(mapping = aes(x = value, y = Systol), method = "lm")
      ```
      
      These trend lines show a linear model fitted through each of the variables vs. `Systol`. This allows is to quickly see whether a relationship is positive or negative (or has no relationship, if there is a horizontal line).
      
      *Don't forget to add the chunk options to increase the figure size!*
      
      **What do the shaded grey regions mean?** These are the confidence intervals (or error bars) around our lines of best fit. 
      We typically want the error bars to have the same correlation as the most likely (central) trend line before we can say that a relationship is either positive or negative.
      If the central trend line is weakly positive, but the confidence interval is wide enough to include a negative correlation as well, then we should describe the trend as having no relationship.
    
    Commit your answers.
    
2.
    Using the visualization from Exercise 1, answer the following questions (make sure you've increased the size of the plots using the hint above):
    
      i. What type of correlation does the `Years` variable have with `Systol`: positive, negative, or no correlation?
      
      ii. Which variable shows a moderate to strong *positive* correlation with `Systol`? (Note: the strength of the relationship increases as the slope of the trend line increases.)
    
    Commit your answers.
    
    > #### Reminder
    >
    > The `Years` variable is the number of years that an individual has lived in an urban environment (not their age).
    
3. 
    A common preprocessing step in model building is to combine or transform some of the variables to create additional columns that may improve the accuracy of a model.

    Our research question asks whether the length of time spent living in an urban environment correlates with decreased blood pressure over time. 
    To figure this out, we could just look at `Years`, but it may be that any trends in the `Years` variable are confounded by the overall age of the individual.

    Use the `mutate()` function to create a new column, `urban_frac_life` by computing the ratio:
    
    ```r
    urban_frac_life = Years / Age
    ```

    Assign the resulting data frame with this new column to `blood_pressure_updated`.
    
    Commit your code.
    
    > #### Hint
    >
    > Recall that we assign to variables using the assignment operator `<-`, e.g. `x <- 2` or `x <- 5 * 20`.
    
4. 
    We're now ready to create our first linear regression model using the `lm()` function!
    Using the following code as a template, build a linear regression model of with `Systol` as the response variable (y) and `urban_frac_life` as the explanatory variable (x). You will need to fill in the ellipses `...` with the appropriate variables (make sure you get the response and explanatory variables the right way round!):
    
    ```r
    systol_urban_frac_model <- lm(... ~ ..., data = blood_pressure_updated)
    ```
    
    Commit your code that creates this model.
    
    > #### Hint
    >
    > The squigly symbol `~` is called a *tilde*. In mathematics it means "is related to".
    >
    > The response variable goes on the left side of the tilde `~` and the explanatory variable goes on the right side, e.g. `y ~ x` which means *y is related to x*. It is another way of writing the linear model formula `y = mx + c`, except we omit the constants `m` and `c`.

5.
    After building the model, use the following two convenience functions from the `broom` package to get a basic overview of the model.
    To get a data frame summarizing the model parameters, use the `tidy()` function,
    
    ```r
    systol_urban_frac_model %>%
      tidy()
    ```
    
    The values under the `estimate` column give us the intercept and slope of our linear model (remember *y=mx+b* ?).
    
    Additional information about the model, such as the model’s *R<sup>2</sup>* parameter, can be obtained using the `glance()` function:
    
    ```r
    systol_urban_frac_model %>%
      glance()
    ```
    
    **Warning:** The table created by the `glance` function may overflow the right hand margin of your knitted document. You can select just the first few columns of the table (as we are only currently interested in the first column, *R<sup>2</sup>*). You will lose points on assignments if you have overflowing graphs in your knitted PDFs.
    
    The *R<sup>2</sup>* value represents the proportion of variability in the response variable that is explained by the explanatory variable.
    If *R<sup>2</sup>* is close to 1, that means that the model is doing an excellent job capturing the variability of the response variable.
    If it is close to 0, then it is doing a poor job capturing the variability of the response variable.
    
    Commit your work.

    
6.
    After building a model, we would like to know what it predicts and how accurate the predictions are.
    
    One way to capture the accuracy of the predictions is to calculate the **residuals**, which tell us how far off any given prediction is from the true value.
    The `modelr` package, which is part of the `tidyverse`, provides us with functions for adding model predictions and residuals to our data frame.
    
    To do this for our model `Systol ~ urban_frac_life`, we pipe our dataset into two functions, `add_predictions()` and `add_residuals()`, which each take the model `systol_urban_frac_model` that we built earlier as input,
    
    ```r
    systol_urban_frac_df <- blood_pressure_updated %>%
      add_predictions(systol_urban_frac_model) %>%
      add_residuals(systol_urban_frac_model)
    ```
    
    Copy this code into your Rmd file and run it. Take a look at the `systol_urban_frac_df` dataframe that it creates, and answer the following questions:
    
      i. What is the name of the column that holds response (y) values predicted by the model?
      
      ii. What is the name of the column that holds the residuals for each observation?
      
      Commit your answers.

> How do we know if a linear regression model is reliable?
In general, three conditions must be met in order for a linear model built using `lm()` to be reliable:
>
> 1.  **Linearity:** The relationship between the explanatory variable and the response variable must be linear
>
> 2.  **Nearly normal residuals:** The residuals should be nearly normal (i.e. follow a bell curve shape)
>
> 3.  **Constant variability:** The variability of the points around the model line should be roughly constant
>
> Now that we've made our predictions and calculated the residuals, let's create some visualizations to investigate how well our model performs.
    
7. 
    Let's assess how well the model meets the assumption of linearity.
    
    First, let's plot the model fit on top of the `Systol` versus `urban_frac_life` scatter plot.
    To create this visualization, we will need the slope and intercept we printed out in Exercise 4, which should be filled in the template code below,
    
    ```r
    ggplot(systol_urban_frac_df) +
      geom_point(mapping = aes(x = ..., y = ...)) +
      geom_abline(slope = ..., intercept = ...)
    ```
    
    As before, you will need to fill in the ellipses `...` to create the plot. See the hint below for what to put in the `geom_abline` function (you should use code to extract the number from `systol_urban_frac_model`, **not** just copy the raw numbers that were reported by the `tidy` function).
    
    Based on your graph, explain whether your model meets the first condition (Linearity).
    
    Commit your answer.

    > #### Hint
    >
    > The `geom_abline` function should plot the slope and intercept of the model you have just created.
    >
    > In the Environment tab in RStudio (top right pane), look at the `systol_urban_frac_model` model by clicking the blue arrow next to the variable name. `systol_urban_frac_model` is an example of a *list*, a datatype in R that holds a list of items. The intercept and slope are stored in its `coefficients` item (**in that order**). 
    >
    > To extract the slope, you would run `systol_urban_frac_model$coefficients[2]`.
    >
    > Note how we use `$` to select the `coefficients` item from the list, and `[2]` to select the first number within `coefficients`.

8. 
    Let's use the graph from the last exercise to interpret the 3rd condition: constant variability.
    
    Examine the distibution of points above and below the line. Does it look like the variability is reasonably constant all the way along the line? What does this mean for the condition of constant variability?
    
    Commit your work.

9.  
    It is also important to inspect the distribution of the residuals to see what the prediction error is like.
    
    It is typical to visualize the residuals using a histogram to get a sense of their center, shape, and overall spread.
    Create a histogram of the `resid` column in `systol_urban_frac_df`, making sure you choose an appropriate bin width for the distribution (so that it is reasonably smooth), and use it to answer the following questions.
    
    i. What is the shape of the distribution of the residuals? Symmetric? Skewed?
    
    ii. This histogram can give us a basic idea of whether the second condition, **nearly normal residuals**, is violated. What does your histogram suggest about whether your model meets this condition, and why?
    
    Commit your work.

10.   
    Create and fit a second linear model for `Systol` using the `Weight` variable and name it `systol_weight_model`. Does this new model seem to predict `Systol` better than `urban_frac_life`?
    Determine this by comparing the *R<sup>2</sup>* values (obtained using `glance()`) for the two models.
    
    Commit your work.
    
11.
    Create a new data frame with residuals and predictions calculated using this new model (refer back to Exercise 6).
    
    Then, create:
    
    * a scatter plot of `Systol` vs `Weight`, showing the line of the `systol_weight_model`.
    
    * a histogram of the residuals.
    
    Use these graphs to interpret the 3 main assumptions of the linear model. Should we conclude that this new model is reliable (explain your reasoning for each assumption).
    
    Commit your work.
    
12.
    Finally, how does this second model compare with the first, both in terms of how well they explain the data (*R<sup>2</sup>*) and how well they meet the 3 assumptions of the linear model. Explain your answers with reference to your results from previous exercises.
    
    Commit your conclusions.


## How to submit

To submit your assignment, follow the two steps below.
Your homework will be graded for credit **after** you've completed both steps!

1.  Save, commit, and push your completed R Markdown file so that everything is synchronized to GitHub.
    If you do this right, then you will be able to view your completed file on the GitHub website.

2.  Knit your R Markdown document to the PDF format, export (download) the PDF file from RStudio Server, and then upload it to *Assignment 5* posting on Blackboard.

## Cheatsheets

You are encouraged to review and keep the following cheatsheets handy while working on this assignment:

*   [What graph should I make?][what-graph]

*   [Data wrangling cheatsheet (`dplyr` package)][dplyr-cheatsheet]

*   [Data visualization cheatsheet (`ggplot2` package)][ggplot2-cheatsheet]

*   [RStudio cheatsheet][rstudio-cheatsheet]

*   [R Markdown cheatsheet][rmarkdown-cheatsheet]

*   [R Markdown reference][rmarkdown-reference]

[what-graph]:           https://drive.google.com/file/d/1zsedQ9kHFtxhFE3R99PQ1a_yTTamJu3e/view?usp=sharing
[dplyr-cheatsheet]:     https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/ESQlogUDLfpNiXc3cD40crwBz0C0zfESw-6jRwTrHT4UBA?e=bQKhzS
[ggplot2-cheatsheet]:   https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/ESLxplzb1sdLszfqs3208G0BdScfSbNqrikzJ1pIKczsFw?e=cwYcjM
[rstudio-cheatsheet]:   https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/EVAQYYLorhxPh49NdlZV4KgBNNBQHRdJNthHK0ZuID8_Gw?e=dfzJPt
[rmarkdown-reference]:  https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/Ed4VQ0-6mEhBp2IkjIdGDK0BwaR9BDzEnpnVyyxDn_gasg?e=1eLHsa
[rmarkdown-cheatsheet]: https://gmuedu-my.sharepoint.com/:b:/g/personal/dwhite34_gmu_edu/ETKKUWqePhRJv-VvAOsg4F4BPte7yKfQJKyyr1gNMg46yQ?e=hJPHXV
