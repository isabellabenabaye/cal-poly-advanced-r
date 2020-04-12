# [Review of Data Wrangling](https://cal-poly-advanced-r.github.io/STAT-431/Canvas_Pages/Data_Wrangling.html)

## Check-In 1: dplyr and piping
**Question 1:** Suppose we would like to study how the length-to-width ratio of petals differs across the species. Rearrange the following steps in the pipeline into an order that accomplishes this goal.  

**Answer:** c, b, e, a, d

**Question 2:** Consider the base R code below.
`mean(iris[iris$Species == "setosa", "Petal.Length"])`  
For each of the following dplyr pipelines, indicate if it:
- Returns the exact same thing as the Base R code;
- Returns the correct information, but the wrong object type;
- Returns incorrect information; or
- Returns an error

1. `iris %>%     
        filter("Petal.Length") %>%
        pull("setosa") %>%
        mean()`  
    **A:** Returns an error

2. `iris %>%
        filter(Species == "setosa") %>%
        select(Petal.Length) %>%
        summarize(mean(Petal.Length))`  
    **A:** Returns the exact same thing as the Base R code

3. `iris %>%
        pull(Petal.Length) %>%
        filter(Species == "setosa") %>%
        mean()`  
    **A:** Returns the exact same thing as the Base R code

4. `iris %>%
        filter(Species == "setosa") %>%
        select(Petal.Length) %>%
        mean()`  
    **A:** Returns the exact same thing as the Base R code

5. `iris %>%
        filter(Species == "setosa") %>%
        pull(Petal.Length) %>%
        mean()`  
    **A:** Returns the exact same thing as the Base R code

6. `iris %>%
        select(Species == "setosa") %>%
        filter(Petal.Length) %>%
        summarize(mean(Petal.Length))`  
    **A:** Returns an error

## Check-In 2: Pivoting
Consider the following dataset, which contains information about arrests for violent crimes in each state:
```
head(us_arrests)

##            Murder Assault UrbanPop
## Alabama      13.2     236       58
## Alaska       10.0     263       48
## Arizona       8.1     294       80
## Arkansas      8.8     190       50
## California    9.0     276       91
## Colorado      7.9     204       78
```
**Question 1:** Consider the following code. What does it do, and why might it be an important step before reshaping the data?
```
us_arrests <- us_arrests %>%
  rownames_to_column()
```
**A:** The code makes the index a new column with the state names, which makes the data tidy and ready for annalysis.

Question 2: Fill in the blanks for the code that will produce the following:
```
us_arrests %>%
  pivot_longer(cols = c('Murder','Assault'),
                names_to = "Crime",
                values_to= "Rate")
```