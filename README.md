# RTrainingManual

---
## Steps for Processing and Visualising Data

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

```{r load required libraries for Rmarkdown, include=FALSE}
library(knitr) # Nice tables
library(kableExtra) # Extra nice tables
```

```{r functions, include=FALSE}
prettyTable <- function(table){
  
  # Use the kable function to create a nicer formatted table
  kable(table) %>%
  
    # Set the format
    kable_styling(bootstrap_options="striped", # Set the colour of rows
                  full_width=FALSE, # Make the table not stretch to fit the page
                  position="left") %>% # Position the table on the left
  
    # Make the table scrollable
    scroll_box(height = "400px")
}
```

## Introduction
In this tutorial we'll be learning about the steps involved in cleaning, processing and visualising our data. These steps are also important so we can spot any problems with the data early and limit their impact on our analyses. In this tutorial we'll explore common steps to import, process and visulaise data in R. 

We'll be using the `mtcars` dataset in the current tutorial, this is a dataset that comes with every R installation. You can read more about these data [here](https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/mtcars.html).

The current tutorial was created using Rmarkdown - a powerful tool to help us create formatted using R code. If you're intersted in using Rmarkdown, there is a great tutorial [here](https://rmarkdown.rstudio.com/lesson-1.html).

## Loading required libraries

The power of R is that millions of people are using it. R comes with a large set of `base` functions that we'll be using. Sometimes though it is useful to using some additional libraries. When storing data I would always recommend storing them as `csv` formatted files. However, it is useful to be able to work with excel formatted files directly. We can load install libraries with the following code to work with excel formatted files:

```{r installing libraries, eval=FALSE}
install.packages("openxlsx") # We'll use this package to handle excel formatted spreadsheets
```

Once installed, load them with the following code:

```{r load libraries, message=FALSE}
# Load the openxlsx library
library(openxlsx)
```

## Setting the working directory

Next, we'll set the working directory - usually the folder on our computer where we have stored our data. We can use the following code for this:

```{r set working directory}

# Set the working directory
setwd("/Users/hugopigott/Dropbox/2. Work/Vanuatu National Statistics Office/Reproducible Analytical Pipelines/RTraining/Data")
```

## Importing the data

With our working directory set, we can now import some data. Today our data will be in `csv` (**C**omma, **S**eparated **V**alues) format, which is a standard format for storing a table in a simple text form.

Here is some code to read in a csv file:
```{r reading in data in csv format}
MotorCarTrends <- read.csv("mtcars1.csv", header=TRUE)
```
<!-- [THIS IS A COMMENT] Hugo - worth implementing a naming conventions so that files and folders are informatively named and dated -->

Or we can use the following code to ask you to select a file:
```{r selecting csv data file by user, eval=FALSE}
MotorCarTrends <- read.csv(file.choose(), header=TRUE)
```

Additionally, if the data was in 'xlsx' format we would import by:
```{r reading in data in xlsx format}
# Read in the xlsx file
MotorCarTrends <- read.xlsx("mtcars1.xlsx")
```

## Exploring the data

### A quick look

There are loads of functions that we can use in R to quickly explore our data. Making sure it is in the right format for our analyses down the line. It is can also be used to explore the underlying data for error detection. 

```{r quick look at the data}
# Check out the names of the MotorCarTrends variable
names(MotorCarTrends)

# Check the class of the MotorCarTrends variable
class(MotorCarTrends)

# Check the classes of each column in the MotorCarTrends dataframe
colClasses <- sapply(MotorCarTrends, FUN=class)
print(colClasses)

# Check the dimensions of the MotorCarTrends dataframe
dim(MotorCarTrends)

# View the first 5 rows of the table
head(MotorCarTrends, n=5)

# View the last 5 rows of the table
tail(MotorCarTrends, n=5)

# Lastly let's print the table so we can interact with it
prettyTable(MotorCarTrends)
```

### Merging datasets

To merge two datasets (data frames) horizontally, use the merge function. In most cases, we join the data frames by one or more common key variables.

```{r merging datasets}
# Read in dataframes that want to merge  
MotorCarTrends<- read.csv('mtcars1.csv')
MotorCarPrices<- read.csv('mtcars2.csv')

# Merge two data frames by a unique identifer 
names(MotorCarTrends)
names(MotorCarPrices) #unique identifer from both datasets will merge on is 'Id'
MergedMotorCarTrends<- merge(MotorCarTrends, MotorCarPrices, by= c('Id'))
head(MergedMotorCarTrends)
MotorCarTrends<- MergedMotorCarTrends #update dataset MotorCarTrends with merged dataframe
```

### Setting our column classes

Some of the data in our `motorCarTrends` dataframe are categorical. We can tell R this to help us in our analyses down the line:

```{r note categorical columns}
# Set the engine shape and transmission type columns to be categorical
MotorCarTrends$vs <- as.factor(MotorCarTrends$vs) # Engine shape
MotorCarTrends$am <- as.factor(MotorCarTrends$am) # Transmission type

# Take a look at the column classes again
colClasses <- sapply(MotorCarTrends, FUN=class)
print(colClasses)

# And take a look at the table again - note it hasn't changed
head(MotorCarTrends)
```

### Creating meaningful column names

The column names `r colnames(motorCarTrends)` are quite difficult to interpret. We can rename the columns in the `motorCarTrends` dataframe with the following code:

```{r renaming columns}
# Create a vector of the new names
newColumnNames <- c("IdentificationNumber","CarModel", "MilesPerGallon", "NumberOfCylinders", "Displacement", "HorsePower", "RearAxelRatio", "Weight", "TimeToQuarterMile", "EngineShape", "TransmissionType", "NumberForwardGears", "NumberOfCarburetors", "CostOfCar", "AgeOfBuyer", "GenderOfBuyer")

# Rename the columns of the motorCarTrends dataframe
colnames(MotorCarTrends) <- newColumnNames

# Take another look at the table
head(MotorCarTrends)
```

Notice that I didn't include any spaces in the column names. You can have spaces in your column names but not having them makes thing easier.

### Calculating summary statistics

We can calculate summary statistics for our data using the `str()` and `summary()` functions. The `str()` displays the structure of the dataframe. For the numeric data, the `summary()` provides summaries of the their distributions (range, average and quantiles). For the categorical data, the `summary()` function will count the number of entries in each category. They can also be used to explore the underlying data for error detection. 

```{r calculate summary statistics}
str(MotorCarTrends)
```

```{r calculate summary statistics}
summary(MotorCarTrends)
```

### Character manipulation 

Often character data can be input in a number of different ways making it difficult to process. For instance Female might be coded as F, Fem, fem, Female. If this would be treated as a factor variable without any preprocessing, four not one class would be stored. Below we discuss an approach to string coding. 

```{r character manipulation of the data}
# Install and load package
install.packages('stringr')
library(stringr)

# Investigate data that needs to be manipulated
MotorCarTrends$GenderOfBuyer
```

### Removing duplicates

Duplicates are data points that are repeated in your dataset. It often happens when data is combined from different sources or someone entering the data submits the entry mulitple times. First you have to investigate if there are  duplicates in the dataset before you either decide to keep or remove them. 

```{r removing duplicates from the data}
# Investigate for duplicates
DuplicatesMotorCarTrends<- duplicated(MotorCarTrends) #selects all replicated rows
summary(DuplicatesMotorCarTrends)
DuplicateRowsMotorCarTrends<- MotorCarTrends[DuplicatesMotorCarTrends,]
DuplicateRowsMotorCarTrends

# Remove duplicates
DuplicatesRemovedMotorCarTrends<- unique(MotorCarTrends)
MotorCarTrends<-  DuplicatesRemovedMotorCarTrends
```

### Missing values

We can delete missing values however data loss can lead to biases in our analysis. Additionally, we can replace missing values with constants such as averages and modes, however this may not be logical. For missing data we will use the MICE package that creates multiple imputations as compared to a single imputation (such as mean). 

```{r impute missing data}
# Install and load MICE Package
install.packages("mice")
library(mice)
library(VIM)

# Calculate percentage of missing data 
PercentageMissingMotorCarTrends<- function(x) {sum(is.na(x)/length(x)*100)} #defining a function to calculate percentage
apply(MotorCarTrends, 2, PercentageMissingMotorCarTrends) #apply function to columns of MotorCarTrends

# Visualise the missing data
md.pattern(MotorCarTrends)

# Apply MICE function to impute random missing values
ImputeMissingMotorCarTrends<- mice(MotorCarTrends, m = 3, seed = 123) #m is number of models that the MICE package will develop
print(ImputeMissingMotorCarTrends)

# Visualise the models
stripplot(ImputeMissingMotorCarTrends, pch= 20, cex= 1.2) #pch and cex are line thickness and data points

# Choose model that best fits data
CompleteMotorCarTrends<- complete(ImputeMissingMotorCarTrends, 3) #First model is chosen
MotorCarTrends<- CompleteMotorCarTrends

# Check for missing values
sum(is.na(MotorCarTrends))
```

### Outliers 

They are values that are significantly different from all other observations. Any data value that lies beyond the upper and lower limited is outside the `Inter-Quartile Range (IQR)`. IQRs are visually represented using the `boxplot function`. Outliers should nopt be removed unless there is a good reason too. 

IQR= 3rd Quartile (75th percentile) - 1st Quartile (25th percentile)
Upper limit= mean + 1.5*IQR
Lower limit= mean - 1.5*IQR

```{r outliers}
# Make a boxplot
str(MotorCarTrends)
boxplot(MotorCarTrends$HorsePower)

# Return values of the boxplot
ValueBoxplot<- boxplot(MotorCarTrends$HorsePower)
ValueBoxplot
``` 

### Create new variable in the dataset

In R programming, the mutate function is used to create a new variable from a data set. In order to use the function, we need to install the dplyr package. Once installed, we can use mutate to specify the name of the new variable, and the action we are taking (e.g. dividing a field by 10).

```{r creating a new variable to the data}
#Load library
install.packages('dplyr')
library(dplyr)

# Add column to the new data and put in decending order
MotorCarTrendsMutated<- MotorCarTrends %>%
  mutate(CostPerMile= MilesPerGallon/10) %>% #cost of gallon petrol is $10
  arrange (desc(CostPerMile)) 
MotorCarTrends<- MotorCarTrendsMutated
```

### Categorising data

R - Factors. Factors are the data objects which are used to categorize the data and store it as levels. They can store both strings and integers. They are useful in the columns which have a limited number of unique values.

```{r creating a new variable to the data using factorising}
# Factorising the ages of car buyers
age<- age_calc(a, units= 'years')

AgeOfBuyerMotorCarTrends <- case_when(between(AgeOfBuyer, 20,29) ~ '20-29',
                  between(AgeOfBuyer, 30,39) ~ '30-39',
                  between(AgeOfBuyer, 40,49) ~ '40-49',
                  between(AgeOfBuyer, 50,59) ~ '50-59',
                  between(AgeOfBuyer, 60,69) ~ '60-69')

AgeOfBuyerMotorCarTrends<- as.factor(AgeOfBuyerMotorCarTrends) #converts output to a factor

# Create a new variable
MotorCarTrends$AgeCategoryOfBuyer<- AgeOfBuyerMotorCarTrends #AgeCategoryOfBuyer is new variable name

```

### Calculating summary statistics to check data is cleaned

We can calculate summary statistics again to check if there are any erorrs in our data remaining before we begin the analysis. 

```{r calculate summary statistics}
str(MotorCarTrends)
```

```{r calculate summary statistics}
summary(MotorCarTrends)
```

## Export cleaned dataset

```{r export cleaned dataset}
# Write the data to a csv file
write.csv(MotorCarTrends, file="cleanedmtcars.csv")
```


### Subsetting the data

In R, we can select subsets of our data using the names or indices of rows or columns. When selecting subsets using row and column names/indices, we use the following format:
`tableName[rows, columns]`

Here are some examples about how to create subsets of the `motorCarTrends` dataframe:
```{r subsetting the data}
# Select the first 3 rows of the table
first3Rows <- motorCarTrends[1:3, ] # Leaving the columns part blank means select all
print(first3Rows)

# Select the data for miles per gallon and horse power
milesPerGallonAndHorsePower <- motorCarTrends[ , c("milesPerGallon", "HorsePower")] # Select all rows and these columns
head(milesPerGallonAndHorsePower)
```

We can use conditional statements to subset our data as well:

```{r conditional subsetting}
# Select the data for automatic cars with a high horsepower
automaticCarData <- motorCarTrends[motorCarTrends$TransmissionType == 0 & motorCarTrends$HorsePower > 150, ]

# Take a look at this subset
prettyTable(automaticCarData)
```

## Plotting the data

Is there a relationship between horsepower and the time it takes to travel a quarter of a mile?
```{r plotting horsepower versus quarter mile travel time, echo=FALSE}
plot(x=motorCarTrends$HorsePower, y=motorCarTrends$TimeToQuarterMile,
     bty="n", # Remove box from around plot
     pch=19, # Set the plotting shape
     las=1, # Set the angle of tick labels to be horizontal
     main="Does horse power affect speed?", # Add a plot title
     ylab="Time to travel a quarter of a mile (seconds)", # Y axis label
     xlab="Gross horse power", # X axis label
     col="blue", # Set colour of points
     cex=1.5) # Set the size of the points

# Run a simple linear regression to test whether there is a relationship
linearModelResults <- lm(TimeToQuarterMile ~ HorsePower, data=motorCarTrends)

# Generate some example horse power values - for predicting speed
horsePowerValues <- seq(min(motorCarTrends$HorsePower), max(motorCarTrends$HorsePower), by=1)

# Predict the time taken to travel a quarter mile for the example values
confidenceIntervals <- predict(linearModelResults, newdata=data.frame(HorsePower=horsePowerValues),
                               interval="confidence", # Record the confidence intervals around predicted values
                               level=0.95) # Set the level for the confidence intervals

# Add the confidence intervals onto the plot as a polygon
polygon(x=c(horsePowerValues, rev(horsePowerValues)),
        y=c(confidenceIntervals[, 2], rev(confidenceIntervals[,3])), 
        col=rgb(1,0,0, 0.25), # Set the colour to be red and slightly transparent
        border=NA) # Remove the border from around the polygon

# Add a line of best fit to the plot
lines(x=horsePowerValues, 
      y=confidenceIntervals[, 1],
      col="red", # Set the line colour to be red
      lty=2, # Set the type of the line to be dashed
      lwd=2) # Set the width of the line
```


```{r plotting horsepower versus quarter mile travel time, echo=FALSE}
plot(x=motorCarTrends$HorsePower, y=motorCarTrends$TimeToQuarterMile,
     bty="n", # Remove box from around plot
     pch=19, # Set the plotting shape
     las=1, # Set the angle of tick labels to be horizontal
     main="Does horse power affect speed?", # Add a plot title
     ylab="Time to travel a quarter of a mile (seconds)", # Y axis label
     xlab="Gross horse power", # X axis label
     col="blue", # Set colour of points
     cex=1.5) # Set the size of the points

# Run a simple linear regression to test whether there is a relationship
linearModelResults <- lm(TimeToQuarterMile ~ HorsePower, data=motorCarTrends)

# Generate some example horse power values - for predicting speed
horsePowerValues <- seq(min(motorCarTrends$HorsePower), max(motorCarTrends$HorsePower), by=1)

# Predict the time taken to travel a quarter mile for the example values
confidenceIntervals <- predict(linearModelResults, newdata=data.frame(HorsePower=horsePowerValues),
                               interval="confidence", # Record the confidence intervals around predicted values
                               level=0.95) # Set the level for the confidence intervals

# Add the confidence intervals onto the plot as a polygon
polygon(x=c(horsePowerValues, rev(horsePowerValues)),
        y=c(confidenceIntervals[, 2], rev(confidenceIntervals[,3])), 
        col=rgb(1,0,0, 0.25), # Set the colour to be red and slightly transparent
        border=NA) # Remove the border from around the polygon

# Add a line of best fit to the plot
lines(x=horsePowerValues, 
      y=confidenceIntervals[, 1],
      col="red", # Set the line colour to be red
      lty=2, # Set the type of the line to be dashed
      lwd=2) # Set the width of the line
```
