# Open Estonian Traffic count datasets


At the Mobile Tartu in Estonia I learned about the country’s strong open
data in general, and open traffic count data in particular. A quick
search, via their open data repository, led here:
https://avaandmed.eesti.ee/datasets/liiklusloenduse-andmed

We’ll use the tidyverse:

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ purrr::%||%()   masks base::%||%()
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

Let’s try to download some data, starting with the following:

``` r
list.files()
```

    [1] "d0cc4ba5-9a4d-448c-b268-bfb5e7b71537-LL-meta.xlsx.csv"
    [2] "e501fb9b-4a71-453f-9d7f-bb5e819ee692-ll_2024.csv.csv" 
    [3] "README.qmd"                                           
    [4] "README.rmarkdown"                                     

``` r
f = "e501fb9b-4a71-453f-9d7f-bb5e819ee692-ll_2024.csv.csv"
if (!file.exists(f)) {
    stop("Go to the portal and download the data")
}
```

You also need to download the traffic location data:

``` r
locations = read_csv("d0cc4ba5-9a4d-448c-b268-bfb5e7b71537-LL-meta.xlsx.csv")
```

    Rows: 118 Columns: 7
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr (3): Name, Connection ID, County
    dbl (4): Road nr, Road km, Lon, Lat

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# # A tibble: 118 × 7
#    Name                   `Connection ID` `Road nr` `Road km` County   Lon   Lat
#    <chr>                  <chr>               <dbl>     <dbl> <chr>  <dbl> <dbl>
#  1 LOO 1_13236 VBV        0cfcf                   1      13.2 Harju…  25.0  59.4
#  2 PRÜGILA RIST 1_17794 … 25785                   1      17.8 Harju…  25.0  59.5
#  3 KODASOO 1_32100 VBV    077a6                   1      32.1 Harju…  25.3  59.4
#  4 VIITNA 1_73241 VBV     67241                   1      73.2 Lääne…  26.0  59.5
#  5 SÄMI 1_109455 VBV      387ad                   1     109.  Lääne…  26.6  59.4
#  6 VARJA 1_146054 VBV     748fe                   1     146.  Ida-V…  27.1  59.4
#  7 KUKRUSE 1_158295 VBV   5e92d                   1     158.  Ida-V…  27.3  59.4
#  8 KONJU 1_176970 VBV     180b3                   1     177.  Ida-V…  27.6  59.4
#  9 SINIMÄE 1_194738 VBV   91e72                   1     195.  Ida-V…  27.9  59.4
# 10 PEETRI 2_7050 VBV      0f55c                   2       7   Harju…  24.8  59.4
# # ℹ 108 more rows
# # ℹ Use `print(n = ...)` to see more rows
```

``` r
traffic_data = read_csv(f)
```

    Rows: 1029423 Columns: 24
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr   (1): id
    dbl  (22): 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, kanal, <40Kph, 40-<50, 50-<60, 60-...
    dttm  (1): aeg

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
names(traffic_data)
```

     [1] "1"        "2"        "3"        "4"        "5"        "6"       
     [7] "7"        "8"        "9"        "10"       "id"       "kanal"   
    [13] "aeg"      "<40Kph"   "40-<50"   "50-<60"   "60-<70"   "70-<80"  
    [19] "80-<90"   "90-<100"  "100-<110" "110-<120" "120-<130" "=>130"   

``` r
#  [1] "1"        "2"        "3"        "4"        "5"        "6"       
#  [7] "7"        "8"        "9"        "10"       "id"       "kanal"   
# [13] "aeg"      "<40Kph"   "40-<50"   "50-<60"   "60-<70"   "70-<80"  
# [19] "80-<90"   "90-<100"  "100-<110" "110-<120" "120-<130" "=>130"
unique_counters = unique(traffic_data$id)
length(unique_counters) # 116
```

    [1] 116

Let’s see how well the IDs of the locations matches the IDs from the
traffic data

``` r
summary(locations$`Connection ID` %in% unique_counters)
```

       Mode   FALSE    TRUE 
    logical       3     115 
