# Gaps-in-Dates

## Gaps between dates
In my work, I frequently have to determine if a date was between two other dates.  This could be fiscal years, academic terms, program dates, etc. This works really well if your start and end dates don't line up. For example, school terms may end in May then start again in August. 

For this example, I'll use the `flights` dataset from `library(nycflights13)`. 
For this example, I need 1 column of dates, so I cleaned up the above dataset like so:
```
flights %>% 
    select(flight, year, month, day) %>% 
    unite(date, year,month, day, sep="-")
```

I typically first, tell R that I am working with a date. There are many different formats and R is smart. 

Here's a generic example if your column is already a character vector:
```
df %>%
  mutate(date = as.Date(date, "%y-%b-%d")) 
```

Let's say in this example, we are interested in flights around holidays, these are usually +/-3 days around holidays. Define date ranges and format as a date for those holiday ranges, let's just use Thanksgiving, Christmas, and New Year's. 

I've built a dataframe of holidays from the `Holidays` R package. Then added 3 days from `date` for `end` date and subtracted 3 days from `date` for `start`. 

![image](https://user-images.githubusercontent.com/51967620/141867831-fd279745-ceb8-4c94-a74d-d4d4510dbf9c.png)

Now using `library(fuzzyjoin)`, you can tell R to join based on loose dates. 

```
fuzzy_left_join(
  flights, holidays,
  by = c("date" = "start", #if it's after the start date
         "date" = "end"), #but before the end date
  match_fun = c(`>=`, `<=`) #as specified here
) 
```
Your output will have the holiday that the flight lines up with if the flight date was +/- 3 days from one of the perscribed holdiays. If the column `holiday` is `NA`, then your flight did not leave during a busy holiday time. `fuzzy_joins()` and joins are nice alternatives to clunky `case_whens()`.  
