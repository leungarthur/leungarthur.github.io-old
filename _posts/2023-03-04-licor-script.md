---
layout: post
title:  "An R script for reading LI-6400 data files into a dataframe"
---

The LI-6400XT is the one of the most common gas exchange instruments for studying photosynthesis. However, the format of the files that it outputs is not so friendly with common methods for data analysis.

It was time-consuming for me to do the following in Excel:
1. Take the columns of data that I'm interested in, e.g., the "Photo" column with CO<sub>2</sub> assimilation rate values.
2. Get rid of empty cells. The "Remark=" lines only have content in the first column, but adds empty cells for the rest of the row.
3. Merge data from multiple files together.

If you are reading this it is probably because I shared this with you. So you know what I'm talking about.

For my script, you have to open raw .xls files and save it as .xlsx, because `readxl` doesn't seem to be able to read the .xls ouputted by the LI-6400. Otherwise, I would just directly load the .xls files with the script.

I used `here::here()` to get the filepath to the location of the R script as a string (the directory in which the script is located). Arguments in this functions get appended to the path. This is useful because it works on macOS and Windows, and it allows the script to be portable. According to the developers of the package, it works better than setting a working directory.

I defined a function called `read_licor()`, which takes the path of the .xlsx file from a LI-6400 you want to read and outputs a dataframe with all the columns in the file.
- I removed the first 8 lines of the Li-Cor file with `skip = 8`. Those lines have metadata that isn't needed for most data analyses. The following row would then be the names of the columns which turn into the variables in the dataframe.
- `dplyr::slice(-1)` then removes the first line in that dataframe, which is a bunch of "in" and "out" strings.
- I got used `dplyr::filter()` to get rid of lines with "Remark=" in the Obs column. I don't use this function in the LI-6400, but you can keep this if you want them. Perhaps `dplyr::filter()` these lines into a separate dataframe. Then you can `stringr::word(Obs, 2, sep = fixed('='))` on those lines.
- I did a check for typos in the leaf area. Mistakes happen when you have to manually type and copy/paste leaf areas.
- I add a new variable called Filename. I personally put important information like the species and plant ID in the name of the data file when taking measurements, which allows me to discern where the data came from. It is not hard to manipulate the filename string to get the important information, e.g. with `strsplit()`, `dplyr::mutate()`, `stringr::word()`, etc.

The key function is `purrr::map_dfr`, which allows you to loop through a list and, in each iteration, run a function with the list item as an argument. It then binds the output of each run of the function.
Using `dplyr::select()`, the columns of interest are selected to include in the final .csv output.

{% highlight R %}

library(dplyr)

# SET WORKING DIRECTORY and CHOOSE COLUMNS OF INTEREST

RUN_TEST <- TRUE # Running a test on one file can save time, in case there are errors with the function
TEST_NAME <- "2022-10-18 AL pentaphylla 34_.xlsx"
COLUMNS <- c("Filename", "Obs", "Photo", "Ci", "Cond", "vp_kPa")
OUTPUT_NAME <- "merged.csv"

# DEFINE FUNCTION
# Takes the path of the .xlsx file from a LI-6400 you want to read, outputs a dataframe with all the columns in the file.
read_licor <- function(infile) {
  # Read .xlsx file
  dataframe <- readxl::read_xlsx(infile, skip = 8) %>% # skip the first 8 metadata lines
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
    mutate(Filename = infile)
  # Return
  return(dataframe)
}

# OPTIONAL TEST ON ONE FILE
if (RUN_TEST == TRUE) {
  test <- read_licor(TEST_NAME)
  test
}

# GET FILE LIST IN THE WORKING DIRECTORY, RUN OUR FUNCTION ON EACH FILE, AND MERGE THE OUTPUTS.
file_list <- list.files(paste(here::here(), sep = ""), pattern="\\.xlsx") # Gets a list of files in the working directory
merged <- purrr::map_dfr(file_list, ~ read_licor(.)) %>% # Runs the function on all the files and merges them by row (stack on top of each other) using map_dfr
  dplyr::select(all_of(COLUMNS)) # select allows you to choose the columns you want
write.csv(merged,
          paste(here::here(), OUTPUT_NAME, sep = "/"),
          row.names = FALSE)

{% endhighlight %}
