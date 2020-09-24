# Gaps-in-Dates

## Gaps between dates
In my work, I frequently have to determine if a date was between two other dates.  This could be fiscal years, academic terms, program dates, etc. This works really well if your start and end dates don't line up. For example, school terms may end in May then start again in August. 

First, tell R you are working with a date. There are many different formats and R is smart. 
```
data <- data %>%
  mutate(date = as.Date(date, "%d-%b-%y")) %>% 
  dplyr::select(c("ID", "date")) 
```
Define date ranges and format as a date. 

```
  dates <- dates %>%  
  mutate(start = as.Date(TERM_START_DATE, "%d-%b-%y"), 
         end = as.Date(TERM_END_DATE, "%d-%b-%y")) %>% 
  dplyr::select(c("TERM_CODE", "start", "end" ))
```
Using library(fuzzyjoin), you can tell R to join based on loose dates. 

```
join.date <- fuzzy_left_join(
  data, dates,
  by = c("Date" = "start", #if it's after the start date
         "Date" = "end"), #but before the end date
  match_fun = c(`>=`, `<=`) #as specified here
) 
```
