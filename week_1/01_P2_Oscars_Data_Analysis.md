Lab, Part 2: `Oscars` Data Analysis
================

## Import libraries and data

``` r
library(tidyverse)
library(lubridate)

oscars <- read_csv('https://raw.githubusercontent.com/Cal-Poly-Advanced-R/Lab_1/master/Oscars-demographics-DFE.csv')
```

## Tidy data

Upon checking the data, we can see there aresome inconsistencies.

For `date_of_birth`:

  - One person has `[1]` at the end of their birthday  
  - Some of the dates have years that are 2 digits, some 4. If we simply
    use `dmy()`, the years before 1969 will be put in the 21st century
    instead of the 20th.  
  - There is one person who only has a birth year listed.

<!-- end list -->

``` r
oscars$date_of_birth <- str_replace_all(oscars$date_of_birth," [1]","")
oscars <- oscars %>% 
  # fix the date of birth
  mutate(date_of_birth = if_else(str_length(date_of_birth) > 11, str_sub(date_of_birth,1,11), date_of_birth)) %>%  ## remove "[1]" from one of the obs
  separate(date_of_birth, c("day","month","birthyear"), sep = "-", remove = FALSE) %>%   ## separate birthday components
  mutate(birthyear = case_when(str_length(birthyear) == 2 ~ paste0("19",birthyear),  ## add the century to the birth year
                               str_length(day) == 4 ~ day,  ## address the obs with only the year
                               TRUE ~ birthyear),
         dob = ymd(paste(birthyear,month,day,sep = " ")))  ## get the formatted birthday
```

## Warm-ups

1.  Which movie(s) won the most unique “Big 5” awards?

<!-- end list -->

``` r
most_awards <- oscars %>% 
  select(movie,award) %>% 
  distinct() %>%   ## make sure there are no duplicates
  group_by(movie) %>% 
  count(name = "awards") %>%  ## count the number of awards per movie
  ungroup() %>% 
  filter(awards == max(awards))  ## only get the top movies with the most unique awards
```

| movie                       | awards |
| :-------------------------- | -----: |
| A Streetcar Named Desire    |      3 |
| Cabaret                     |      3 |
| From Here to Eternity       |      3 |
| Going My Way                |      3 |
| Gone with the Wind          |      3 |
| It Happened One Night       |      3 |
| Kramer vs. Kramer           |      3 |
| Million Dollar Baby         |      3 |
| Mrs. Miniver                |      3 |
| Network                     |      3 |
| On the Waterfront           |      3 |
| Terms of Endearment         |      3 |
| The Best Years of Our Lives |      3 |
| West Side Story             |      3 |

2.  Of all actresses who have won the Best Actress award, what are is
    the most common first name?

<!-- end list -->

``` r
common_name <- oscars %>% 
  filter(award == "Best Actress") %>% 
  distinct(person)%>%  ## make sure each actress is only counted once
  mutate(first_name = word(person,1)) %>%  ## get their first names
  group_by(first_name) %>% 
  count() %>%  ## count the number of women with the same first name
  ungroup() %>% 
  filter(n == max(n)) ## only output the most common
```

| first\_name | n |
| :---------- | -: |
| Helen       | 3 |

3.  What US State, or non-US country, has produced the most Oscar
    winners (for the awards in this dataset)?

<!-- end list -->

``` r
common_birthplace <- oscars %>% 
  select(person, birthplace) %>% 
  distinct() %>% 
  group_by(birthplace) %>%
  count() %>% 
  ungroup() %>% 
  filter(n == max(n))
```

| birthplace    |  n |
| :------------ | -: |
| New York City | 27 |

The most common birthplace is New York, since New York City has produced
the most Oscar winners.
