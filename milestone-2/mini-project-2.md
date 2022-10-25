Mini Data Analysis Milestone 2
================

*To complete this milestone, you can edit [this `.rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are commented out with
`<!--- start your work here--->`. When you are done, make sure to knit
to an `.md` file by changing the output in the YAML header to
`github_document`, before submitting a tagged release on canvas.*

# Welcome to your second (and last) milestone in your mini data analysis project!

In Milestone 1, you explored your data, came up with research questions,
and obtained some results by making summary tables and graphs. This
time, we will first explore more in depth the concept of *tidy data.*
Then, you’ll be sharpening some of the results you obtained from your
previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

# Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 55 points (compared to the 45 points
of the Milestone 1): 45 for your analysis, and 10 for your entire
mini-analysis GitHub repository. Details follow.

**Research Questions**: In Milestone 1, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Learning Objectives

By the end of this milestone, you should:

-   Understand what *tidy* data is, and how to create it using `tidyr`.
-   Generate a reproducible and clear report using R Markdown.
-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
library(tidyverse)
```

# Task 1: Tidy your data (15 points)

In this task, we will do several exercises to reshape our data. The goal
here is to understand how to do this reshaping with the `tidyr` package.

A reminder of the definition of *tidy* data:

-   Each row is an **observation**
-   Each column is a **variable**
-   Each cell is a **value**

*Tidy’ing* data is sometimes necessary because it can simplify
computation. Other times it can be nice to organize data so that it can
be easier to understand when read manually.

### 2.1 (2.5 points)

Based on the definition above, can you identify if your data is tidy or
untidy? Go through all your columns, or if you have \\\>8 variables,
just pick 8, and explain whether the data is untidy or tidy.

<!--------------------------- Start your work below --------------------------->

#### Answer

``` r
# Load my data
data(steam_games)

# Take a look on the dataset structure
glimpse(steam_games)
```

    ## Rows: 40,833
    ## Columns: 21
    ## $ id                       <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14…
    ## $ url                      <chr> "https://store.steampowered.com/app/379720/DO…
    ## $ types                    <chr> "app", "app", "app", "app", "app", "bundle", …
    ## $ name                     <chr> "DOOM", "PLAYERUNKNOWN'S BATTLEGROUNDS", "BAT…
    ## $ desc_snippet             <chr> "Now includes all three premium DLC packs (Un…
    ## $ recent_reviews           <chr> "Very Positive,(554),- 89% of the 554 user re…
    ## $ all_reviews              <chr> "Very Positive,(42,550),- 92% of the 42,550 u…
    ## $ release_date             <chr> "May 12, 2016", "Dec 21, 2017", "Apr 24, 2018…
    ## $ developer                <chr> "id Software", "PUBG Corporation", "Harebrain…
    ## $ publisher                <chr> "Bethesda Softworks,Bethesda Softworks", "PUB…
    ## $ popular_tags             <chr> "FPS,Gore,Action,Demons,Shooter,First-Person,…
    ## $ game_details             <chr> "Single-player,Multi-player,Co-op,Steam Achie…
    ## $ languages                <chr> "English,French,Italian,German,Spanish - Spai…
    ## $ achievements             <dbl> 54, 37, 128, NA, NA, NA, 51, 55, 34, 43, 72, …
    ## $ genre                    <chr> "Action", "Action,Adventure,Massively Multipl…
    ## $ game_description         <chr> "About This Game Developed by id software, th…
    ## $ mature_content           <chr> NA, "Mature Content Description  The develope…
    ## $ minimum_requirements     <chr> "Minimum:,OS:,Windows 7/8.1/10 (64-bit versio…
    ## $ recommended_requirements <chr> "Recommended:,OS:,Windows 7/8.1/10 (64-bit ve…
    ## $ original_price           <dbl> 19.99, 29.99, 39.99, 44.99, 0.00, NA, 59.99, …
    ## $ discount_price           <dbl> 14.99, NA, NA, NA, NA, 35.18, 70.42, 17.58, N…

Since my dataset has 21 columns, I will analyze the first 8 columns.
Namely: `id`, `url`, `types`, `name`, `desc_snippet`, `recent_reviews`,
`all_reviews` and `release_date`.

Overall, the data is tidy. Each row is an observation (game), each
column is a variable (game’s id, game’s url, game’s type, etc) and each
cell is a value. BUT a few columns could be even tidier. For instance,
the first value of `all_reviews` reads as “Very Positive,(42,550),- 92%
of the 42,550 user reviews for this game are positive.”. It contains a
single value (namely a text summary of all reviews) but could be broken
down into many variables to make the analysis easier. For example, it
could be broken into `all_reviews_status` (“Very Positive”),
`all_reviews_count` (42555), `all_reviews_positive_frequency` (0.92).

<!----------------------------------------------------------------------------->

### 2.2 (5 points)

Now, if your data is tidy, untidy it! Then, tidy it back to it’s
original state.

If your data is untidy, then tidy it! Then, untidy it back to it’s
original state.

Be sure to explain your reasoning for this task. Show us the “before”
and “after”.

<!--------------------------- Start your work below --------------------------->

``` r
# Load more packages
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

#### Before tidying

``` r
# Take a look on the dataset structure before tidying it
glimpse(steam_games)
```

    ## Rows: 40,833
    ## Columns: 21
    ## $ id                       <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14…
    ## $ url                      <chr> "https://store.steampowered.com/app/379720/DO…
    ## $ types                    <chr> "app", "app", "app", "app", "app", "bundle", …
    ## $ name                     <chr> "DOOM", "PLAYERUNKNOWN'S BATTLEGROUNDS", "BAT…
    ## $ desc_snippet             <chr> "Now includes all three premium DLC packs (Un…
    ## $ recent_reviews           <chr> "Very Positive,(554),- 89% of the 554 user re…
    ## $ all_reviews              <chr> "Very Positive,(42,550),- 92% of the 42,550 u…
    ## $ release_date             <chr> "May 12, 2016", "Dec 21, 2017", "Apr 24, 2018…
    ## $ developer                <chr> "id Software", "PUBG Corporation", "Harebrain…
    ## $ publisher                <chr> "Bethesda Softworks,Bethesda Softworks", "PUB…
    ## $ popular_tags             <chr> "FPS,Gore,Action,Demons,Shooter,First-Person,…
    ## $ game_details             <chr> "Single-player,Multi-player,Co-op,Steam Achie…
    ## $ languages                <chr> "English,French,Italian,German,Spanish - Spai…
    ## $ achievements             <dbl> 54, 37, 128, NA, NA, NA, 51, 55, 34, 43, 72, …
    ## $ genre                    <chr> "Action", "Action,Adventure,Massively Multipl…
    ## $ game_description         <chr> "About This Game Developed by id software, th…
    ## $ mature_content           <chr> NA, "Mature Content Description  The develope…
    ## $ minimum_requirements     <chr> "Minimum:,OS:,Windows 7/8.1/10 (64-bit versio…
    ## $ recommended_requirements <chr> "Recommended:,OS:,Windows 7/8.1/10 (64-bit ve…
    ## $ original_price           <dbl> 19.99, 29.99, 39.99, 44.99, 0.00, NA, 59.99, …
    ## $ discount_price           <dbl> 14.99, NA, NA, NA, NA, 35.18, 70.42, 17.58, N…

#### After tidying

``` r
# Utility function
parse_all_reviews <- function(
    all_reviews
) {
    all_reviews_template <- "^([\\w ]+),\\(([\\d,]+)\\),- (\\d+)% of the [\\d,]+ user reviews for this game are positive.$"

    matches <- str_match_all(all_reviews, all_reviews_template)
    matches <- as.vector(matches[[1]])

    all_reviews_status <- matches[2]

    all_reviews_count <-
        matches[3] |>
        str_remove_all(",") |>
        as.numeric()

    all_reviews_positive_frequency <-
        matches[4] |>
        as.numeric() |>
        magrittr::divide_by(100)

    results <- list(
        all_reviews_status = all_reviews_status,
        all_reviews_count = all_reviews_count,
        all_reviews_positive_frequency = all_reviews_positive_frequency
    )

    return(results)
}

# Utility function
parse_recent_reviews <- function(
    recent_reviews
) {
    recent_reviews_template <- "^([\\w ]+),\\(([\\d,]+)\\),- (\\d+)% of the [\\d,]+ user reviews in the last 30 days are positive\\.$"

    matches <- str_match_all(recent_reviews, recent_reviews_template)
    matches <- as.vector(matches[[1]])

    recent_reviews_status <- matches[2]

    recent_reviews_count <-
        matches[3] |>
        str_remove_all(",") |>
        as.numeric()

    recent_reviews_positive_frequency <-
        matches[4] |>
        as.numeric() |>
        magrittr::divide_by(100)

    results <- list(
        recent_reviews_status = recent_reviews_status,
        recent_reviews_count = recent_reviews_count,
        recent_reviews_positive_frequency = recent_reviews_positive_frequency
    )

    return(results)
}

# Parse `all_reviews` into `all_reviews_status`, `all_reviews_count` and `all_reviews_positive_frequency`
# Parse `recent_reviews` into `recent_reviews_status`, `recent_reviews_count` and `recent_reviews_positive_frequency`
tidy_steam_games <-
    steam_games |>
    rowwise() |>
    mutate(all_reviews_parsing = list(parse_all_reviews(all_reviews))) |>
    mutate(recent_reviews_parsing = list(parse_recent_reviews(recent_reviews))) |>
    unnest_wider(c(all_reviews_parsing, recent_reviews_parsing)) |>
    select(
        - all_reviews,
        - recent_reviews
    )

# Parse `release_date` from string to date
tidy_steam_games <-
    tidy_steam_games |>
    mutate(release_date = mdy(release_date))
```

    ## Warning: 4418 failed to parse.

``` r
# Take a look on the dataset structure after tidying it
tidy_steam_games |>
    glimpse()
```

    ## Rows: 40,833
    ## Columns: 25
    ## $ id                                <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 1…
    ## $ url                               <chr> "https://store.steampowered.com/app/…
    ## $ types                             <chr> "app", "app", "app", "app", "app", "…
    ## $ name                              <chr> "DOOM", "PLAYERUNKNOWN'S BATTLEGROUN…
    ## $ desc_snippet                      <chr> "Now includes all three premium DLC …
    ## $ release_date                      <date> 2016-05-12, 2017-12-21, 2018-04-24,…
    ## $ developer                         <chr> "id Software", "PUBG Corporation", "…
    ## $ publisher                         <chr> "Bethesda Softworks,Bethesda Softwor…
    ## $ popular_tags                      <chr> "FPS,Gore,Action,Demons,Shooter,Firs…
    ## $ game_details                      <chr> "Single-player,Multi-player,Co-op,St…
    ## $ languages                         <chr> "English,French,Italian,German,Spani…
    ## $ achievements                      <dbl> 54, 37, 128, NA, NA, NA, 51, 55, 34,…
    ## $ genre                             <chr> "Action", "Action,Adventure,Massivel…
    ## $ game_description                  <chr> "About This Game Developed by id sof…
    ## $ mature_content                    <chr> NA, "Mature Content Description  The…
    ## $ minimum_requirements              <chr> "Minimum:,OS:,Windows 7/8.1/10 (64-b…
    ## $ recommended_requirements          <chr> "Recommended:,OS:,Windows 7/8.1/10 (…
    ## $ original_price                    <dbl> 19.99, 29.99, 39.99, 44.99, 0.00, NA…
    ## $ discount_price                    <dbl> 14.99, NA, NA, NA, NA, 35.18, 70.42,…
    ## $ all_reviews_status                <chr> "Very Positive", "Mixed", "Mostly Po…
    ## $ all_reviews_count                 <dbl> 42550, 836608, 7030, 167115, 11481, …
    ## $ all_reviews_positive_frequency    <dbl> 0.92, 0.49, 0.71, 0.61, 0.74, NA, 0.…
    ## $ recent_reviews_status             <chr> "Very Positive", "Mixed", "Mixed", "…
    ## $ recent_reviews_count              <dbl> 554, 6214, 166, 932, 287, NA, 408, 6…
    ## $ recent_reviews_positive_frequency <dbl> 0.89, 0.49, 0.54, 0.57, 0.54, NA, 0.…

Do note that the other “string” columns could be parsed into new
variables but it is not so clear how to do so. For instance, there are
378 unique popular tags in the `popular_tags` column. I don’t think it
would be helpful for the analysis to create 378 columns (one for each
tag).

#### After untidying the tidied data

``` r
# Deparse `all_reviews` from `all_reviews_status`, `all_reviews_count` and `all_reviews_positive_frequency`
# Deparse `recent_reviews` from `recent_reviews_status`, `recent_reviews_count` and `recent_reviews_positive_frequency`
untidy_steam_games <-
    tidy_steam_games |>
    mutate(all_reviews = str_glue("{all_reviews_status},({all_reviews_count}),- {all_reviews_positive_frequency * 100}% of the {all_reviews_count} user reviews for this game are positive.")) |>
    mutate(recent_reviews = str_glue("{recent_reviews_status},({recent_reviews_count}),- {recent_reviews_positive_frequency * 100}% of the {recent_reviews_count} user reviews in the last 30 days are positive\\.$")) |>
    select(
        - all_reviews_status,
        - all_reviews_count,
        - all_reviews_positive_frequency,
        - recent_reviews_status,
        - recent_reviews_count,
        - recent_reviews_positive_frequency
    )

# Convert `release_date` back to string from date
untidy_steam_games <-
    untidy_steam_games |>
    mutate(release_date = str_glue("{month(release_date, label = TRUE)} {day(release_date)}, year(release_date)}"))


# Take a look on the dataset structure after untidying it
untidy_steam_games |>
    glimpse()
```

    ## Rows: 40,833
    ## Columns: 21
    ## $ id                       <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14…
    ## $ url                      <chr> "https://store.steampowered.com/app/379720/DO…
    ## $ types                    <chr> "app", "app", "app", "app", "app", "bundle", …
    ## $ name                     <chr> "DOOM", "PLAYERUNKNOWN'S BATTLEGROUNDS", "BAT…
    ## $ desc_snippet             <chr> "Now includes all three premium DLC packs (Un…
    ## $ release_date             <glue> "May 12, year(release_date)}", "Dec 21, year…
    ## $ developer                <chr> "id Software", "PUBG Corporation", "Harebrain…
    ## $ publisher                <chr> "Bethesda Softworks,Bethesda Softworks", "PUB…
    ## $ popular_tags             <chr> "FPS,Gore,Action,Demons,Shooter,First-Person,…
    ## $ game_details             <chr> "Single-player,Multi-player,Co-op,Steam Achie…
    ## $ languages                <chr> "English,French,Italian,German,Spanish - Spai…
    ## $ achievements             <dbl> 54, 37, 128, NA, NA, NA, 51, 55, 34, 43, 72, …
    ## $ genre                    <chr> "Action", "Action,Adventure,Massively Multipl…
    ## $ game_description         <chr> "About This Game Developed by id software, th…
    ## $ mature_content           <chr> NA, "Mature Content Description  The develope…
    ## $ minimum_requirements     <chr> "Minimum:,OS:,Windows 7/8.1/10 (64-bit versio…
    ## $ recommended_requirements <chr> "Recommended:,OS:,Windows 7/8.1/10 (64-bit ve…
    ## $ original_price           <dbl> 19.99, 29.99, 39.99, 44.99, 0.00, NA, 59.99, …
    ## $ discount_price           <dbl> 14.99, NA, NA, NA, NA, 35.18, 70.42, 17.58, N…
    ## $ all_reviews              <glue> "Very Positive,(42550),- 92% of the 42550 us…
    ## $ recent_reviews           <glue> "Very Positive,(554),- 89% of the 554 user r…

<!----------------------------------------------------------------------------->

### 2.3 (7.5 points)

Now, you should be more familiar with your data, and also have made
progress in answering your research questions. Based on your interest,
and your analyses, pick 2 of the 4 research questions to continue your
analysis in the next four tasks:

<!-------------------------- Start your work below ---------------------------->

1.  How is price associated with positive review frequency?
2.  How is release date associated with positive review frequency?

<!----------------------------------------------------------------------------->

Explain your decision for choosing the above two research questions.

<!--------------------------- Start your work below --------------------------->

I think positive review frequency is a good metric for game quality
(number of reviews would simply measure game popularity). Moreover, I
expect release date and price to be very impactful covariates. The
questions that I chose relate these three important variables and should
help to better understand the data.

<!----------------------------------------------------------------------------->

Now, try to choose a version of your data that you think will be
appropriate to answer these 2 questions. Use between 4 and 8 functions
that we’ve covered so far (i.e. by filtering, cleaning, tidy’ing,
dropping irrelevant columns, etc.).

<!--------------------------- Start your work below --------------------------->

``` r
# Parse `all_reviews` into `all_reviews_status`, `all_reviews_count` and `all_reviews_positive_frequency`
# Parse `recent_reviews` into `recent_reviews_status`, `recent_reviews_count` and `recent_reviews_positive_frequency`
analysis_steam_games <-
    steam_games |>
    rowwise() |>
    mutate(all_reviews_parsing = list(parse_all_reviews(all_reviews))) |>
    mutate(recent_reviews_parsing = list(parse_recent_reviews(recent_reviews))) |>
    unnest_wider(c(all_reviews_parsing, recent_reviews_parsing)) |>
    select(
        - all_reviews,
        - recent_reviews
    )

# Parse `release_date` from string to date
analysis_steam_games <-
    analysis_steam_games |>
    mutate(release_date = mdy(release_date))
```

    ## Warning: 4418 failed to parse.

``` r
# Select only the necessary variables for the analysis
analysis_steam_games <-
    analysis_steam_games |>
    select(
        name,
        release_date,
        original_price,
        all_reviews_positive_frequency,
        all_reviews_status
    )

# Remove rows with NA
analysis_steam_games <-
    analysis_steam_games |>
    drop_na()
```

Do note that defined functions such as `parse_all_reviews` use many of
the functions covared in the course.

<!----------------------------------------------------------------------------->

# Task 2: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

``` r
# Plot original price by positive review frequency (as status)
# Original price is in the log scale given the data covers multiple orders of magnitude
analysis_steam_games |>
    filter(0 < original_price, original_price <= 1000) |>  # Remove free games and very expensive games
    ggplot(aes(x = all_reviews_status, y = original_price)) +
    geom_boxplot() +
    scale_y_log10(labels = scales::label_dollar()) +
    coord_flip() +
    theme_bw(20) +
    labs(
        x = "Positive review frequency (as status)",
        y = "Original price"
    )
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)

        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.

    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).

        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 1

``` r
# Manually define a new level ordering
review_status_levels <- c(
    "Overwhelmingly Negative",
    "Very Negative",
    "Negative",
    "Mostly Negative",
    "Mixed",
    "Mostly Positive",
    "Positive",
    "Very Positive",
    "Overwhelmingly Positive"
)

# Plot original price by positive review frequency (as status)
# Original price is in the log scale given the data covers multiple orders of magnitude
# Order by manual ordering and then reverse so that negatives stays on the top and positives on the bottom of the plot
analysis_steam_games |>
    filter(0 < original_price, original_price <= 1000) |>  # Remove free games and very expensive games
    mutate(all_reviews_status = fct_rev(factor(all_reviews_status, review_status_levels))) |>  # Change ordering
    ggplot(aes(x = all_reviews_status, y = original_price)) +
    geom_boxplot() +
    scale_y_log10(labels = scales::label_dollar()) +
    coord_flip() +
    theme_bw(20) +
    labs(
        x = "Positive review frequency (as status)",
        y = "Original price"
    )
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

Explanation: I chose to go with a manual ordering for
`all_reviews_status` because it seemed the intuitive ordering for the
data. I could have infered the ordering from
`all_reviews_positive_frequency` by using
`fct_reorder(all_reviews_status, all_reviews_positive_frequency)` as I
suspect that `all_reviews_status` is a function of
`all_reviews_positive_frequency` in the original data but this solution
would be more fragile (some testing that I did placed “Positive” after
“Very positive” for instance). Finally, I reversed the order so that the
negative levels stay on the top and positive levels stay on the bottom
of the plot.

<!----------------------------------------------------------------------------->
<!-------------------------- Start your work below ---------------------------->

**Task Number**: 3

``` r
analysis_steam_games <-
    analysis_steam_games |>
    mutate(release_date_week_day = wday(release_date))
```

Do note that `release_date` has already been parsed to a date column in
section 2.2.

Explanation: Investigating the day of the week of the release date might
be insightful because people have more free time on the weekend to play
games.

<!----------------------------------------------------------------------------->

# Task 3: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: How is price associated with positive review
frequency?

**Variable of interest**: `all_reviews_positive_frequency`

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.

    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

``` r
model <- lm(all_reviews_positive_frequency ~ original_price, data = analysis_steam_games)

print(summary(model))
```

    ## 
    ## Call:
    ## lm(formula = all_reviews_positive_frequency ~ original_price, 
    ##     data = analysis_steam_games)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.74696 -0.10693  0.04306  0.14307  0.25307 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    7.469e-01  1.440e-03 518.853   <2e-16 ***
    ## original_price 2.659e-07  2.848e-07   0.934     0.35    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.1853 on 16564 degrees of freedom
    ## Multiple R-squared:  5.263e-05,  Adjusted R-squared:  -7.738e-06 
    ## F-statistic: 0.8718 on 1 and 16564 DF,  p-value: 0.3505

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

``` r
broom::tidy(model)
```

    ## # A tibble: 2 × 5
    ##   term              estimate   std.error statistic p.value
    ##   <chr>                <dbl>       <dbl>     <dbl>   <dbl>
    ## 1 (Intercept)    0.747       0.00144       519.      0    
    ## 2 original_price 0.000000266 0.000000285     0.934   0.350

Here, I’m calculating the p-value for the variable `original_price`. It
is available at “row `original_price`” (second row) and “column
`p.value`” (last column).

<!----------------------------------------------------------------------------->

# Task 4: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 1 (Task 4.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

``` r
steam_games |>
    mutate(original_price_category = case_when(
        original_price == 0 ~ "free",
        0 < original_price & original_price <= 1 ~ "super cheap",
        1 < original_price & original_price <= 10 ~ "cheap",
        10 < original_price & original_price <= 100 ~ "normal",
        100 < original_price ~ "expensive"
    )) |>
    group_by(original_price_category) |>
    summarise(
        range_min = min(discount_price, na.rm = TRUE),
        range_max = max(discount_price, na.rm = TRUE),
        mean = mean(discount_price, na.rm = TRUE),
        median = median(discount_price, na.rm = TRUE),
        sd = sd(discount_price, na.rm = TRUE)
    ) |>
    write_csv(here::here("output/discout_price_by_original_price_category.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Task 3 to an R binary file (an RDS), and
load it again. Be sure to save the binary file in your `output` folder.
Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

``` r
model |>
    saveRDS(here::here("output/model.rds"))

read_model <- readRDS(here::here("output/model.rds"))
```

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Task 4 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 1 document knits error-free, and the Milestone 2 document
knits error-free.

## Tagged release (1 point)

You’ve tagged a release for Milestone 1, and you’ve tagged a release for
Milestone 2.

### Attribution

Thanks to Victor Yuan for mostly putting this together.
