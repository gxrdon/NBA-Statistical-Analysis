# NBA-Statistical-Analysis


We chose to conduct an analysis on NBA statistics from the 2014/15 season. In this tutorial, we will tidy and parse our data so that we can further analyze it. After that, we will test our hypotheses. Finally, we will use machine learning to predict the MVP for this season and future successes in the NBA. 

To begin, we first need to get the data that we want to analyze. The dataframe that we will be pulling from can be found at https://www.kaggle.com/drgilermo/nba-players-stats-20142015/. The first step is to specify any libraries you may need and then get the path of the file that you want to use. For this case, the table is located under our "NBAStatisticalAnalysis" folder. After that, we use dbConnect to create a path and then use dbListTables to collect the table(s) that were created from the specified file. 

```{r setup}
library(RSQLite)
library(tidyverse)
data_path <- "C:/Users/Andrew/Documents/NBAStatisticalAnalysis/players_stats"
db <- DBI::dbConnect(RSQLite::SQLite(), data_path)
alltables = dbListTables(db)
```


The "%>%" is an operation that allows the user to send a dataset into the first parameter of the next function. For example, imagine if you had a function add() that takes a dataframe and an integer. You can either do add(dataframe, integer) or you can do dataframe %>% add(integer) which will have the same effect. In the long run, using dplyr pipelines (%>%) will save a lot of space and confusion. 
