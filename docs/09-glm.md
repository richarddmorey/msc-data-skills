
# Introduction to GLM {#glm}

## Learning Objectives

### Basic

1. Prove to yourself the correspondence among two-sample t-test, one-way ANOVA, and linear regression with dummy coding
2. Given data and a GLM, generate a decomposition matrix and calculate sums of squares, mean squares, and F ratios

## Resources

* [Jeff Miller and Patricia Haden, Statistical Analysis with the Linear Model (free online textbook)](http://www.otago.ac.nz/psychology/otago039309.pdf)
* [lecture slides introducing the General Linear Model](slides/08_glm_slides.pdf)
* [GLM shiny app](http://rstudio2.psy.gla.ac.uk/Dale/GLM)
* [F distribution](http://rstudio2.psy.gla.ac.uk/Dale/fdist)


## GLM

In the code block below, you are given the `two_sample()` function which you will use to generate random datasets.


```r
# libraries needed for these examples
library(tidyverse)

two_sample <- function(diff = 0.5, n_per_group = 20) {
  tibble(Y = c(rnorm(n_per_group, -.5 * diff, sd = 1),
               rnorm(n_per_group, .5 * diff, sd = 1)),
         grp = factor(rep(c("a", "b"), each = n_per_group)),
         grp_d = rep(c(0, 1), each = n_per_group))
}
```

## Relationship between t-test, ANOVA, and linear regression

Generate a single random dataset using `two_sample()`.  Then compare and contrast the results from:

1. A t-test (with `var.equal = TRUE`)
2. An ANOVA (using `aov()`)
3. Linear regression (using `lm()`) on the data


## Understanding decomposition matrices

Now use `two_sample()` to create a dataset `dat` with N=5 per group.  Then use the estimation equations for a one-factor ANOVA to calculate the model components `mu_hat` ($\hat{\mu}$), and `a_hat` ($\hat{A_i}$).  'mu_hat" should be a single value and `a_hat` should be a table with two rows and columns `grp` and `a` (the estimated effect for that group).

    <div class="solution"><button>Hint</button>
    
    ```r
    mu_hat <- dat %>%
      summarise(????) %>%
      pull(????)
    
    a_hat <- dat %>%
      group_by(???) %>% 
      summarise(????)
    ```
    </div>

Calculate residuals (`err` or $\widehat{S(A)_{ij}}$) and generate a *decomposition matrix* of `Y` in `dat`, such as seen slides 10-12 from the lecture.  (Despite being called a matrix, your decomposition matrix should be a `tibble` not a matrix, and you don't need to include the last line with SS yet.) 

    <div class="solution"><button>Hint</button>
    It might be easier to calculate the residuals *after* you get everything else in the table.
    </div>

    <div class="solution"><button>Another Hint</button>
    Use `select()` on dat to get `Y` and `grp`, and `inner_join()` to add in the $A_i$s.
    </div>

    <div class="solution"><button>Yet Another Hint</button>
    
    ```r
    decomp <- dat %>% 
      select(Y, grp) %>%
      mutate(mu = mu_hat) %>%
      inner_join(a_hat, "grp") # %>% ...etc
    ```
    </div>

Calculate sums of squares for `Y`, `a`, and `err`.  The resulting table should be called `all_ss` with columns `SS_Y`, `SS_a`, and `SS_err` respectively.

    <div class="solution"><button>Hint</button>
    Use the `summarise()` function.  You can declare more than one summary values as separate arguments to the function.
    </div>

Divide each sum of squares by its corresponding df to calculate mean squares.  The calculate an F-ratio, and get the p-value using the `pf()` function.

    <div class="solution"><button>Hint</button>
    Use `lower.tail = FALSE` with `pf()`.  See `?pf`
    </div>

Now run a one-way ANOVA on your results and compare it to what you obtained in your calculations.


## Exercises

Download the [exercises](exercises/09_glm_exercise.Rmd). See the [answers](exercises/09_glm_solution.Rmd) only after you've attempted all the questions.
