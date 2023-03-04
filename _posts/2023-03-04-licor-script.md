---
layout: post
title:  "A simple script for turning LI-6400 files into an R dataframe and .csv file"
---

The LI-6400 is the one of the most common gas exchange instrument for studying photosynthesis. However, given its age, the files that it outputs is not so friendly with common languages for data analysis.

It was time-consuming for me to do the following in Excel:
1. Take the columns of data that I'm interested in, e.g., the "Photo" column with CO<sub>2</sub> assimilation rate values.
2. Get rid of empty cells. The "Remark=" lines only have content in the first column, but adds empty cells for the rest of the row.
3. Merge data from multiple files together.

So, here I'm sharing a script that does that I will note that I manually add the leaf area to the raw .xls files and save it as .xlsx, because readxl doesn't seem to be able to read them. Otherwise, I would have a separate file with the leaf areas and just directly load the .xls files with the script.

{% highlight R %}

library(dplyr)

# SET WORKING DIRECTORY and CHOOSE COLUMNS OF INTEREST
setwd(here::here())
RUN_TEST <- FALSE # Running a test on one file can save time, in case there are errors with the function
TEST_NAME <- "2022-10-07 AL tenella 38_.xlsx"
COLUMNS <- c("Filename", "Obs", "Photo", "Ci", "Cond", "vp_kPa")
OUTPUT_NAME <- "merged.csv"

# DEFINE FUNCTION
# Takes the path of the .xlsx file from a LI-6400 you want to read, outputs a dataframe with all the columns in the file.
read_licor <- function(filename) {
  # Read .xlsx file
  dataframe <- readxl::read_xlsx(filename, skip = 8) %>% # skip the first 8 metadata lines
    dplyr::slice(-1) %>% # remove first row of "in" and "out"
    dplyr::filter(Obs != "Remark=") # remove rows with "Remark="
  # Check leaf areas for mistakes
  areas <- dataframe %>% select(Area) %>% distinct()
  if (length(areas) > 1) {
    print(paste(filename,"has more than one unique leaf area."))} # If you had a typo somewhere it would show up as a unique leaf area
  if (2.5 %in% areas) {
    print("Leaf area is 2.5, did you enter the right leaf area?") # The default leaf area for our LI-6400 is 2.5, so if you didn't change the leaf area it would tell me
  }
  # Add column with filename
  dataframe <- dataframe %>%
    mutate(Filename = filename)
  # Return
  return(dataframe)
}

# OPTIONAL TEST ON ONE FILE
if (RUN_TEST == TRUE) {
  test <- read_licor(TEST_NAME)
  test
}

# Get list of files ending in .xlsx
file_list <- list.files(paste(getwd(), sep = ""), pattern="\\.xlsx") # Gets a list of files in the working directory
merged <- purrr::map_dfr(file_list, ~ read_licor(.)) %>% # Runs the function on all the files and merges them by row (stack on top of each other) using map_dfr
  dplyr::select(all_of(COLUMNS)) # select allows you to choose the columns you want
write.csv(merged,
          paste(getwd(), OUTPUT_NAME, sep = "/"),
          row.names = FALSE)

{% endhighlight %}
