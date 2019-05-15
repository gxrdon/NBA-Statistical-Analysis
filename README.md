# NBA-Statistical-Analysis


We chose to conduct an analysis on NBA statistics from the 2014/15 season. In this tutorial, we will tidy and parse our data so that we can further analyze it. After that, we will test our hypotheses. Finally, we will use machine learning to predict the MVP for this season and future successes in the NBA. 

To begin, we first need to get the data that we want to analyze. The dataframe that we will be pulling from can be found at https://www.kaggle.com/drgilermo/nba-players-stats-20142015/. The first step is to specify any libraries you may need and then get the path of the file that you want to use. For this case, the table is located under our "NBAStatisticalAnalysis" folder on my local C drive. Since the data we want to use is coming from an Excel Spreadsheet, we use the "readxl" library to read the excel file and create a table out of it. The "<-" syntax simply stores the resulting data as a variable so we now have NBAStats which is the table we will be working with for the remainder of this tutorial.

```{r setup}
library(readxl)
library(tidyverse)
path <- "C:/Users/Andrew/Documents/NBAStatisticalAnalysis/players_stats.xlsx"
NBAStats <- read_excel(path)
```

Now that we have the table that we need to analyze, it's time to tidy it. This means that we are going to remove anything from the table that might skew our results. For example, if a player's position is not listed, this could be trouble since we are going to analyze things such as rebounds based on position, shooting percentage based on position, etc. 

We can clean out table by simply looping through it and using conditional statements to create a new table. A conditional statement refers to a querry that checks if the current entity fulfills a certain trait. For example, if the current player's position is NA, meaning that it is not listed, do nothing. Otherwise, add them to the new table. This will be useful later on in the project when we further analyze our data.

The "%>%" is an operation that allows the user to send a dataset into the first parameter of the next function. For example, imagine if you had a function add() that takes a dataframe and an integer. You can either do add(dataframe, integer) or you can do dataframe %>% add(integer) which will have the same effect. In the long run, using dplyr pipelines (%>%) will save a lot of space and confusion. 

```{r}

111
