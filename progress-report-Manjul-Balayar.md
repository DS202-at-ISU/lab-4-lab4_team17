    library(tidyverse)

    ## ── Attaching core tidyverse packages ────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

    library(rvest)

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

    library(Lahman)
    knitr::opts_chunk$set(echo = TRUE)

    # Loading the Hall of Fame dataset from Lahman package to check its structure.
    data("HallOfFame", package = "Lahman")
    head(HallOfFame)

    ##    playerID yearID votedBy ballots needed votes inducted category needed_note
    ## 1  cobbty01   1936   BBWAA     226    170   222        Y   Player        <NA>
    ## 2  ruthba01   1936   BBWAA     226    170   215        Y   Player        <NA>
    ## 3 wagneho01   1936   BBWAA     226    170   215        Y   Player        <NA>
    ## 4 mathech01   1936   BBWAA     226    170   205        Y   Player        <NA>
    ## 5 johnswa01   1936   BBWAA     226    170   189        Y   Player        <NA>
    ## 6 lajoina01   1936   BBWAA     226    170   146        N   Player        <NA>

## Web Scraping Verification

    # Scrape the webpage and check the structure of the tables.
    url_2023 <- "https://www.baseball-reference.com/awards/hof_2023.shtml"
    web_page <- read_html(url_2023)
    hof_tables <- html_table(web_page, fill = TRUE)
    # Display the structure of all scraped tables to identify which one we need.
    str(hof_tables)

    ## List of 2
    ##  $ : tibble [29 × 39] (S3: tbl_df/tbl/data.frame)
    ##   ..$               : chr [1:29] "Rk" "1" "2" "3" ...
    ##   ..$               : chr [1:29] "Name" "Scott Rolen" "Todd Helton HOF" "Billy Wagner" ...
    ##   ..$               : chr [1:29] "YoB" "6th" "5th" "8th" ...
    ##   ..$               : chr [1:29] "Votes" "297" "281" "265" ...
    ##   ..$               : chr [1:29] "%vote" "76.3%" "72.2%" "68.1%" ...
    ##   ..$               : chr [1:29] "HOFm" "99" "175" "107" ...
    ##   ..$               : chr [1:29] "HOFs" "40" "59" "24" ...
    ##   ..$               : chr [1:29] "Yrs" "17" "17" "16" ...
    ##   ..$               : chr [1:29] "WAR" "70.1" "61.8" "27.7" ...
    ##   ..$               : chr [1:29] "WAR7" "43.6" "46.6" "19.8" ...
    ##   ..$               : chr [1:29] "JAWS" "56.9" "54.2" "23.7" ...
    ##   ..$               : chr [1:29] "Jpos" "56.3" "53.4" "32.5" ...
    ##   ..$ Batting Stats : chr [1:29] "G" "2038" "2247" "810" ...
    ##   ..$ Batting Stats : chr [1:29] "AB" "7398" "7962" "20" ...
    ##   ..$ Batting Stats : chr [1:29] "R" "1211" "1401" "1" ...
    ##   ..$ Batting Stats : chr [1:29] "H" "2077" "2519" "2" ...
    ##   ..$ Batting Stats : chr [1:29] "HR" "316" "369" "0" ...
    ##   ..$ Batting Stats : chr [1:29] "RBI" "1287" "1406" "1" ...
    ##   ..$ Batting Stats : chr [1:29] "SB" "118" "37" "0" ...
    ##   ..$ Batting Stats : chr [1:29] "BB" "899" "1335" "1" ...
    ##   ..$ Batting Stats : chr [1:29] "BA" ".281" ".316" ".100" ...
    ##   ..$ Batting Stats : chr [1:29] "OBP" ".364" ".414" ".143" ...
    ##   ..$ Batting Stats : chr [1:29] "SLG" ".490" ".539" ".100" ...
    ##   ..$ Batting Stats : chr [1:29] "OPS" ".855" ".953" ".243" ...
    ##   ..$ Batting Stats : chr [1:29] "OPS+" "122" "133" "-35" ...
    ##   ..$ Pitching Stats: chr [1:29] "W" "" "" "47" ...
    ##   ..$ Pitching Stats: chr [1:29] "L" "" "" "40" ...
    ##   ..$ Pitching Stats: chr [1:29] "ERA" "" "" "2.31" ...
    ##   ..$ Pitching Stats: chr [1:29] "ERA+" "" "" "187" ...
    ##   ..$ Pitching Stats: chr [1:29] "WHIP" "" "" "0.998" ...
    ##   ..$ Pitching Stats: chr [1:29] "G" "" "" "853" ...
    ##   ..$ Pitching Stats: chr [1:29] "GS" "" "" "0" ...
    ##   ..$ Pitching Stats: chr [1:29] "SV" "" "" "422" ...
    ##   ..$ Pitching Stats: chr [1:29] "IP" "" "" "903.0" ...
    ##   ..$ Pitching Stats: chr [1:29] "H" "" "" "601" ...
    ##   ..$ Pitching Stats: chr [1:29] "HR" "" "" "82" ...
    ##   ..$ Pitching Stats: chr [1:29] "BB" "" "" "300" ...
    ##   ..$ Pitching Stats: chr [1:29] "SO" "" "" "1196" ...
    ##   ..$               : chr [1:29] "Pos Summary" "*5/H" "*3H/7D9" "*1" ...
    ##  $ : tibble [2 × 40] (S3: tbl_df/tbl/data.frame)
    ##   ..$               : chr [1:2] "Rk" "1"
    ##   ..$               : chr [1:2] "Name" "Fred McGriff"
    ##   ..$               : chr [1:2] "Inducted As" "as Player"
    ##   ..$               : chr [1:2] "HOFm" "100"
    ##   ..$               : chr [1:2] "HOFs" "48"
    ##   ..$               : chr [1:2] "Yrs" "19"
    ##   ..$               : chr [1:2] "WAR" "52.6"
    ##   ..$               : chr [1:2] "WAR7" "36.0"
    ##   ..$               : chr [1:2] "JAWS" "44.3"
    ##   ..$               : chr [1:2] "Jpos" "53.4"
    ##   ..$ Batting Stats : chr [1:2] "G" "2460"
    ##   ..$ Batting Stats : chr [1:2] "AB" "8757"
    ##   ..$ Batting Stats : chr [1:2] "R" "1349"
    ##   ..$ Batting Stats : chr [1:2] "H" "2490"
    ##   ..$ Batting Stats : chr [1:2] "HR" "493"
    ##   ..$ Batting Stats : chr [1:2] "RBI" "1550"
    ##   ..$ Batting Stats : chr [1:2] "SB" "72"
    ##   ..$ Batting Stats : chr [1:2] "BB" "1305"
    ##   ..$ Batting Stats : chr [1:2] "BA" ".284"
    ##   ..$ Batting Stats : chr [1:2] "OBP" ".377"
    ##   ..$ Batting Stats : chr [1:2] "SLG" ".509"
    ##   ..$ Batting Stats : chr [1:2] "OPS" ".886"
    ##   ..$ Batting Stats : chr [1:2] "OPS+" "134"
    ##   ..$ Pitching Stats: chr [1:2] "W" ""
    ##   ..$ Pitching Stats: chr [1:2] "L" ""
    ##   ..$ Pitching Stats: chr [1:2] "ERA" ""
    ##   ..$ Pitching Stats: chr [1:2] "ERA+" ""
    ##   ..$ Pitching Stats: chr [1:2] "WHIP" ""
    ##   ..$ Pitching Stats: chr [1:2] "G" ""
    ##   ..$ Pitching Stats: chr [1:2] "GS" ""
    ##   ..$ Pitching Stats: chr [1:2] "SV" ""
    ##   ..$ Pitching Stats: chr [1:2] "IP" ""
    ##   ..$ Pitching Stats: chr [1:2] "H" ""
    ##   ..$ Pitching Stats: chr [1:2] "HR" ""
    ##   ..$ Pitching Stats: chr [1:2] "BB" ""
    ##   ..$ Pitching Stats: chr [1:2] "SO" ""
    ##   ..$               : chr [1:2] "Pos Summary" "*3DH"
    ##   ..$ Manager       : chr [1:2] "W" ""
    ##   ..$ Manager       : chr [1:2] "L" ""
    ##   ..$ Manager       : chr [1:2] "W-L%" ""

    # Show the first few rows of the first table to ensure it's correct.
    head(hof_tables[[1]])

    ## # A tibble: 6 × 39
    ##   ``    ``           ``    ``    ``    ``    ``    ``    ``    ``    ``    ``   
    ##   <chr> <chr>        <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 Rk    Name         YoB   Votes %vote HOFm  HOFs  Yrs   WAR   WAR7  JAWS  Jpos 
    ## 2 1     Scott Rolen  6th   297   76.3% 99    40    17    70.1  43.6  56.9  56.3 
    ## 3 2     Todd Helton… 5th   281   72.2% 175   59    17    61.8  46.6  54.2  53.4 
    ## 4 3     Billy Wagner 8th   265   68.1% 107   24    16    27.7  19.8  23.7  32.5 
    ## 5 4     Andruw Jones 6th   226   58.1% 109   34    17    62.7  46.4  54.6  58.2 
    ## 6 5     Gary Sheffi… 9th   214   55.0% 158   61    22    60.5  38.0  49.3  56.7 
    ## # ℹ 27 more variables: `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>, …

## Data Cleaning and Setup

    # Assuming the first table is correct, let's clean and set up columns.
    hof_2023_raw <- hof_tables[[1]]
    names(hof_2023_raw) <- as.character(hof_2023_raw[1,])
    hof_2023 <- hof_2023_raw[-1, ]
    names(hof_2023) <- names(hof_2023) %>% trimws()
    # Convert names to a standardized lowercase format to avoid case sensitivity issues.
    names(hof_2023) <- tolower(names(hof_2023))
    # Verify the structure after renaming.
    str(hof_2023)

    ## tibble [28 × 39] (S3: tbl_df/tbl/data.frame)
    ##  $ rk         : chr [1:28] "1" "2" "3" "4" ...
    ##  $ name       : chr [1:28] "Scott Rolen" "Todd Helton HOF" "Billy Wagner" "Andruw Jones" ...
    ##  $ yob        : chr [1:28] "6th" "5th" "8th" "6th" ...
    ##  $ votes      : chr [1:28] "297" "281" "265" "226" ...
    ##  $ %vote      : chr [1:28] "76.3%" "72.2%" "68.1%" "58.1%" ...
    ##  $ hofm       : chr [1:28] "99" "175" "107" "109" ...
    ##  $ hofs       : chr [1:28] "40" "59" "24" "34" ...
    ##  $ yrs        : chr [1:28] "17" "17" "16" "17" ...
    ##  $ war        : chr [1:28] "70.1" "61.8" "27.7" "62.7" ...
    ##  $ war7       : chr [1:28] "43.6" "46.6" "19.8" "46.4" ...
    ##  $ jaws       : chr [1:28] "56.9" "54.2" "23.7" "54.6" ...
    ##  $ jpos       : chr [1:28] "56.3" "53.4" "32.5" "58.2" ...
    ##  $ g          : chr [1:28] "2038" "2247" "810" "2196" ...
    ##  $ ab         : chr [1:28] "7398" "7962" "20" "7599" ...
    ##  $ r          : chr [1:28] "1211" "1401" "1" "1204" ...
    ##  $ h          : chr [1:28] "2077" "2519" "2" "1933" ...
    ##  $ hr         : chr [1:28] "316" "369" "0" "434" ...
    ##  $ rbi        : chr [1:28] "1287" "1406" "1" "1289" ...
    ##  $ sb         : chr [1:28] "118" "37" "0" "152" ...
    ##  $ bb         : chr [1:28] "899" "1335" "1" "891" ...
    ##  $ ba         : chr [1:28] ".281" ".316" ".100" ".254" ...
    ##  $ obp        : chr [1:28] ".364" ".414" ".143" ".337" ...
    ##  $ slg        : chr [1:28] ".490" ".539" ".100" ".486" ...
    ##  $ ops        : chr [1:28] ".855" ".953" ".243" ".823" ...
    ##  $ ops+       : chr [1:28] "122" "133" "-35" "111" ...
    ##  $ w          : chr [1:28] "" "" "47" "" ...
    ##  $ l          : chr [1:28] "" "" "40" "" ...
    ##  $ era        : chr [1:28] "" "" "2.31" "" ...
    ##  $ era+       : chr [1:28] "" "" "187" "" ...
    ##  $ whip       : chr [1:28] "" "" "0.998" "" ...
    ##  $ g          : chr [1:28] "" "" "853" "" ...
    ##  $ gs         : chr [1:28] "" "" "0" "" ...
    ##  $ sv         : chr [1:28] "" "" "422" "" ...
    ##  $ ip         : chr [1:28] "" "" "903.0" "" ...
    ##  $ h          : chr [1:28] "" "" "601" "" ...
    ##  $ hr         : chr [1:28] "" "" "82" "" ...
    ##  $ bb         : chr [1:28] "" "" "300" "" ...
    ##  $ so         : chr [1:28] "" "" "1196" "" ...
    ##  $ pos summary: chr [1:28] "*5/H" "*3H/7D9" "*1" "*89H7D/3" ...

## Handling Duplicate Column Names

    # Check current names and identify duplicates
    print(names(hof_2023))

    ##  [1] "rk"          "name"        "yob"         "votes"       "%vote"      
    ##  [6] "hofm"        "hofs"        "yrs"         "war"         "war7"       
    ## [11] "jaws"        "jpos"        "g"           "ab"          "r"          
    ## [16] "h"           "hr"          "rbi"         "sb"          "bb"         
    ## [21] "ba"          "obp"         "slg"         "ops"         "ops+"       
    ## [26] "w"           "l"           "era"         "era+"        "whip"       
    ## [31] "g"           "gs"          "sv"          "ip"          "h"          
    ## [36] "hr"          "bb"          "so"          "pos summary"

    # Renaming duplicate columns by appending a prefix based on their order or context (e.g., batting or pitching)
    names(hof_2023) <- make.unique(names(hof_2023), sep = "_")

    # Alternatively, you can manually rename to be more descriptive, especially for critical columns like 'g', 'h', 'hr', 'bb':
    names(hof_2023)[which(names(hof_2023) == "g")] <- c("games_batting", "games_pitching")

    ## Warning in names(hof_2023)[which(names(hof_2023) == "g")] <- c("games_batting",
    ## : number of items to replace is not a multiple of replacement length

    names(hof_2023)[which(names(hof_2023) == "h")] <- c("hits_batting", "hits_pitching")

    ## Warning in names(hof_2023)[which(names(hof_2023) == "h")] <- c("hits_batting",
    ## : number of items to replace is not a multiple of replacement length

    names(hof_2023)[which(names(hof_2023) == "hr")] <- c("home_runs_batting", "home_runs_pitching")

    ## Warning in names(hof_2023)[which(names(hof_2023) == "hr")] <-
    ## c("home_runs_batting", : number of items to replace is not a multiple of
    ## replacement length

    names(hof_2023)[which(names(hof_2023) == "bb")] <- c("walks_batting", "walks_pitching")

    ## Warning in names(hof_2023)[which(names(hof_2023) == "bb")] <-
    ## c("walks_batting", : number of items to replace is not a multiple of
    ## replacement length

    # Verify the changes
    print(names(hof_2023))

    ##  [1] "rk"                "name"              "yob"              
    ##  [4] "votes"             "%vote"             "hofm"             
    ##  [7] "hofs"              "yrs"               "war"              
    ## [10] "war7"              "jaws"              "jpos"             
    ## [13] "games_batting"     "ab"                "r"                
    ## [16] "hits_batting"      "home_runs_batting" "rbi"              
    ## [19] "sb"                "walks_batting"     "ba"               
    ## [22] "obp"               "slg"               "ops"              
    ## [25] "ops+"              "w"                 "l"                
    ## [28] "era"               "era+"              "whip"             
    ## [31] "g_1"               "gs"                "sv"               
    ## [34] "ip"                "h_1"               "hr_1"             
    ## [37] "bb_1"              "so"                "pos summary"

## Data Transformation

    # Constants for vote calculations
    total_ballots_2023 <- 389
    votes_needed_2023 <- 292  # Calculated based on some criteria like 75% of total ballots

    # Transform data, ensuring column names now match the unique ones assigned
    hof_2023_transformed <- hof_2023 %>% 
      mutate(
        playerid = gsub("X-", "", tolower(name)),  # Adjust based on the column 'name'
        yearid = 2023,
        votedby = "BBWAA",
        ballots = total_ballots_2023,
        needed = votes_needed_2023,
        votes = as.integer(votes),
        inducted = ifelse(votes >= votes_needed_2023, "Y", "N"),
        category = "Player",  # Assuming all entries are for players
        needed_note = NA_character_
      )

    # Verify the transformed data
    head(hof_2023_transformed)

    ## # A tibble: 6 × 47
    ##   rk    name       yob   votes `%vote` hofm  hofs  yrs   war   war7  jaws  jpos 
    ##   <chr> <chr>      <chr> <int> <chr>   <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 1     Scott Rol… 6th     297 76.3%   99    40    17    70.1  43.6  56.9  56.3 
    ## 2 2     Todd Helt… 5th     281 72.2%   175   59    17    61.8  46.6  54.2  53.4 
    ## 3 3     Billy Wag… 8th     265 68.1%   107   24    16    27.7  19.8  23.7  32.5 
    ## 4 4     Andruw Jo… 6th     226 58.1%   109   34    17    62.7  46.4  54.6  58.2 
    ## 5 5     Gary Shef… 9th     214 55.0%   158   61    22    60.5  38.0  49.3  56.7 
    ## 6 6     Carlos Be… 1st     181 46.5%   126   52    20    70.1  44.4  57.3  58.2 
    ## # ℹ 35 more variables: games_batting <chr>, ab <chr>, r <chr>,
    ## #   hits_batting <chr>, home_runs_batting <chr>, rbi <chr>, sb <chr>,
    ## #   walks_batting <chr>, ba <chr>, obp <chr>, slg <chr>, ops <chr>,
    ## #   `ops+` <chr>, w <chr>, l <chr>, era <chr>, `era+` <chr>, whip <chr>,
    ## #   g_1 <chr>, gs <chr>, sv <chr>, ip <chr>, h_1 <chr>, hr_1 <chr>, bb_1 <chr>,
    ## #   so <chr>, `pos summary` <chr>, playerid <chr>, yearid <dbl>, votedby <chr>,
    ## #   ballots <dbl>, needed <dbl>, inducted <chr>, category <chr>, …

    hof_2023_transformed <- hof_2023_transformed %>%
      select(playerID = playerid, yearID = yearid, votedBy = votedby,
             ballots, needed, votes, inducted, category, needed_note)

    # Convert the 'inducted' column from character to factor to match the Lahman 'HallOfFame' dataset
    hof_2023_transformed$inducted <- factor(hof_2023_transformed$inducted, levels = c("N", "Y"))

    # Verify the structure again
    str(hof_2023_transformed)

    ## tibble [28 × 9] (S3: tbl_df/tbl/data.frame)
    ##  $ playerID   : chr [1:28] "scott rolen" "todd helton hof" "billy wagner" "andruw jones" ...
    ##  $ yearID     : num [1:28] 2023 2023 2023 2023 2023 ...
    ##  $ votedBy    : chr [1:28] "BBWAA" "BBWAA" "BBWAA" "BBWAA" ...
    ##  $ ballots    : num [1:28] 389 389 389 389 389 389 389 389 389 389 ...
    ##  $ needed     : num [1:28] 292 292 292 292 292 292 292 292 292 292 ...
    ##  $ votes      : int [1:28] 297 281 265 226 214 181 181 139 129 76 ...
    ##  $ inducted   : Factor w/ 2 levels "N","Y": 2 1 1 1 1 1 1 1 1 1 ...
    ##  $ category   : chr [1:28] "Player" "Player" "Player" "Player" ...
    ##  $ needed_note: chr [1:28] NA NA NA NA ...

## Data Integration and Export

    # Combine with existing Hall of Fame data.
    combined_hof <- rbind(HallOfFame, hof_2023_transformed)
    # Export to CSV.
    write_csv(combined_hof, "HallOfFame2023.csv")
