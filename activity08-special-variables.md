Activity 8
================
Viktoria Basso

## Data and packages

Load the entirety of the `{tidyverse}`. `{forcats}` and `{stringr}` are
loaded as part of this. If you wish to work with dates during this
activity, you will need to also load `{lubridate}`. Be sure to avoid
printing out any unnecessary information and give the code chunk a
meaningful name.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.4     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.2     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.1.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(forcats)
library(stringr)
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

Cheatsheets that you might want to add to your collection:

-   [`{stringr}`](https://stringr.tidyverse.org/)
-   [`{forcats}`](https://forcats.tidyverse.org/)
-   [`{lubridate}`](https://lubridate.tidyverse.org/)

Using `here::here`, upload the `billboard_songs.txt` file that is saved
in your `data` folder. Notice that this file is a tab-delimited file
that is stored as a `.txt` file. Therefore, you will need to use either
`read_delim` with a `delim = ...` argument or (better) `read_tsv` .
Assign the file to a meaningful object name, be sure to avoid printing
any unnecessary information, and give the code chunk a meaningful name.

``` r
BillSongs <- read_tsv(here::here("data","billboard_songs.txt"))
```

These data include information on song popularity. In the US, the
Billboard Hot 100 is a list that comes out every week, showing the 100
most played songs that week. More information about the creation of this
dataset, as well as some analyses by the author, can be found here:
<https://mikekling.com/analyzing-the-billboard-hot-100/>. The dataset
you are provided is a limited version of the full data, containing:

-   `title`
-   `artist`
-   `overall peak`: The highest rank the song ever reached (1 is the
    best)
-   `weeks on chart`: The number of weeks the song was on the chart
-   `chart date`: The latest date the song appeared on the Billboard Hot
    100

This is a long dataset (34,605 observations)! You might like to create a
small dataset with only, say, 200 of the rows to try all your code out
on the smaller dataset first, and then only run the analysis of the full
data after you have experienced everything. One way to do this is to use
a function like `slice_sample(n = ...)` from `{dplyr}`.

    # Randomly sample 200 rows
    dataset %>% 
      slice_sample(n = 200)

If you wish to work with `{stringr}`, I find it useful to work on
vectors first. A way to do this is with `pull` from `{dplyr}`.

    # Turn variable from dataset into a vector
    dataset %>% 
      pull(variable)

## Analysis

You are encouraged to explore these data as you wish using functions
from the packages for the three special variable types. Some ideas that
you might be interested in:

-   What 10 songs (display title, artist, and week) were on the charts
    for the longest?

``` r
BillRank <- BillSongs %>%
  drop_na(`weeks on chart`) %>%
  arrange(`weeks on chart`) %>%
  tail(n=10)
view(BillRank)
```

Imagine Dragons Radioactive was first, then I’m Yours Jason Miraz, and
then How do I live by LeANN Rimes.

-   What distinct date did the oldest song(s) leave the charts?

``` r
class(BillSongs$`chart date`)
```

    ## [1] "numeric"

``` r
WeekChartvec <- ymd(pull(BillSongs,`chart date`))
BillSongs$Date <- WeekChartvec
OldBill <- BillSongs %>%
  arrange(ymd(Date)) %>%
  head(n=10)
```

The oldest songs left in the 1940s in July.

-   What songs could have been played at your 16th birthday party? That
    is, which songs that eventually peaked at \#1 **entered** the charts
    within a couple months (before or after) your 16th birthday?
    Assuming you were 16 years old by 2015.

``` r
Birthday <- interval(ymd("2013-08-1"),ymd("2013-10-30"))
BirthdaySong <- BillSongs[BillSongs$Date >= "2013-08-1" & BillSongs$Date <= "2013-10-30", ]
BirthdaySong %>%
  filter(`overall peak` == 1)
```

    ## # A tibble: 2 x 6
    ##   title     artist       `overall peak` `weeks on chart` `chart date` Date      
    ##   <chr>     <chr>                 <dbl>            <dbl>        <dbl> <date>    
    ## 1 Thrift S… MACKLEMORE …              1               48     20130921 2013-09-21
    ## 2 When I W… BRUNO MARS                1               32     20130831 2013-08-31

Two Songs on the chart around my 16th birthday that would make it to \#1
are Thrift Shop by Macklemore and When I Was Your Man by Bruno Mars.

-   Which artist has been **featured** on the most Billboard charting
    songs?

``` r
Artist <- BillSongs$artist
Artistsub <- str_detect(Artist, '.featuring.')
Artist2 <- tibble(Artist, Artistsub)
Artist2 %>% 
  filter(Artistsub == "TRUE") %>%
  mutate(Featured = )
```

    ## # A tibble: 1,527 x 2
    ##    Artist                                               Artistsub
    ##    <chr>                                                <lgl>    
    ##  1 MARK RONSON featuring BRUNO MARS                     TRUE     
    ##  2 USHER featuring JUICY J                              TRUE     
    ##  3 NICKI MINAJ featuring DRAKE, LIL WAYNE & CHRIS BROWN TRUE     
    ##  4 NICKI MINAJ featuring DRAKE & LIL WAYNE              TRUE     
    ##  5 NE-YO featuring JUICY J                              TRUE     
    ##  6 BIG SEAN featuring E-40                              TRUE     
    ##  7 NATALIE LA ROSE featuring JEREMIH                    TRUE     
    ##  8 FLO RIDA featuring SAGE THE GEMINI & LOOKAS          TRUE     
    ##  9 CALVIN HARRIS featuring ELLIE GOULDING               TRUE     
    ## 10 BLAKE SHELTON featuring ASHLEY MONROE                TRUE     
    ## # … with 1,517 more rows

-   Which artist has **collaborated** on the most Billboard charting
    songs?
-   Create some data visualization controlling the order of the
    character/string variables.

## Attribution

Parts of this Activity are based on a lab from [Dr. Kelly
Bodwin’s](https://www.kelly-bodwin.com/) STAT 331 course.
