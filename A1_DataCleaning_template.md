Assignment 1, Language development in Autism Spectrum Disorder (ASD) - Brushing up your code skills
===================================================================================================

In this first part of the assignment we will brush up your programming
skills, and make you familiar with the data sets you will be analysing
for the next parts of the assignment.

In this warm-up assignment you will: 1) Create a Github (or gitlab)
account, link it to your RStudio, and create a new repository/project 2)
Use small nifty lines of code to transform several data sets into just
one. The final data set will contain only the variables that are needed
for the analysis in the next parts of the assignment 3) Warm up your
tidyverse skills (especially the sub-packages stringr and dplyr), which
you will find handy for later assignments.

N.B: Usually you’ll also have to doc/pdf with a text. Not for Assignment
1.

Learning objectives:
--------------------

-   Become comfortable with tidyverse (and R in general)
-   Test out the git integration with RStudio
-   Build expertise in data wrangling (which will be used in future
    assignments)

0. First an introduction on the data
------------------------------------

Language development in Autism Spectrum Disorder (ASD)
======================================================

Reference to the study:
<a href="https://www.ncbi.nlm.nih.gov/pubmed/30396129" class="uri">https://www.ncbi.nlm.nih.gov/pubmed/30396129</a>

Background: Autism Spectrum Disorder (ASD) is often related to language
impairment, and language impairment strongly affects the patients
ability to function socially (maintaining a social network, thriving at
work, etc.). It is therefore crucial to understand how language
abilities develop in children with ASD, and which factors affect them
(to figure out e.g. how a child will develop in the future and whether
there is a need for language therapy). However, language impairment is
always quantified by relying on the parent, teacher or clinician
subjective judgment of the child, and measured very sparcely (e.g. at 3
years of age and again at 6).

In this study we videotaped circa 30 kids with ASD and circa 30
comparison kids (matched by linguistic performance at visit 1) for ca.
30 minutes of naturalistic interactions with a parent. We repeated the
data collection 6 times per kid, with 4 months between each visit. We
transcribed the data and counted: i) the amount of words that each kid
uses in each video. Same for the parent. ii) the amount of unique words
that each kid uses in each video. Same for the parent. iii) the amount
of morphemes per utterance (Mean Length of Utterance) displayed by each
child in each video. Same for the parent.

Different researchers involved in the project provide you with different
datasets: 1) demographic and clinical data about the children (recorded
by a clinical psychologist) 2) length of utterance data (calculated by a
linguist) 3) amount of unique and total words used (calculated by a
fumbling jack-of-all-trade, let’s call him RF)

Your job in this assignment is to double check the data and make sure
that it is ready for the analysis proper (Assignment 2), in which we
will try to understand how the children’s language develops as they grow
as a function of cognitive and social factors and which are the “cues”
suggesting a likely future language impairment.

1. Let’s get started on GitHub
------------------------------

In the assignments you will be asked to upload your code on Github and
the GitHub repositories will be part of the portfolio, therefore all
students must make an account and link it to their RStudio (you’ll thank
us later for this!).

Follow the link to one of the tutorials indicated in the syllabus: \*
Recommended:
<a href="https://happygitwithr.com/" class="uri">https://happygitwithr.com/</a>
\* Alternative (if the previous doesn’t work):
<a href="https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN" class="uri">https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN</a>
\* Alternative (if the previous doesn’t work):
<a href="https://docs.google.com/document/d/1WvApy4ayQcZaLRpD6bvAqhWncUaPmmRimT016-PrLBk/mobilebasic" class="uri">https://docs.google.com/document/d/1WvApy4ayQcZaLRpD6bvAqhWncUaPmmRimT016-PrLBk/mobilebasic</a>

N.B. Create a GitHub repository for the Assignment 1 and link it to a
project on your RStudio.

2. Now let’s take dirty dirty data sets and make them into a tidy one
---------------------------------------------------------------------

If you’re not in a project in Rstudio, make sure to set your working
directory here. If you created an RStudio project, then your working
directory (the directory with your data and code for these assignments)
is the project directory.

``` r
pacman::p_load(tidyverse,janitor)
```

Load the three data sets, after downloading them from dropbox and saving
them in your working directory: \* Demographic data for the
participants:
<a href="https://www.dropbox.com/s/w15pou9wstgc8fe/demo_train.csv?dl=0" class="uri">https://www.dropbox.com/s/w15pou9wstgc8fe/demo_train.csv?dl=0</a>
\* Length of utterance data:
<a href="https://www.dropbox.com/s/usyauqm37a76of6/LU_train.csv?dl=0" class="uri">https://www.dropbox.com/s/usyauqm37a76of6/LU_train.csv?dl=0</a>
\* Word data:
<a href="https://www.dropbox.com/s/8ng1civpl2aux58/token_train.csv?dl=0" class="uri">https://www.dropbox.com/s/8ng1civpl2aux58/token_train.csv?dl=0</a>

``` r
demo <- read.csv("demo_train.csv")
lu <- read.csv("LU_train.csv")
token <- read.csv("token_train.csv")
```

Explore the 3 datasets (e.g. visualize them, summarize them, etc.). You
will see that the data is messy, since the psychologist collected the
demographic data, the linguist analyzed the length of utterance in May
2014 and the fumbling jack-of-all-trades analyzed the words several
months later. In particular: - the same variables might have different
names (e.g. participant and visit identifiers) - the same variables
might report the values in different ways (e.g. participant and visit
IDs) Welcome to real world of messy data :-)

Before being able to combine the data sets we need to make sure the
relevant variables have the same names and the same kind of values.

So:

2a. Identify which variable names do not match (that is are spelled
differently) and find a way to transform variable names. Pay particular
attention to the variables indicating participant and visit.

Tip: look through the chapter on data transformation in R for data
science
(<a href="http://r4ds.had.co.nz" class="uri">http://r4ds.had.co.nz</a>).
Alternatively you can look into the package dplyr (part of tidyverse),
or google “how to rename variables in R”. Or check the janitor R
package. There are always multiple ways of solving any problem and no
absolute best method.

``` r
names(demo)[1] <- "SUBJ"
names(demo)[2] <- "VISIT"
```

2b. Find a way to homogeneize the way “visit” is reported (visit1
vs. 1).

Tip: The stringr package is what you need. str\_extract () will allow
you to extract only the digit (number) from a string, by using the
regular expression \\d.

``` r
lu$VISIT <- str_extract(lu$VISIT,"\\d" )   #it takes out the letters and keeps only the digits in that column
token$VISIT <- str_extract(token$VISIT,"\\d" )
```

2c. We also need to make a small adjustment to the content of the
Child.ID coloumn in the demographic data. Within this column, names that
are not abbreviations do not end with “.” (i.e. Adam), which is the case
in the other two data sets (i.e. Adam.). If The content of the two
variables isn’t identical the rows will not be merged. A neat way to
solve the problem is simply to remove all “.” in all datasets.

Tip: stringr is helpful again. Look up str\_replace\_all Tip: You can
either have one line of code for each child name that is to be changed
(easier, more typing) or specify the pattern that you want to match
(more complicated: look up “regular expressions”, but less typing)

``` r
lu$SUBJ <- str_replace_all(lu$SUBJ, "[.]", "")
token$SUBJ <- str_replace_all(token$SUBJ, "[.]", "")
demo$SUBJ <- str_replace_all(demo$SUBJ, "[.]", "")
```

2d. Now that the nitty gritty details of the different data sets are
fixed, we want to make a subset of each data set only containig the
variables that we wish to use in the final data set. For this we use the
tidyverse package dplyr, which contains the function select().

The variables we need are: \* Child.ID, \* Visit, \* Diagnosis, \*
Ethnicity, \* Gender, \* Age, \* ADOS,  
\* MullenRaw, \* ExpressiveLangRaw, \* Socialization \* MOT\_MLU, \*
CHI\_MLU, \* types\_MOT, \* types\_CHI, \* tokens\_MOT, \* tokens\_CHI.

Most variables should make sense, here the less intuitive ones. \* ADOS
(Autism Diagnostic Observation Schedule) indicates the severity of the
autistic symptoms (the higher the score, the worse the symptoms). Ref:
<a href="https://link.springer.com/article/10.1023/A:1005592401947" class="uri">https://link.springer.com/article/10.1023/A:1005592401947</a>
\* MLU stands for mean length of utterance (usually a proxy for
syntactic complexity) \* types stands for unique words (e.g. even if
“doggie” is used 100 times it only counts for 1) \* tokens stands for
overall amount of words (if “doggie” is used 100 times it counts for
100) \* MullenRaw indicates non verbal IQ, as measured by Mullen Scales
of Early Learning (MSEL
<a href="https://link.springer.com/referenceworkentry/10.1007%2F978-1-4419-1698-3_596" class="uri">https://link.springer.com/referenceworkentry/10.1007%2F978-1-4419-1698-3_596</a>)
\* ExpressiveLangRaw indicates verbal IQ, as measured by MSEL \*
Socialization indicates social interaction skills and social
responsiveness, as measured by Vineland
(<a href="https://cloudfront.ualberta.ca/-/media/ualberta/faculties-and-programs/centres-institutes/community-university-partnership/resources/tools---assessment/vinelandjune-2012.pdf" class="uri">https://cloudfront.ualberta.ca/-/media/ualberta/faculties-and-programs/centres-institutes/community-university-partnership/resources/tools---assessment/vinelandjune-2012.pdf</a>)

Feel free to rename the variables into something you can remember
(i.e. nonVerbalIQ, verbalIQ)

``` r
sub_demo <- select(demo,SUBJ, VISIT,Ethnicity,Diagnosis,Gender,Age, ADOS, MullenRaw, ExpressiveLangRaw,Socialization)
sub_lu <- select(lu, SUBJ, VISIT, MOT_MLU, CHI_MLU)
sub_token <- select(token, SUBJ, VISIT,types_MOT, types_CHI,tokens_MOT,tokens_CHI)

names(sub_demo)[8] <- "nonverbalIQ"
names(sub_demo)[9] <- "verbalIQ"
```

2e. Finally we are ready to merge all the data sets into just one.

Some things to pay attention to: \* make sure to check that the merge
has included all relevant data (e.g. by comparing the number of rows) \*
make sure to understand whether (and if so why) there are NAs in the
dataset (e.g. some measures were not taken at all visits, some
recordings were lost or permission to use was withdrawn)

``` r
df <- merge(sub_demo,sub_lu)
df_all <- merge(df, sub_token)
```

2f. Only using clinical measures from Visit 1 In order for our models to
be useful, we want to miimize the need to actually test children as they
develop. In other words, we would like to be able to understand and
predict the children’s linguistic development after only having tested
them once. Therefore we need to make sure that our ADOS, MullenRaw,
ExpressiveLangRaw and Socialization variables are reporting (for all
visits) only the scores from visit 1.

A possible way to do so: \* create a new dataset with only visit 1,
child id and the 4 relevant clinical variables to be merged with the old
dataset \* rename the clinical variables (e.g. ADOS to ADOS1) and remove
the visit (so that the new clinical variables are reported for all 6
visits) \* merge the new dataset with the old

``` r
clinical <- df_all %>% 
  filter(VISIT==1) %>% 
  select(SUBJ, ADOS, nonverbalIQ, verbalIQ, Socialization)

names(clinical) <- c("SUBJ", "ADOS1", "nonverbalIQ1", "verbalIQ1", "Socialization1")

df_merged <- merge(df_all, clinical)
```

2g. Final touches

Now we want to \* anonymize our participants (they are real children!).
\* make sure the variables have sensible values. E.g. right now gender
is marked 1 and 2, but in two weeks you will not be able to remember,
which gender were connected to which number, so change the values from 1
and 2 to F and M in the gender variable. For the same reason, you should
also change the values of Diagnosis from A and B to ASD (autism spectrum
disorder) and TD (typically developing). Tip: Try taking a look at
ifelse(), or google “how to rename levels in R”. \* Save the data set
using into a csv file. Hint: look into write.csv()

``` r
df_merged$SUBJ <- as.factor(df_merged$SUBJ)
levels(df_merged$SUBJ) <- c(1:69)

df_merged$Gender <- as.factor(df_merged$Gender)
levels(df_merged$Gender) <- c("M", "F")

df_merged$Diagnosis <- as.factor(df_merged$Diagnosis)
levels(df_merged$Diagnosis)[levels(df_merged$Diagnosis)=="A"] <- "ASD"
levels(df_merged$Diagnosis)[levels(df_merged$Diagnosis)=="B"] <- "TD"

write.csv(df_merged, file="assignment1df.csv")
```

1.  BONUS QUESTIONS The aim of this last section is to make sure you are
    fully fluent in the tidyverse. Here’s the link to a very helpful
    book, which explains each function:
    <a href="http://r4ds.had.co.nz/index.html" class="uri">http://r4ds.had.co.nz/index.html</a>

2.  USING FILTER List all kids who:

<!-- -->

1.  have a mean length of utterance (across all visits) of more than 2.7
    morphemes.
2.  have a mean length of utterance of less than 1.5 morphemes at the
    first visit
3.  have not completed all trials. Tip: Use pipes to solve this

``` r
#1
filter1 <- df_merged %>%  group_by(SUBJ) %>% 
  summarize(mean(CHI_MLU)) %>% filter(`mean(CHI_MLU)`>2.7)

#2
filter2 <- filter(df_merged,VISIT ==1 & CHI_MLU <1.5)

#3 what do you mean by trials?
```

USING ARRANGE

1.  Sort kids to find the kid who produced the most words on the 6th
    visit
2.  Sort kids to find the kid who produced the least amount of words on
    the 1st visit.

``` r
#1
arrange(df_merged, desc(VISIT), desc(tokens_CHI))
```

    ##     SUBJ VISIT        Ethnicity Diagnosis Gender   Age ADOS nonverbalIQ
    ## 1     55     6            White        TD      M 41.93   NA          45
    ## 2     26     6            White       ASD      M 51.00   NA          46
    ## 3     18     6            White        TD      M 44.07   NA          50
    ## 4     25     6            White        TD      M 42.47   NA          48
    ## 5     43     6            White       ASD      M 57.37   NA          49
    ## 6     37     6            White       ASD      M 58.77   NA          50
    ## 7     13     6            White        TD      M 39.40   NA          47
    ## 8     22     6            White        TD      F 39.23   NA          43
    ## 9     14     6            White        TD      M 39.43   NA          44
    ## 10     4     6     White/Latino       ASD      M 51.37   NA          44
    ## 11    42     6            White        TD      M 40.13   NA          46
    ## 12    60     6            White       ASD      M 54.73   NA          50
    ## 13    16     6            White        TD      M 42.93   NA          49
    ## 14    41     6            White        TD      M 39.93   NA          45
    ## 15     2     6            White       ASD      M 49.70   NA          48
    ## 16     1     6            White        TD      M 40.13   NA          42
    ## 17    11     6            White        TD      M 40.27   NA          42
    ## 18    59     6            White        TD      M 41.00   NA          NA
    ## 19    49     6            White        TD      M 43.40   NA          45
    ## 20    12     6            White        TD      M 41.50   NA          44
    ## 21    58     6            White       ASD      M 46.07   NA          45
    ## 22    35     6            White        TD      M 39.07   NA          45
    ## 23    34     6            White       ASD      M 53.77   NA          44
    ## 24    20     6            White       ASD      M 37.30   NA          43
    ## 25    40     6         Lebanese       ASD      M 46.40   NA          41
    ## 26    36     6            White        TD      M 38.53   NA          46
    ## 27     5     6            White       ASD      M 54.13   NA          40
    ## 28    27     6            White        TD      M 41.17   NA          43
    ## 29    28     6            White        TD      M 39.43   NA          39
    ## 30    54     6            White        TD      M 39.30   NA          41
    ## 31     3     6            White        TD      F 45.07   NA          45
    ## 32    52     6            White        TD      M 43.03   NA          47
    ## 33    31     6            White        TD      M 43.80   NA          45
    ## 34    15     6            White        TD      M 40.30   NA          40
    ## 35    51     6            Asian        TD      F 42.10   NA          46
    ## 36    53     6            White        TD      M 44.43   NA          45
    ## 37    48     6            White       ASD      M    NA   NA          32
    ## 38     6     6      Bangladeshi       ASD      F 46.53   NA          39
    ## 39    19     6            White       ASD      M 56.73   NA          30
    ## 40    23     6     White/Latino       ASD      M 47.50   NA          30
    ## 41    61     6            White       ASD      M 62.33   NA          41
    ## 42    29     6            White       ASD      M 55.17   NA          39
    ## 43     8     6            White        TD      M 40.17   NA          43
    ## 44    30     6            White       ASD      M 56.43   NA          49
    ## 45    10     6            White        TD      M 40.43   NA          33
    ## 46    24     6            White       ASD      M 62.40   NA          30
    ## 47    46     6            White       ASD      M 57.43   NA          32
    ## 48    39     6      White/Asian       ASD      M 53.63   NA          30
    ## 49    32     6            White       ASD      M 54.63   NA          28
    ## 50    33     6 African American       ASD      F 46.17   NA          33
    ## 51    57     6            White       ASD      F 61.70   NA          32
    ## 52    47     6            White        TD      F 40.37   NA          43
    ## 53    17     6            White       ASD      M 54.43   NA          27
    ## 54    56     6            White       ASD      M    NA   NA          34
    ## 55    50     6            White       ASD      M 62.40   NA          24
    ## 56    21     6 African American       ASD      M 48.97   NA          26
    ## 57    26     5            White       ASD      M 46.93    4          NA
    ## 58    25     5            White        TD      M 37.93    0          NA
    ## 59    43     5            White       ASD      M 53.40   11          NA
    ## 60    13     5            White        TD      M 35.37    0          NA
    ## 61    12     5            White        TD      M 35.87    0          NA
    ## 62    42     5            White        TD      M 36.40    0          NA
    ## 63    37     5            White       ASD      M 55.17    4          NA
    ## 64    53     5            White        TD      M 40.07    0          NA
    ## 65    38     5            White        TD      F 36.13    0          NA
    ## 66    20     5            White       ASD      M 34.13    8          NA
    ## 67    18     5            White        TD      M 38.70    0          NA
    ## 68    14     5            White        TD      M 35.10    0          NA
    ## 69    35     5            White        TD      M 34.93    2          NA
    ## 70    54     5            White        TD      M 35.17    0          NA
    ## 71    52     5            White        TD      M 38.60    0          NA
    ## 72    31     5            White        TD      M 39.53    0          NA
    ## 73    22     5            White        TD      F 35.13    0          NA
    ## 74    41     5            White        TD      M 35.67    0          NA
    ## 75    59     5            White        TD      M 37.34    5          NA
    ## 76    28     5            White        TD      M 36.43    0          NA
    ## 77     3     5            White        TD      F 39.47    0          NA
    ## 78    49     5            White        TD      M 39.47    0          NA
    ## 79    16     5            White        TD      M 38.17    0          NA
    ## 80     4     5     White/Latino       ASD      M 47.40    9          NA
    ## 81    34     5            White       ASD      M 50.30   16          NA
    ## 82    58     5            White       ASD      M 42.30   16          NA
    ## 83    11     5            White        TD      M 35.63    0          NA
    ## 84    61     5            White       ASD      M 58.57   15          NA
    ## 85    45     5            White        TD      M 36.70   10          NA
    ## 86     1     5            White        TD      M 35.90    0          NA
    ## 87    36     5            White        TD      M 34.40    2          NA
    ## 88    44     5            White        TD      M 35.83    1          NA
    ## 89    10     5            White        TD      M 36.53    5          NA
    ## 90     5     5            White       ASD      M 49.30    9          NA
    ## 91    60     5            White       ASD      M 51.13   15          NA
    ## 92     6     5      Bangladeshi       ASD      F 42.43    8          NA
    ## 93     9     5            White        TD      F 35.00    1          NA
    ## 94    39     5      White/Asian       ASD      M 48.63   11          NA
    ## 95     8     5            White        TD      M 36.07    5          NA
    ## 96    27     5            White        TD      M 37.73    0          NA
    ## 97    47     5            White        TD      F 39.10   NA          NA
    ## 98    48     5            White       ASD      M 52.77   17          NA
    ## 99    51     5            Asian        TD      F 37.67    3          NA
    ## 100   19     5            White       ASD      M 51.97    9          NA
    ## 101   23     5     White/Latino       ASD      M 44.93   14          NA
    ## 102    7     5            White       ASD      F 57.73   17          NA
    ## 103   30     5            White       ASD      M 52.90   15          NA
    ## 104   29     5            White       ASD      M 50.97   20          NA
    ## 105   15     5            White        TD      M 35.90    3          NA
    ## 106   33     5 African American       ASD      F 42.97   25          NA
    ## 107   24     5            White       ASD      M 57.20   17          NA
    ## 108   32     5            White       ASD      M 50.73   20          NA
    ## 109   46     5            White       ASD      M 54.33   17          NA
    ## 110   57     5            White       ASD      F 58.27   13          NA
    ## 111   17     5            White       ASD      M 50.93   15          NA
    ## 112   21     5 African American       ASD      M 44.70   19          NA
    ## 113   50     5            White       ASD      M 57.20   17          NA
    ## 114   25     4            White        TD      M 34.00   NA          47
    ## 115   18     4            White        TD      M 34.43   NA          50
    ## 116   55     4            White        TD      M 32.03   NA          40
    ## 117   53     4            White        TD      M 36.40   NA          45
    ## 118   47     4            White        TD      F 32.13   NA          42
    ## 119   43     4            White       ASD      M 49.30   NA          50
    ## 120   42     4            White        TD      M 32.07   NA          39
    ## 121   16     4            White        TD      M 34.27   NA          45
    ## 122    3     4            White        TD      F 35.53   NA          39
    ## 123   31     4            White        TD      M 35.03   NA          43
    ## 124   20     4            White       ASD      M 30.80   NA          43
    ## 125   26     4            White       ASD      M 42.63   NA          41
    ## 126   54     4            White        TD      M 30.83   NA          39
    ## 127   60     4            White       ASD      M 47.00   NA          47
    ## 128   49     4            White        TD      M 35.63   NA          45
    ## 129    2     4            White       ASD      M 41.07   NA          49
    ## 130   52     4            White        TD      M 35.03   NA          45
    ## 131   59     4            White        TD      M 33.60   NA          34
    ## 132    5     4            White       ASD      M 45.53   NA          38
    ## 133   22     4            White        TD      F 30.93   NA          40
    ## 134   38     4            White        TD      F 32.17   NA          44
    ## 135   58     4            White       ASD      M 38.20   NA          40
    ## 136   14     4            White        TD      M 31.47   NA          29
    ## 137   13     4            White        TD      M 31.03   NA          40
    ## 138   37     4            White       ASD      M 51.67   NA          49
    ## 139   28     4            White        TD      M 31.00   NA          28
    ## 140   15     4            White        TD      M 31.27   NA          33
    ## 141    7     4            White       ASD      F 52.27   NA          29
    ## 142    8     4            White        TD      M 32.07   NA          42
    ## 143   40     4         Lebanese       ASD      M    NA   NA          NA
    ## 144    4     4     White/Latino       ASD      M 43.13   NA          41
    ## 145   36     4            White        TD      M 30.40   NA          35
    ## 146   19     4            White       ASD      M 47.83   NA          35
    ## 147   34     4            White       ASD      M 46.13   NA          31
    ## 148   41     4            White        TD      M 32.07   NA          41
    ## 149   45     4            White        TD      M 32.77   NA          26
    ## 150   10     4            White        TD      M 32.43   NA          31
    ## 151    9     4            White        TD      F 31.07   NA          45
    ## 152   27     4            White        TD      M 33.30   NA          42
    ## 153   44     4            White        TD      M 32.07   NA          40
    ## 154   11     4            White        TD      M 31.20   NA          35
    ## 155   51     4            Asian        TD      F 34.43   NA          30
    ## 156   61     4            White       ASD      M 54.13   NA          28
    ## 157    1     4            White        TD      M 32.90   NA          33
    ## 158   12     4            White        TD      M 32.07   NA          42
    ## 159    6     4      Bangladeshi       ASD      F 38.47   NA          33
    ## 160   23     4     White/Latino       ASD      M 42.23   NA          29
    ## 161   24     4            White       ASD      M 52.13   NA          29
    ## 162   33     4 African American       ASD      F 36.93   NA          36
    ## 163   39     4      White/Asian       ASD      M 45.33   NA          45
    ## 164   21     4 African American       ASD      M 40.27   NA          27
    ## 165   30     4            White       ASD      M 48.10   NA          47
    ## 166   35     4            White        TD      M 30.80   NA          36
    ## 167   50     4            White       ASD      M 52.13   NA          22
    ## 168   46     4            White       ASD      M 50.23   NA          29
    ## 169   48     4            White       ASD      M 43.63   NA          33
    ## 170   29     4            White       ASD      M 46.63   NA          27
    ## 171   32     4            White       ASD      M 47.10   NA          27
    ## 172   57     4            White       ASD      F 53.90   NA          30
    ## 173   26     3            White       ASD      M 38.60   NA          NA
    ## 174   31     3            White        TD      M 32.07   NA          NA
    ## 175   43     3            White       ASD      M 46.23   NA          NA
    ## 176    2     3            White       ASD      M 37.07   NA          NA
    ## 177   18     3            White        TD      M 30.03   NA          NA
    ## 178   52     3            White        TD      M 30.77   NA          NA
    ## 179   25     3            White        TD      M 30.13   NA          NA
    ## 180   16     3            White        TD      M 30.63   NA          NA
    ## 181   38     3            White        TD      F 28.07   NA          NA
    ## 182   12     3            White        TD      M 28.27   NA          NA
    ## 183   35     3            White        TD      M 27.13   NA          NA
    ## 184   60     3            White       ASD      M 42.47   NA          NA
    ## 185   37     3            White       ASD      M 47.73   NA          NA
    ## 186   55     3            White        TD      M 28.03   NA          NA
    ## 187   49     3            White        TD      M 31.63   NA          NA
    ## 188   53     3            White        TD      M 32.50   NA          NA
    ## 189   13     3            White        TD      M 27.07   NA          NA
    ## 190   42     3            White        TD      M 28.90   NA          NA
    ## 191    3     3            White        TD      F 31.83   NA          NA
    ## 192   44     3            White        TD      M 27.67   NA          NA
    ## 193   20     3            White       ASD      M 26.77   NA          NA
    ## 194    4     3     White/Latino       ASD      M 38.90   NA          NA
    ## 195   47     3            White        TD      F 29.53   NA          NA
    ## 196    5     3            White       ASD      M 41.57   NA          NA
    ## 197    9     3            White        TD      F 26.63   NA          NA
    ## 198   59     3            White        TD      M 29.67   NA          NA
    ## 199   40     3         Lebanese       ASD      M 34.00   NA          NA
    ## 200   28     3            White        TD      M 27.03   NA          NA
    ## 201   58     3            White       ASD      M 34.27   NA          NA
    ## 202   14     3            White        TD      M 26.80   NA          NA
    ## 203   34     3            White       ASD      M 43.00   NA          NA
    ## 204   45     3            White        TD      M 28.63   NA          NA
    ## 205   41     3            White        TD      M 28.47   NA          NA
    ## 206   19     3            White       ASD      M 43.90   NA          NA
    ## 207   22     3            White        TD      F 26.67   NA          NA
    ## 208   27     3            White        TD      M 29.07   NA          NA
    ## 209   54     3            White        TD      M 27.07   NA          NA
    ## 210    1     3            White        TD      M 27.70   NA          NA
    ## 211   61     3            White       ASD      M 50.63   NA          NA
    ## 212   15     3            White        TD      M 28.10   NA          NA
    ## 213   39     3      White/Asian       ASD      M 41.73   NA          NA
    ## 214   36     3            White        TD      M 26.87   NA          NA
    ## 215    7     3            White       ASD      F 49.20   NA          NA
    ## 216   11     3            White        TD      M 27.27   NA          NA
    ## 217   10     3            White        TD      M 28.53   NA          NA
    ## 218   30     3            White       ASD      M 44.67   NA          NA
    ## 219   23     3     White/Latino       ASD      M 35.93   NA          NA
    ## 220   51     3            Asian        TD      F 29.67   NA          NA
    ## 221   21     3 African American       ASD      M 36.17   NA          NA
    ## 222   50     3            White       ASD      M 46.37   NA          NA
    ## 223    6     3      Bangledeshi       ASD      F 34.57   NA          NA
    ## 224   17     3            White       ASD      M    NA   NA          NA
    ## 225   57     3            White       ASD      F 49.97   NA          NA
    ## 226   24     3            White       ASD      M 46.37   NA          NA
    ## 227   29     3            White       ASD      M 43.10   NA          NA
    ## 228   33     3 African American       ASD      F 34.10   NA          NA
    ## 229   56     3            White       ASD      M 44.47   NA          NA
    ## 230   46     3            White       ASD      M 45.57   NA          NA
    ## 231   32     3            White       ASD      M 42.13   NA          NA
    ## 232   35     2            White        TD      M 23.43   NA          NA
    ## 233   18     2            White        TD      M 26.13   NA          NA
    ## 234   16     2            White        TD      M 26.27   NA          NA
    ## 235   60     2            White       ASD      M 38.63   NA          NA
    ## 236   25     2            White        TD      M 25.47   NA          NA
    ## 237   47     2            White        TD      F 24.40   NA          NA
    ## 238    2     2            White       ASD      M 33.17   NA          NA
    ## 239    4     2     White/Latino       ASD      M 35.30   NA          NA
    ## 240    8     2            White        TD      M 24.07   NA          NA
    ## 241   31     2            White        TD      M 27.70   NA          NA
    ## 242   43     2            White       ASD      M 42.77   NA          NA
    ## 243    3     2            White        TD      F 27.83   NA          NA
    ## 244    5     2            White       ASD      M 37.57   NA          NA
    ## 245   53     2            White        TD      M 28.60   NA          NA
    ## 246   38     2            White        TD      F 24.17   NA          NA
    ## 247   37     2            White       ASD      M 43.68   NA          NA
    ## 248   42     2            White        TD      M    NA   NA          NA
    ## 249   22     2            White        TD      F 22.90   NA          NA
    ## 250   59     2            White        TD      M 25.87   NA          NA
    ## 251   13     2            White        TD      M 22.97   NA          NA
    ## 252   34     2            White       ASD      M 38.13   NA          NA
    ## 253   20     2            White       ASD      M 22.50   NA          NA
    ## 254   49     2            White        TD      M 27.47   NA          NA
    ## 255   61     2            White       ASD      M 46.77   NA          NA
    ## 256   19     2            White       ASD      M 39.93   NA          NA
    ## 257   52     2            White        TD      M 26.63   NA          NA
    ## 258   14     2            White        TD      M 22.87   NA          NA
    ## 259   54     2            White        TD      M 23.17   NA          NA
    ## 260   41     2            White        TD      M 23.07   NA          NA
    ## 261   58     2            White       ASD      M 30.10   NA          NA
    ## 262   28     2            White        TD      M 23.57   NA          NA
    ## 263   55     2            White        TD      M 23.93   NA          NA
    ## 264   27     2            White        TD      M 25.23   NA          NA
    ## 265   12     2            White        TD      M 23.83   NA          NA
    ## 266   29     2            White       ASD      M 38.13   NA          NA
    ## 267    7     2            White       ASD      F 49.20   NA          NA
    ## 268   45     2            White        TD      M 24.68   NA          NA
    ## 269   15     2            White        TD      M 24.80   NA          NA
    ## 270   23     2     White/Latino       ASD      M 32.10   NA          NA
    ## 271    6     2      Bangladeshi       ASD      F 30.27   NA          NA
    ## 272    1     2            White        TD      M 23.93   NA          NA
    ## 273   40     2         Lebanese       ASD      M 32.20   NA          NA
    ## 274   50     2            White       ASD      M 42.20   NA          NA
    ## 275    9     2            White        TD      F 23.73   NA          NA
    ## 276   39     2      White/Asian       ASD      M 37.37   NA          NA
    ## 277   44     2            White        TD      M 23.63   NA          NA
    ## 278   11     2            White        TD      M    NA   NA          NA
    ## 279   30     2            White       ASD      M 40.50   NA          NA
    ## 280   36     2            White        TD      M 22.87   NA          NA
    ## 281   51     2            Asian        TD      F 25.40   NA          NA
    ## 282   17     2            White       ASD      M 38.73   NA          NA
    ## 283   21     2 African American       ASD      M 31.80   NA          NA
    ## 284   10     2            White        TD      M 23.83   NA          NA
    ## 285   57     2            White       ASD      F 41.07   NA          NA
    ## 286   46     2            White       ASD      M 41.50   NA          NA
    ## 287   24     2            White       ASD      M 42.20   NA          NA
    ## 288   56     2            White       ASD      M 39.40   NA          NA
    ## 289   33     2 African American       ASD      F 29.70   NA          NA
    ## 290   32     2            White       ASD      M 38.33   NA          NA
    ## 291   48     2            White       ASD      M 37.20   NA          NA
    ## 292   26     1            White       ASD      M 30.40   11          32
    ## 293   53     1            White        TD      M 23.90    0          30
    ## 294   60     1            White       ASD      M 34.00   13          30
    ## 295    2     1            White       ASD      M 28.80   13          34
    ## 296   43     1            White       ASD      M 37.03   14          42
    ## 297   25     1            White        TD      M 21.77    0          29
    ## 298   18     1            White        TD      M 20.77    0          29
    ## 299   38     1            White        TD      F 19.87    1          29
    ## 300   37     1            White       ASD      M 39.50    7          33
    ## 301   31     1            White        TD      M 23.13    0          27
    ## 302   52     1            White        TD      M 22.57    0          29
    ## 303   49     1            White        TD      M 23.07    0          27
    ## 304    4     1     White/Latino       ASD      M 31.03    8          31
    ## 305    3     1            White        TD      F 23.50    1          29
    ## 306   35     1            White        TD      M 19.30    1          23
    ## 307   16     1            White        TD      M 21.67    0          29
    ## 308   13     1            White        TD      M 19.00    0          25
    ## 309   59     1            White        TD      M 20.80    4          29
    ## 310   41     1            White        TD      M 19.23    0          25
    ## 311   58     1            White       ASD      M 26.00   15          30
    ## 312   19     1            White       ASD      M 35.80   11          28
    ## 313   20     1            White       ASD      M 18.77    9          26
    ## 314   47     1            White        TD      F 20.03    0          27
    ## 315    5     1            White       ASD      M 34.03    9          34
    ## 316   34     1            White       ASD      M 33.77   10          27
    ## 317   42     1            White        TD      M 19.37    0          24
    ## 318   61     1            White       ASD      M 42.00   15          27
    ## 319   55     1            White        TD      M 19.97    0          26
    ## 320    8     1            White        TD      M 20.10    5          32
    ## 321    1     1            White        TD      M 19.80    0          28
    ## 322    9     1            White        TD      F 18.30    0          24
    ## 323    7     1            White       ASD      F 41.00   18          24
    ## 324   21     1 African American       ASD      M 27.53   21          22
    ## 325   22     1            White        TD      F 18.93    0          21
    ## 326   29     1            White       ASD      M 34.87   17          26
    ## 327   28     1            White        TD      M 19.93    0          20
    ## 328   39     1      White/Asian       ASD      M 33.20   11          26
    ## 329   54     1            White        TD      M 19.10    1          24
    ## 330   14     1            White        TD      M 18.97    0          23
    ## 331   23     1     White/Latino       ASD      M 27.37   14          25
    ## 332   11     1            White        TD      M 19.23    0          21
    ## 333   15     1            White        TD      M 19.27    0          24
    ## 334   27     1            White        TD      M 21.03    0          26
    ## 335   45     1            White        TD      M 20.03    5          24
    ## 336   10     1            White        TD      M 19.27    3          27
    ## 337   44     1            White        TD      M 19.77    3          30
    ## 338   46     1            White       ASD      M 36.73   20          21
    ## 339   12     1            White        TD      M 20.07    0          30
    ## 340   40     1         Lebanese       ASD      M 24.90   13          27
    ## 341   56     1            White       ASD      M 35.50   14          27
    ## 342   50     1            White       ASD      M 37.47   19          17
    ## 343   48     1            White       ASD      M 31.63   17          28
    ## 344   51     1            Asian        TD      F 20.87    1          22
    ## 345   17     1            White       ASD      M 34.80   14          25
    ## 346   33     1 African American       ASD      F 25.33   14          25
    ## 347    6     1      Bangladeshi       ASD      F 26.17   17          20
    ## 348   24     1            White       ASD      M 37.47   20          13
    ## 349   30     1            White       ASD      M 36.53   12          31
    ## 350   36     1            White        TD      M 19.20    3          19
    ## 351   32     1            White       ASD      M 34.27   21          21
    ## 352   57     1            White       ASD      F 41.07   15          28
    ##     verbalIQ Socialization  MOT_MLU   CHI_MLU types_MOT types_CHI
    ## 1         38            95 3.957230 2.9092742       367       260
    ## 2         46            90 4.111413 3.3643411       452       273
    ## 3         50           116 4.013353 2.9095355       516       235
    ## 4         39           103 4.472993 3.0614525       555       237
    ## 5         45            77 4.250853 2.6794872       374       219
    ## 6         48           116 4.240798 3.0774194       429       217
    ## 7         41            92 4.847418 3.7011952       388       210
    ## 8         40           110 4.211321 3.0911950       478       221
    ## 9         37            83 4.664286 3.8114035       359       178
    ## 10        44            74 3.532374 3.2781955       410       166
    ## 11        46           101 3.391525 3.0727969       357       219
    ## 12        50            97 4.080446 3.4415584       505       226
    ## 13        46            97 4.445872 2.9482072       491       250
    ## 14        36           101 4.061475 2.8526316       397       213
    ## 15        48            81 4.588477 3.4135021       304       245
    ## 16        44           100 4.664013 2.8651685       595       210
    ## 17        27           101 4.287582 2.7571429       260       168
    ## 18        NA            NA 4.113158 2.8480000       303       158
    ## 19        48           101 5.247093 3.5950000       383       217
    ## 20        47            97 4.468493 3.5049505       339       201
    ## 21        30            79 3.320000 2.1553398       335       197
    ## 22        34            97 4.239198 2.3815789       411       156
    ## 23        32            92 3.370937 2.2745098       342       157
    ## 24        48           103 5.379798 2.9027778       433       155
    ## 25        37            94 3.926928 2.1586207       417       170
    ## 26        31            97 4.388235 2.5614754       324       165
    ## 27        37            75 4.587179 2.7665198       462       179
    ## 28        35           106 4.254505 2.4800000       385       154
    ## 29        32            95 4.239437 2.9607843       391       164
    ## 30        41           101 4.186161 2.8921569       437       183
    ## 31        45            86 5.229885 3.7103448       486       173
    ## 32        44            97 5.587332 2.4292929       548       163
    ## 33        45           108 3.603473 2.0717489       475       185
    ## 34        40            94 4.347709 2.8690476       327       156
    ## 35        40           108 5.153639 2.7612903       383       140
    ## 36        36           101 3.706790 3.2439024       249       102
    ## 37        21            63 3.370093 1.4734513       319        98
    ## 38        26            74 2.483146 1.4967320       158        66
    ## 39        32            72 3.156695 2.1450382       256        55
    ## 40        18            65 3.534173 1.3052632       372        47
    ## 41        31           103 3.514403 1.4012346       311        58
    ## 42        21            72 4.349353 1.2883436       454        52
    ## 43        39           108 4.366972 2.2258065       322        64
    ## 44        22            85 4.341549 1.0843373       425       101
    ## 45        33           103 4.235585 2.7051282       400        73
    ## 46        12            68 3.860795 1.1680000       309        62
    ## 47        10            59 3.460548 1.1610169       351        10
    ## 48        29            63 4.100707 1.6447368       330        55
    ## 49        10            66 4.003257 2.6315789       585        12
    ## 50        18            83 3.965392 0.7536232       326        14
    ## 51         9            68 3.241422 0.0156250       444         2
    ## 52        46           101 4.676101 2.7600000       322        34
    ## 53        11            65 3.650235 1.0000000       281         3
    ## 54        17            NA 3.474725 1.0588235       284         6
    ## 55        11            66 3.886842 1.3333333       370         4
    ## 56        16            65 3.943636 0.5000000       388         2
    ## 57        NA            97 4.345515 3.2919897       437       261
    ## 58        NA           101 4.267409 2.7413793       578       298
    ## 59        NA            77 4.232258 2.8665049       396       262
    ## 60        NA           100 4.487842 3.2301136       511       247
    ## 61        NA           101 4.910494 4.3647541       290       222
    ## 62        NA           100 3.921444 3.7749077       358       213
    ## 63        NA           105 4.658802 2.7468750       407       228
    ## 64        NA           103 4.857143 3.8225806       278       217
    ## 65        NA           120 4.917927 3.5185185       447       210
    ## 66        NA           107 4.837884 2.2506739       506       219
    ## 67        NA           118 3.877102 3.0902778       575       219
    ## 68        NA            87 4.446281 3.3750000       390       238
    ## 69        NA           103 4.069659 2.5541796       436       161
    ## 70        NA           105 3.750000 3.6072874       394       244
    ## 71        NA            94 4.983389 2.8321678       563       219
    ## 72        NA           106 3.906856 2.1420613       491       211
    ## 73        NA           114 4.746606 3.7000000       436       178
    ## 74        NA           105 3.969147 2.2892308       415       187
    ## 75        NA           116 4.424837 2.6038462       341       132
    ## 76        NA           100 3.708934 3.5852535       272       177
    ## 77        NA            86 4.566038 3.2985782       534       207
    ## 78        NA           101 5.743772 3.5371429       425       209
    ## 79        NA           103 5.433579 3.1095238       517       219
    ## 80        NA            79 4.602851 4.0434783       397       146
    ## 81        NA            90 3.419664 2.5758929       298       120
    ## 82        NA            83 3.290683 1.7591463       360       158
    ## 83        NA           100 4.543210 2.5482456       276       148
    ## 84        NA            95 2.965928 1.6688963       320       122
    ## 85        NA           122 4.329810 2.2558140       418       142
    ## 86        NA           107 5.209615 3.2380952       601       182
    ## 87        NA            91 4.137405 2.2787611       366       145
    ## 88        NA           107 4.113846 3.2000000       307       131
    ## 89        NA           103 3.867601 2.2835821       359        97
    ## 90        NA            75 4.292135 2.4232804       418       144
    ## 91        NA            92 4.223572 2.9655172       521       145
    ## 92        NA            68 1.948454 1.6363636        92        80
    ## 93        NA           107 4.384946 2.8358209       428       121
    ## 94        NA            63 2.902089 1.6511628       321        90
    ## 95        NA           100 4.564103 1.9408602       387        40
    ## 96        NA           112 3.479393 2.1791908       327       114
    ## 97        NA            95 3.673418 2.7388060       315       131
    ## 98        NA            68 3.162554 1.4102564       369        79
    ## 99        NA           112 5.185941 2.9215686       388       108
    ## 100       NA            72 4.157191 1.5840000       328        45
    ## 101       NA            65 2.822259 1.4133333       306        50
    ## 102       NA            63 3.992095 1.5299145       234        73
    ## 103       NA            77 3.859624 0.4130435       465        42
    ## 104       NA            68 4.872014 0.2553191       415         9
    ## 105       NA            86 4.165414 2.4691358       356        69
    ## 106       NA            85 4.185417 0.6756757       383         9
    ## 107       NA            72 4.421222 1.0000000       303         4
    ## 108       NA            66 3.414894 1.0000000       355         3
    ## 109       NA            63 3.637965 1.0000000       344         1
    ## 110       NA            66 3.693846 0.2000000       435         3
    ## 111       NA            66 3.996071 1.0000000       475         6
    ## 112       NA            72 4.019231 0.2000000       373         2
    ## 113       NA            68 3.864407 0.0000000       364         0
    ## 114       NA           102 5.362500 3.7310924       553       291
    ## 115       NA           105 4.131579 3.2128205       565       207
    ## 116       NA            93 4.177340 2.8620690       318       205
    ## 117       NA           108 4.458937 3.6340694       232       217
    ## 118       NA            93 4.190698 3.1622419       343       211
    ## 119       NA            75 3.905213 2.6837838       390       243
    ## 120       NA           109 4.611247 3.8300395       339       193
    ## 121       NA           100 4.251605 2.3225806       508       231
    ## 122       NA            93 5.301053 3.9292035       449       206
    ## 123       NA           107 3.667638 2.7272727       480       186
    ## 124       NA            98 4.560201 2.2485380       422       153
    ## 125       NA            86 4.857143 3.5238095       398       188
    ## 126       NA           105 3.819961 3.4065041       332       153
    ## 127       NA           100 4.085409 3.3598326       539       214
    ## 128       NA           105 4.880240 3.4809524       340       229
    ## 129       NA            75 5.309804 4.3023256       335       200
    ## 130       NA           100 5.338462 3.0775510       554       177
    ## 131       NA           116 4.697624 2.7148289       330       157
    ## 132       NA            77 4.744000 2.9072581       384       187
    ## 133       NA           109 3.991667 2.3740741       480       169
    ## 134       NA           125 4.169162 2.6475410       513       195
    ## 135       NA            83 3.471510 2.0127796       359       144
    ## 136       NA            96 3.801292 2.3178295       369       158
    ## 137       NA           107 4.083700 2.7982833       463       203
    ## 138       NA           101 5.050000 2.5159817       410       148
    ## 139       NA            96 3.683406 2.3500000       309       139
    ## 140       NA            87 4.584071 2.5613208       357       161
    ## 141       NA            61 2.736842 1.9115646       198       126
    ## 142       NA           109 4.658333 3.0265957       375       134
    ## 143       NA            86 3.812930 2.9900000       430       180
    ## 144       NA            77 4.301624 3.2571429       356       163
    ## 145       NA           100 3.705441 2.2594142       295       136
    ## 146       NA            74 2.762463 1.6179402       248        87
    ## 147       NA            92 3.997126 2.1962617       321       107
    ## 148       NA           100 4.118790 2.2325581       366       151
    ## 149       NA           114 4.019928 1.7600000       424       108
    ## 150       NA           103 3.790896 1.6479401       411        96
    ## 151       NA           103 4.262195 2.7756410       400       121
    ## 152       NA           100 3.973469 1.7405858       364       114
    ## 153       NA            95 4.256790 2.5370370       302       122
    ## 154       NA           100 4.732733 2.1348315       272        88
    ## 155       NA           120 4.137014 3.0289855       363       112
    ## 156       NA            92 3.312388 1.3204225       348        61
    ## 157       NA           102 4.415330 2.2515723       533       133
    ## 158       NA           103 3.988304 3.0000000       197       122
    ## 159       NA            66 2.035714 1.7142857       125        50
    ## 160       NA            63 3.012594 1.0974026       270        21
    ## 161       NA            72 3.814815 1.2032520       353        29
    ## 162       NA            86 3.110932 1.2100840       281        18
    ## 163       NA            66 3.714556 1.7000000       302        36
    ## 164       NA            68 3.600624 0.2727273       427        10
    ## 165       NA            74 3.961832 1.1250000       409        26
    ## 166       NA           105 4.001613 1.9743590       361        29
    ## 167       NA            68 4.009934 1.0000000       358         1
    ## 168       NA            55 2.377171 1.1200000       203        11
    ## 169       NA            68 3.339114 1.0000000       280         1
    ## 170       NA            72 3.885113 0.8947368       361        13
    ## 171       NA            72 3.743333 1.0000000       352         6
    ## 172       NA            68 3.536415 0.0000000       400         1
    ## 173       NA            83 4.316279 3.9196891       333       307
    ## 174       NA           107 3.552459 2.9875260       396       233
    ## 175       NA            70 3.573574 2.3053892       331       221
    ## 176       NA            77 4.147059 3.1193182       351       262
    ## 177       NA           112 4.116057 3.3400000       487       201
    ## 178       NA           103 5.288991 3.3037543       474       200
    ## 179       NA           100 4.127941 2.8042169       455       217
    ## 180       NA            95 4.164789 2.8076923       408       200
    ## 181       NA           125 4.577491 2.8690476       420       175
    ## 182       NA           105 4.974684 3.1857708       318       169
    ## 183       NA           112 3.675134 2.5710145       366       155
    ## 184       NA           100 4.907591 4.1318681       459       196
    ## 185       NA           100 3.642412 1.8812665       330       175
    ## 186       NA            91 4.231362 2.4932432       292       168
    ## 187       NA           105 4.737127 3.1124498       323       151
    ## 188       NA           107 4.078292 3.1313559       219       152
    ## 189       NA           109 3.871968 2.0798817       435       139
    ## 190       NA           109 4.057047 2.4911032       367       155
    ## 191       NA            91 5.134892 2.6916300       482       164
    ## 192       NA           100 4.106212 2.2313433       300       102
    ## 193       NA            95 4.063518 1.6109325       361       132
    ## 194       NA            81 3.818681 3.5180723       388       165
    ## 195       NA            98 3.960265 2.2893617       364       149
    ## 196       NA            77 4.198973 2.5324074       412       159
    ## 197       NA           105 4.592593 1.8320312       369       129
    ## 198       NA           116 4.210407 2.1157895       257       101
    ## 199       NA            87 3.723664 2.0704225       346       134
    ## 200       NA           100 3.210084 1.8369099       233       131
    ## 201       NA            86 3.774030 1.8000000       346       153
    ## 202       NA            91 3.848175 1.1855072       378        92
    ## 203       NA            92 3.866834 1.8056872       318       100
    ## 204       NA           112 3.395899 1.1722689       370        95
    ## 205       NA           103 4.050505 1.9086294       304       127
    ## 206       NA            79 3.615176 1.9052632       298        88
    ## 207       NA           114 3.094880 1.4549550       432       112
    ## 208       NA            98 3.846626 1.4976077       318        89
    ## 209       NA           107 4.298667 2.3309353       299        85
    ## 210       NA           109 4.321881 1.5568862       455        97
    ## 211       NA            94 4.520325 1.5828571       313        70
    ## 212       NA            86 4.182979 1.4408602       409        92
    ## 213       NA            63 3.607664 1.8880000       327        51
    ## 214       NA           103 4.595568 1.7500000       323        76
    ## 215       NA            63 2.633929 1.3974359       196        50
    ## 216       NA           102 3.940711 1.5234375       227        56
    ## 217       NA           111 4.298942 1.5520000       409        55
    ## 218       NA            79 4.015699 1.0066667       430        21
    ## 219       NA            70 3.215859 1.0863309       259        29
    ## 220       NA           105 4.022624 1.5529412       333        56
    ## 221       NA            77 3.646778 2.0000000       434        13
    ## 222       NA            72 3.979950 1.1666667       368        27
    ## 223       NA            70 1.856115 1.1698113        74        12
    ## 224       NA            68 2.919169 1.0000000       252         2
    ## 225       NA            65 4.145588 0.1621622       542         3
    ## 226       NA            74 3.910448 1.0000000       344         1
    ## 227       NA            72 4.382550 0.6842105       345        13
    ## 228       NA            74 3.404959 1.0833333       272         3
    ## 229       NA            70 2.963303 1.7500000       252         9
    ## 230       NA            61 2.821782 1.0000000       245         2
    ## 231       NA            68 3.990826 1.0000000       323         1
    ## 232       NA           105 3.636519 1.9342916       300       128
    ## 233       NA           107 4.357911 2.7220339       461       160
    ## 234       NA           102 4.750455 2.7441077       391       196
    ## 235       NA            46 4.604341 2.7840000       413       149
    ## 236       NA           108 4.600000 2.4847328       419       145
    ## 237       NA           100 4.183445 2.2481481       295       128
    ## 238       NA           105 4.964664 3.4530387       307       171
    ## 239       NA           115 4.130337 2.5630252       343       170
    ## 240       NA           102 3.967213 1.6019108       305       122
    ## 241       NA           103 4.222034 2.2481481       405       141
    ## 242       NA            72 3.505582 1.9832215       309       170
    ## 243       NA            89 4.572086 2.6418605       464       149
    ## 244       NA            94 4.886646 2.5743590       441       152
    ## 245       NA            86 4.557471 3.2179487       197       126
    ## 246       NA           116 4.073282 2.1727273       435       142
    ## 247       NA            83 4.054054 1.3432836       416       100
    ## 248       NA           105 4.100167 2.0046729       368       124
    ## 249       NA           102 3.978599 1.7804878       392       121
    ## 250       NA           118 4.001724 1.7709251       334       120
    ## 251       NA           100 3.817109 1.4761905       411       119
    ## 252       NA            74 2.667722 1.9481132       220       100
    ## 253       NA            84 2.841004 1.0800000       288        73
    ## 254       NA            70 4.745238 2.4967742       408       126
    ## 255       NA            79 3.302663 1.0990991       250        48
    ## 256       NA           102 3.170000 1.4867257       315        86
    ## 257       NA            81 4.567329 1.7359551       415       122
    ## 258       NA           102 3.239832 1.0744681       346        65
    ## 259       NA            59 3.609881 2.0466667       352        88
    ## 260       NA            92 3.751332 1.1974249       404        97
    ## 261       NA            72 3.372320 1.4432990       260        78
    ## 262       NA            76 3.367292 1.6557377       255        71
    ## 263       NA            96 3.789634 1.7902098       219        70
    ## 264       NA            98 3.869654 1.4228571       287        72
    ## 265       NA           115 3.444664 1.3647799       297        72
    ## 266       NA            68 4.003072 1.0114286       331        18
    ## 267       NA            63 2.417344 1.3211679       186        42
    ## 268       NA           118 3.171717 0.9051724       288        41
    ## 269       NA            86 4.224839 1.0460526       363        25
    ## 270       NA            65 2.903723 1.4273504       301        61
    ## 271       NA            74 1.995885 0.7606838       140        29
    ## 272       NA           110 3.857367 1.0136054       403        18
    ## 273       NA            38 2.900838 1.2195122       295        52
    ## 274       NA            77 3.563356 1.3628319       381        20
    ## 275       NA           106 4.079060 1.6162791       369        45
    ## 276       NA            72 2.760976 1.2571429       277        55
    ## 277       NA           106 3.445545 1.3043478       271        52
    ## 278       NA           104 2.821918 1.0630631       185        50
    ## 279       NA            79 3.843411 1.0265487       403        15
    ## 280       NA           102 3.942164 1.3103448       321        44
    ## 281       NA           109 4.073350 1.1976744       309        20
    ## 282       NA           104 2.947230 1.0125000       243         7
    ## 283       NA            74 3.381510 1.0400000       426         9
    ## 284       NA           106 4.378840 1.1200000       358        18
    ## 285       NA            66 3.688478 1.0000000       389         1
    ## 286       NA            74 3.830795 1.2258065       392         6
    ## 287       NA            78 3.815141 1.0000000       434         6
    ## 288       NA            65 3.430642 1.0000000       285         1
    ## 289       NA            74 2.879925 1.0000000       285         2
    ## 290       NA            80 4.142562 1.0000000       454         2
    ## 291       NA            66 3.555804 1.0000000       272         2
    ## 292       33           100 4.690751 3.4000000       278       119
    ## 293       30            98 4.159159 1.9397163       236       120
    ## 294       30            87 3.604140 2.8763441       400       149
    ## 295       27            85 4.098446 2.2768595       317       146
    ## 296       27            65 3.341418 1.9144981       271       101
    ## 297       22            90 4.643200 1.5827338       373        71
    ## 298       33           106 3.587855 1.7948718       467       108
    ## 299       28           104 3.420975 1.3322785       342        96
    ## 300       26            70 4.135036 0.4805825       381        39
    ## 301       22           100 3.494327 1.5333333       322        91
    ## 302       22            94 5.344227 1.4086957       441        92
    ## 303       27           108 4.030075 1.4258373       255        73
    ## 304       27            82 3.459370 2.0972222       379       102
    ## 305       18            88 3.757269 1.8776978       334        51
    ## 306       21           102 3.561364 1.2641509       291        24
    ## 307       26           102 4.910190 1.5988372       375        90
    ## 308       17           102 3.960254 1.5740741       329        69
    ## 309       22           108 3.432387 1.1830065       345        62
    ## 310       17           102 3.093146 1.0262009       333        32
    ## 311       24            88 2.747100 1.1809045       338        98
    ## 312       20            88 2.539823 1.3595506       283        89
    ## 313       14            86 2.524740 0.1857143       321        16
    ## 314       20           102 3.943005 1.0761421       260        13
    ## 315       27            82 3.986357 1.3947368       324        57
    ## 316       22            82 2.743455 1.3766234       214        67
    ## 317       19            96 4.033333 1.3034483       373        47
    ## 318       16            79 3.030405 1.0375000       303        15
    ## 319       17            96 3.509138 1.1846154       178        11
    ## 320       31           102 3.265082 1.5600000       288        59
    ## 321       14           108 3.621993 1.2522523       378        14
    ## 322       18           100 3.544419 1.0378788       363        36
    ## 323       14            65 2.244755 1.2641509       152        29
    ## 324        8            72 4.390879 1.0000000       485         8
    ## 325       19           106 3.630476 1.2371134       343        36
    ## 326       14            72 3.304189 1.0086207       295         6
    ## 327       16           102 2.788793 1.2758621       178        34
    ## 328       19            88 3.298748 1.2043011       274        36
    ## 329       22           115 3.487871 1.3139535       214        32
    ## 330       17            92 3.420315 1.0396040       287         7
    ## 331       19            65 3.024548 1.4324324       328        41
    ## 332       15           106 3.380463 1.2168675       215        24
    ## 333       15            86 3.967078 1.1647059       277        27
    ## 334       18            96 3.616867 1.3661972       317        37
    ## 335       20           113 2.776181 0.5584416       258        20
    ## 336       18           104 4.204846 1.0375000       289        15
    ## 337       20            94 3.088757 1.2592593       275         9
    ## 338        9            75 4.883966 1.1666667       387        10
    ## 339       16           104 4.195335 1.0877193       235        17
    ## 340       13            86 2.997050 1.0175439       252         9
    ## 341       11            77 2.548969 1.0444444       195         9
    ## 342       10            64 3.704110 1.1000000       265         8
    ## 343       10            82 3.765528 1.2500000       303        17
    ## 344       14           102 3.435743 1.1818182       331         7
    ## 345       11            74 3.182390 1.0277778       281         9
    ## 346       11            76 2.287293 1.2500000       206        13
    ## 347       17            68 2.618729 1.0000000       212         4
    ## 348       11            67 2.917355 1.0833333       193         6
    ## 349       13            70 3.607088 0.9000000       366        11
    ## 350       13            98 3.921109 1.2307692       281         8
    ## 351        9            69 3.686347 1.5000000       228         3
    ## 352       10            66 3.833770 0.0000000       386         0
    ##     tokens_MOT tokens_CHI ADOS1 nonverbalIQ1 verbalIQ1 Socialization1
    ## 1         1731       1294     0           26        17             96
    ## 2         3076       1249    11           32        33            100
    ## 3         2576       1079     0           29        33            106
    ## 4         2895       1010     0           29        22             90
    ## 5         2227        921    14           42        27             65
    ## 6         2510        897     7           33        26             70
    ## 7         1959        864     0           25        17            102
    ## 8         2044        847     0           21        19            106
    ## 9         1802        793     0           23        17             92
    ## 10        2171        738     8           31        27             82
    ## 11        1764        719     0           24        19             96
    ## 12        3072        713    13           30        30             87
    ## 13        2264        710     0           29        26            102
    ## 14        1741        702     0           25        17            102
    ## 15         999        698    13           34        27             85
    ## 16        2586        686     0           28        14            108
    ## 17        1138        666     0           21        15            106
    ## 18        1460        659     4           29        22            108
    ## 19        1701        646     0           27        27            108
    ## 20        1498        640     0           30        16            104
    ## 21        2221        618    15           30        24             88
    ## 22        2438        611     1           23        21            102
    ## 23        1540        605    10           27        22             82
    ## 24        2389        590     9           26        14             86
    ## 25        2508        571    13           27        13             86
    ## 26        1978        568     3           19        13             98
    ## 27        3182        538     9           34        27             82
    ## 28        1788        530     0           26        18             96
    ## 29        1889        521     0           20        16            102
    ## 30        2347        517     1           24        22            115
    ## 31        2564        460     1           29        18             88
    ## 32        2887        436     0           29        22             94
    ## 33        2363        418     0           27        22            100
    ## 34        1395        410     0           24        15             86
    ## 35        1866        395     1           22        14            102
    ## 36        1024        358     0           30        30             98
    ## 37        1565        306    17           28        10             82
    ## 38         536        300    17           20        17             68
    ## 39         995        274    11           28        20             88
    ## 40        1748        236    14           25        19             65
    ## 41        1481        210    15           27        16             79
    ## 42        2391        204    17           26        14             72
    ## 43        1352        197     5           32        31            102
    ## 44        2219        195    12           31        13             70
    ## 45        2271        189     3           27        18            104
    ## 46        1349        143    20           13        11             67
    ## 47        1918        137    20           21         9             75
    ## 48        1987        110    11           26        19             88
    ## 49        2202        100    21           21         9             69
    ## 50        1852         79    14           25        11             76
    ## 51        2450         64    15           28        10             66
    ## 52        1371         61     0           27        20            102
    ## 53        1454         37    14           25        11             74
    ## 54        1390         36    14           27        11             77
    ## 55        1396          8    19           17        10             64
    ## 56        2077          2    21           22         8             72
    ## 57        2410       1154    11           32        33            100
    ## 58        2940       1145     0           29        22             90
    ## 59        2326       1054    14           42        27             65
    ## 60        2668        932     0           25        17            102
    ## 61        1467        916     0           30        16            104
    ## 62        1600        875     0           24        19             96
    ## 63        2314        815     7           33        26             70
    ## 64        1067        814     0           30        30             98
    ## 65        1999        800     1           29        28            104
    ## 66        2504        792     9           26        14             86
    ## 67        2881        769     0           29        33            106
    ## 68        1864        755     0           23        17             92
    ## 69        2279        736     1           23        21            102
    ## 70        1977        725     1           24        22            115
    ## 71        2772        723     0           29        22             94
    ## 72        2729        690     0           27        22            100
    ## 73        1965        686     0           21        19            106
    ## 74        1936        651     0           25        17            102
    ## 75        1916        636     4           29        22            108
    ## 76        1098        622     0           20        16            102
    ## 77        2672        588     1           29        18             88
    ## 78        1525        586     0           27        27            108
    ## 79        2762        583     0           29        26            102
    ## 80        2082        539     8           31        27             82
    ## 81        1223        537    10           27        22             82
    ## 82        2276        531    15           30        24             88
    ## 83        1300        513     0           21        15            106
    ## 84        1527        511    15           27        16             79
    ## 85        1791        502     5           24        20            113
    ## 86        2553        472     0           28        14            108
    ## 87        1979        459     3           19        13             98
    ## 88        1207        433     3           30        20             94
    ## 89        2208        431     3           27        18            104
    ## 90        2749        404     9           34        27             82
    ## 91        3090        357    13           30        30             87
    ## 92         209        349    17           20        17             68
    ## 93        1879        346     0           24        18            100
    ## 94        1951        340    11           26        19             88
    ## 95        2345        323     5           32        31            102
    ## 96        1385        318     0           26        18             96
    ## 97        1224        304     0           27        20            102
    ## 98        1871        255    17           28        10             82
    ## 99        1986        238     1           22        14            102
    ## 100       1142        197    11           28        20             88
    ## 101       1518        197    14           25        19             65
    ## 102        891        194    18           24        14             65
    ## 103       2360        193    12           31        13             70
    ## 104       2560        179    17           26        14             72
    ## 105       1426        160     0           24        15             86
    ## 106       1917         42    14           25        11             76
    ## 107       1278         34    20           13        11             67
    ## 108       1388         33    21           21         9             69
    ## 109       1609         32    20           21         9             75
    ## 110       2070         30    15           28        10             66
    ## 111       2088          9    14           25        11             74
    ## 112       1963          5    21           22         8             72
    ## 113       1447          0    19           17        10             64
    ## 114       2978       1225     0           29        22             90
    ## 115       2965       1092     0           29        33            106
    ## 116       1524       1051     0           26        17             96
    ## 117        798        978     0           30        30             98
    ## 118       1559        973     0           27        20            102
    ## 119       2158        822    14           42        27             65
    ## 120       1726        820     0           24        19             96
    ## 121       3077        793     0           29        26            102
    ## 122       2397        754     1           29        18             88
    ## 123       2233        750     0           27        22            100
    ## 124       2485        717     9           26        14             86
    ## 125       2518        714    11           32        33            100
    ## 126       1699        694     1           24        22            115
    ## 127       3163        693    13           30        30             87
    ## 128       1452        684     0           27        27            108
    ## 129       1286        674    13           34        27             85
    ## 130       2585        642     0           29        22             94
    ## 131       1974        637     4           29        22            108
    ## 132       2685        604     9           34        27             82
    ## 133       2256        592     0           21        19            106
    ## 134       2482        576     1           29        28            104
    ## 135       2109        574    15           30        24             88
    ## 136       1984        557     0           23        17             92
    ## 137       2361        538     0           25        17            102
    ## 138       2252        510     7           33        26             70
    ## 139       1470        502     0           20        16            102
    ## 140       1517        495     0           24        15             86
    ## 141        814        494    18           24        14             65
    ## 142       2069        493     5           32        31            102
    ## 143       2377        493    13           27        13             86
    ## 144       1711        479     8           31        27             82
    ## 145       1715        477     3           19        13             98
    ## 146        855        455    11           28        20             88
    ## 147       1224        438    10           27        22             82
    ## 148       1689        434     0           25        17            102
    ## 149       1909        433     5           24        20            113
    ## 150       2345        414     3           27        18            104
    ## 151       1934        390     0           24        18            100
    ## 152       1754        384     0           26        18             96
    ## 153       1556        378     3           30        20             94
    ## 154       1387        354     0           21        15            106
    ## 155       1829        348     1           22        14            102
    ## 156       1644        334    15           27        16             79
    ## 157       2260        321     0           28        14            108
    ## 158        632        243     0           30        16            104
    ## 159        360        205    17           20        17             68
    ## 160       1054        162    14           25        19             65
    ## 161       1482        143    20           13        11             67
    ## 162       1726        142    14           25        11             76
    ## 163       1675        135    11           26        19             88
    ## 164       2283         93    21           22         8             72
    ## 165       1863         79    12           31        13             70
    ## 166       2145         74     1           23        21            102
    ## 167       1145         70    19           17        10             64
    ## 168        861         55    20           21         9             75
    ## 169       1482         45    17           28        10             82
    ## 170       2214         39    17           26        14             72
    ## 171       2054         14    21           21         9             69
    ## 172       2196          1    15           28        10             66
    ## 173       1668       1293    11           32        33            100
    ## 174       1872       1246     0           27        22            100
    ## 175       2141       1023    14           42        27             65
    ## 176       1445        983    13           34        27             85
    ## 177       2479        940     0           29        33            106
    ## 178       2142        897     0           29        22             94
    ## 179       2589        826     0           29        22             90
    ## 180       2675        825     0           29        26            102
    ## 181       2146        825     1           29        28            104
    ## 182       1463        733     0           30        16            104
    ## 183       2328        702     1           23        21            102
    ## 184       2841        698    13           30        30             87
    ## 185       1570        694     7           33        26             70
    ## 186       1470        672     0           26        17             96
    ## 187       1609        671     0           27        27            108
    ## 188       1020        660     0           30        30             98
    ## 189       2571        630     0           25        17            102
    ## 190       2124        604     0           24        19             96
    ## 191       2630        542     1           29        18             88
    ## 192       1740        528     3           30        20             94
    ## 193       2092        503     9           26        14             86
    ## 194       1788        490     8           31        27             82
    ## 195       1568        486     0           27        20            102
    ## 196       2939        468     9           34        27             82
    ## 197       1991        453     0           24        18            100
    ## 198       1610        424     4           29        22            108
    ## 199       2148        404    13           27        13             86
    ## 200       1006        403     0           20        16            102
    ## 201       1891        393    15           30        24             88
    ## 202       2328        368     0           23        17             92
    ## 203       1350        365    10           27        22             82
    ## 204       1912        354     5           24        20            113
    ## 205       1426        352     0           25        17            102
    ## 206       1159        330    11           28        20             88
    ## 207       1811        305     0           21        19            106
    ## 208       1706        299     0           26        18             96
    ## 209       1418        261     1           24        22            115
    ## 210       2149        255     0           28        14            108
    ## 211       1618        254    15           27        16             79
    ## 212       1743        252     0           24        15             86
    ## 213       1709        232    11           26        19             88
    ## 214       1504        218     3           19        13             98
    ## 215        768        210    18           24        14             65
    ## 216        878        193     0           21        15            106
    ## 217       2936        188     3           27        18            104
    ## 218       2278        151    12           31        13             70
    ## 219       1332        148    14           25        19             65
    ## 220       1562        122     1           22        14            102
    ## 221       2764         96    21           22         8             72
    ## 222       1423         77    19           17        10             64
    ## 223        250         62    17           20        17             68
    ## 224       1145         58    14           25        11             74
    ## 225       2717         41    15           28        10             66
    ## 226       1393         40    20           13        11             67
    ## 227       2417         39    17           26        14             72
    ## 228       1504         26    14           25        11             76
    ## 229       1393         14    14           27        11             77
    ## 230       1018          3    20           21         9             75
    ## 231       1590          1    21           21         9             69
    ## 232       1784        879     1           23        21            102
    ## 233       2687        738     0           29        33            106
    ## 234       2303        733     0           29        26            102
    ## 235       2534        670    13           30        30             87
    ## 236       2562        623     0           29        22             90
    ## 237       1653        571     0           27        20            102
    ## 238       1270        562    13           34        27             85
    ## 239       1657        555     8           31        27             82
    ## 240       2099        554     5           32        31            102
    ## 241       2219        542     0           27        22            100
    ## 242       2031        536    14           42        27             65
    ## 243       2694        530     1           29        18             88
    ## 244       2955        487     9           34        27             82
    ## 245        686        449     0           30        30             98
    ## 246       2290        439     1           29        28            104
    ## 247       2340        433     7           33        26             70
    ## 248       2201        423     0           24        19             96
    ## 249       1843        414     0           21        19            106
    ## 250       1959        401     4           29        22            108
    ## 251       2260        393     0           25        17            102
    ## 252        748        368    10           27        22             82
    ## 253       1822        362     9           26        14             86
    ## 254       1867        356     0           27        27            108
    ## 255       1205        356    15           27        16             79
    ## 256       1316        335    11           28        20             88
    ## 257       1845        298     0           29        22             94
    ## 258       1982        297     0           23        17             92
    ## 259       1905        287     1           24        22            115
    ## 260       1847        264     0           25        17            102
    ## 261       1511        260    15           30        24             88
    ## 262       1139        258     0           20        16            102
    ## 263       1058        244     0           26        17             96
    ## 264       1663        236     0           26        18             96
    ## 265       1551        203     0           30        16            104
    ## 266       2448        177    17           26        14             72
    ## 267        755        169    18           24        14             65
    ## 268       1375        165     5           24        20            113
    ## 269       1819        159     0           24        15             86
    ## 270       1990        157    14           25        19             65
    ## 271        464        149    17           20        17             68
    ## 272       2160        148     0           28        14            108
    ## 273       1882        148    13           27        13             86
    ## 274       1851        147    19           17        10             64
    ## 275       1702        132     0           24        18            100
    ## 276       1481        126    11           26        19             88
    ## 277       1515        120     3           30        20             94
    ## 278        703        117     0           21        15            106
    ## 279       2211        116    12           31        13             70
    ## 280       1843        107     3           19        13             98
    ## 281       1483         97     1           22        14            102
    ## 282        999         82    14           25        11             74
    ## 283       2524         78    21           22         8             72
    ## 284       2263         55     3           27        18            104
    ## 285       2274         44    15           28        10             66
    ## 286       1973         38    20           21         9             75
    ## 287       1995         32    20           13        11             67
    ## 288       1417         19    14           27        11             77
    ## 289       1393         10    14           25        11             76
    ## 290       1856          7    21           21         9             69
    ## 291       1344          4    17           28        10             82
    ## 292       1450        483    11           32        33            100
    ## 293       1170        473     0           30        30             98
    ## 294       2587        469    13           30        30             87
    ## 295       1428        461    13           34        27             85
    ## 296       1591        450    14           42        27             65
    ## 297       2740        433     0           29        22             90
    ## 298       2555        406     0           29        33            106
    ## 299       2035        398     1           29        28            104
    ## 300       1988        337     7           33        26             70
    ## 301       1870        319     0           27        22            100
    ## 302       2267        319     0           29        22             94
    ## 303       1452        286     0           27        27            108
    ## 304       2009        269     8           31        27             82
    ## 305       2674        260     1           29        18             88
    ## 306       1344        260     1           23        21            102
    ## 307       2585        254     0           29        26            102
    ## 308       2139        249     0           25        17            102
    ## 309       1805        244     4           29        22            108
    ## 310       1547        235     0           25        17            102
    ## 311       2084        233    15           30        24             88
    ## 312       1019        227    11           28        20             88
    ## 313       1787        214     9           26        14             86
    ## 314       1347        212     0           27        20            102
    ## 315       2859        197     9           34        27             82
    ## 316        893        180    10           27        22             82
    ## 317       2334        176     0           24        19             96
    ## 318       1579        166    15           27        16             79
    ## 319       1144        154     0           26        17             96
    ## 320       1564        143     5           32        31            102
    ## 321       1835        139     0           28        14            108
    ## 322       1408        137     0           24        18            100
    ## 323        578        130    18           24        14             65
    ## 324       2826        122    21           22         8             72
    ## 325       1698        118     0           21        19            106
    ## 326       1643        117    17           26        14             72
    ## 327        584        111     0           20        16            102
    ## 328       1537        109    11           26        19             88
    ## 329       1118        109     1           24        22            115
    ## 330       1625        105     0           23        17             92
    ## 331       2138        103    14           25        19             65
    ## 332       1136        101     0           21        15            106
    ## 333       1643         99     0           24        15             86
    ## 334       1361         95     0           26        18             96
    ## 335       1188         91     5           24        20            113
    ## 336       1808         83     3           27        18            104
    ## 337       1417         68     3           30        20             94
    ## 338       2144         63    20           21         9             75
    ## 339       1262         62     0           30        16            104
    ## 340       1827         58    13           27        13             86
    ## 341        955         47    14           27        11             77
    ## 342       1215         43    19           17        10             64
    ## 343       2147         40    17           28        10             82
    ## 344       1503         38     1           22        14            102
    ## 345       1418         37    14           25        11             74
    ## 346        788         35    14           25        11             76
    ## 347        761         29    17           20        17             68
    ## 348        654         26    20           13        11             67
    ## 349       2054         21    12           31        13             70
    ## 350       1631         16     3           19        13             98
    ## 351        927          3    21           21         9             69
    ## 352       2613          0    15           28        10             66

``` r
# kid nr. 55

#2
arrange(df_merged, VISIT, tokens_CHI)
```

    ##     SUBJ VISIT        Ethnicity Diagnosis Gender   Age ADOS nonverbalIQ
    ## 1     57     1            White       ASD      F 41.07   15          28
    ## 2     32     1            White       ASD      M 34.27   21          21
    ## 3     36     1            White        TD      M 19.20    3          19
    ## 4     30     1            White       ASD      M 36.53   12          31
    ## 5     24     1            White       ASD      M 37.47   20          13
    ## 6      6     1      Bangladeshi       ASD      F 26.17   17          20
    ## 7     33     1 African American       ASD      F 25.33   14          25
    ## 8     17     1            White       ASD      M 34.80   14          25
    ## 9     51     1            Asian        TD      F 20.87    1          22
    ## 10    48     1            White       ASD      M 31.63   17          28
    ## 11    50     1            White       ASD      M 37.47   19          17
    ## 12    56     1            White       ASD      M 35.50   14          27
    ## 13    40     1         Lebanese       ASD      M 24.90   13          27
    ## 14    12     1            White        TD      M 20.07    0          30
    ## 15    46     1            White       ASD      M 36.73   20          21
    ## 16    44     1            White        TD      M 19.77    3          30
    ## 17    10     1            White        TD      M 19.27    3          27
    ## 18    45     1            White        TD      M 20.03    5          24
    ## 19    27     1            White        TD      M 21.03    0          26
    ## 20    15     1            White        TD      M 19.27    0          24
    ## 21    11     1            White        TD      M 19.23    0          21
    ## 22    23     1     White/Latino       ASD      M 27.37   14          25
    ## 23    14     1            White        TD      M 18.97    0          23
    ## 24    39     1      White/Asian       ASD      M 33.20   11          26
    ## 25    54     1            White        TD      M 19.10    1          24
    ## 26    28     1            White        TD      M 19.93    0          20
    ## 27    29     1            White       ASD      M 34.87   17          26
    ## 28    22     1            White        TD      F 18.93    0          21
    ## 29    21     1 African American       ASD      M 27.53   21          22
    ## 30     7     1            White       ASD      F 41.00   18          24
    ## 31     9     1            White        TD      F 18.30    0          24
    ## 32     1     1            White        TD      M 19.80    0          28
    ## 33     8     1            White        TD      M 20.10    5          32
    ## 34    55     1            White        TD      M 19.97    0          26
    ## 35    61     1            White       ASD      M 42.00   15          27
    ## 36    42     1            White        TD      M 19.37    0          24
    ## 37    34     1            White       ASD      M 33.77   10          27
    ## 38     5     1            White       ASD      M 34.03    9          34
    ## 39    47     1            White        TD      F 20.03    0          27
    ## 40    20     1            White       ASD      M 18.77    9          26
    ## 41    19     1            White       ASD      M 35.80   11          28
    ## 42    58     1            White       ASD      M 26.00   15          30
    ## 43    41     1            White        TD      M 19.23    0          25
    ## 44    59     1            White        TD      M 20.80    4          29
    ## 45    13     1            White        TD      M 19.00    0          25
    ## 46    16     1            White        TD      M 21.67    0          29
    ## 47     3     1            White        TD      F 23.50    1          29
    ## 48    35     1            White        TD      M 19.30    1          23
    ## 49     4     1     White/Latino       ASD      M 31.03    8          31
    ## 50    49     1            White        TD      M 23.07    0          27
    ## 51    31     1            White        TD      M 23.13    0          27
    ## 52    52     1            White        TD      M 22.57    0          29
    ## 53    37     1            White       ASD      M 39.50    7          33
    ## 54    38     1            White        TD      F 19.87    1          29
    ## 55    18     1            White        TD      M 20.77    0          29
    ## 56    25     1            White        TD      M 21.77    0          29
    ## 57    43     1            White       ASD      M 37.03   14          42
    ## 58     2     1            White       ASD      M 28.80   13          34
    ## 59    60     1            White       ASD      M 34.00   13          30
    ## 60    53     1            White        TD      M 23.90    0          30
    ## 61    26     1            White       ASD      M 30.40   11          32
    ## 62    48     2            White       ASD      M 37.20   NA          NA
    ## 63    32     2            White       ASD      M 38.33   NA          NA
    ## 64    33     2 African American       ASD      F 29.70   NA          NA
    ## 65    56     2            White       ASD      M 39.40   NA          NA
    ## 66    24     2            White       ASD      M 42.20   NA          NA
    ## 67    46     2            White       ASD      M 41.50   NA          NA
    ## 68    57     2            White       ASD      F 41.07   NA          NA
    ## 69    10     2            White        TD      M 23.83   NA          NA
    ## 70    21     2 African American       ASD      M 31.80   NA          NA
    ## 71    17     2            White       ASD      M 38.73   NA          NA
    ## 72    51     2            Asian        TD      F 25.40   NA          NA
    ## 73    36     2            White        TD      M 22.87   NA          NA
    ## 74    30     2            White       ASD      M 40.50   NA          NA
    ## 75    11     2            White        TD      M    NA   NA          NA
    ## 76    44     2            White        TD      M 23.63   NA          NA
    ## 77    39     2      White/Asian       ASD      M 37.37   NA          NA
    ## 78     9     2            White        TD      F 23.73   NA          NA
    ## 79    50     2            White       ASD      M 42.20   NA          NA
    ## 80     1     2            White        TD      M 23.93   NA          NA
    ## 81    40     2         Lebanese       ASD      M 32.20   NA          NA
    ## 82     6     2      Bangladeshi       ASD      F 30.27   NA          NA
    ## 83    23     2     White/Latino       ASD      M 32.10   NA          NA
    ## 84    15     2            White        TD      M 24.80   NA          NA
    ## 85    45     2            White        TD      M 24.68   NA          NA
    ## 86     7     2            White       ASD      F 49.20   NA          NA
    ## 87    29     2            White       ASD      M 38.13   NA          NA
    ## 88    12     2            White        TD      M 23.83   NA          NA
    ## 89    27     2            White        TD      M 25.23   NA          NA
    ## 90    55     2            White        TD      M 23.93   NA          NA
    ## 91    28     2            White        TD      M 23.57   NA          NA
    ## 92    58     2            White       ASD      M 30.10   NA          NA
    ## 93    41     2            White        TD      M 23.07   NA          NA
    ## 94    54     2            White        TD      M 23.17   NA          NA
    ## 95    14     2            White        TD      M 22.87   NA          NA
    ## 96    52     2            White        TD      M 26.63   NA          NA
    ## 97    19     2            White       ASD      M 39.93   NA          NA
    ## 98    49     2            White        TD      M 27.47   NA          NA
    ## 99    61     2            White       ASD      M 46.77   NA          NA
    ## 100   20     2            White       ASD      M 22.50   NA          NA
    ## 101   34     2            White       ASD      M 38.13   NA          NA
    ## 102   13     2            White        TD      M 22.97   NA          NA
    ## 103   59     2            White        TD      M 25.87   NA          NA
    ## 104   22     2            White        TD      F 22.90   NA          NA
    ## 105   42     2            White        TD      M    NA   NA          NA
    ## 106   37     2            White       ASD      M 43.68   NA          NA
    ## 107   38     2            White        TD      F 24.17   NA          NA
    ## 108   53     2            White        TD      M 28.60   NA          NA
    ## 109    5     2            White       ASD      M 37.57   NA          NA
    ## 110    3     2            White        TD      F 27.83   NA          NA
    ## 111   43     2            White       ASD      M 42.77   NA          NA
    ## 112   31     2            White        TD      M 27.70   NA          NA
    ## 113    8     2            White        TD      M 24.07   NA          NA
    ## 114    4     2     White/Latino       ASD      M 35.30   NA          NA
    ## 115    2     2            White       ASD      M 33.17   NA          NA
    ## 116   47     2            White        TD      F 24.40   NA          NA
    ## 117   25     2            White        TD      M 25.47   NA          NA
    ## 118   60     2            White       ASD      M 38.63   NA          NA
    ## 119   16     2            White        TD      M 26.27   NA          NA
    ## 120   18     2            White        TD      M 26.13   NA          NA
    ## 121   35     2            White        TD      M 23.43   NA          NA
    ## 122   32     3            White       ASD      M 42.13   NA          NA
    ## 123   46     3            White       ASD      M 45.57   NA          NA
    ## 124   56     3            White       ASD      M 44.47   NA          NA
    ## 125   33     3 African American       ASD      F 34.10   NA          NA
    ## 126   29     3            White       ASD      M 43.10   NA          NA
    ## 127   24     3            White       ASD      M 46.37   NA          NA
    ## 128   57     3            White       ASD      F 49.97   NA          NA
    ## 129   17     3            White       ASD      M    NA   NA          NA
    ## 130    6     3      Bangledeshi       ASD      F 34.57   NA          NA
    ## 131   50     3            White       ASD      M 46.37   NA          NA
    ## 132   21     3 African American       ASD      M 36.17   NA          NA
    ## 133   51     3            Asian        TD      F 29.67   NA          NA
    ## 134   23     3     White/Latino       ASD      M 35.93   NA          NA
    ## 135   30     3            White       ASD      M 44.67   NA          NA
    ## 136   10     3            White        TD      M 28.53   NA          NA
    ## 137   11     3            White        TD      M 27.27   NA          NA
    ## 138    7     3            White       ASD      F 49.20   NA          NA
    ## 139   36     3            White        TD      M 26.87   NA          NA
    ## 140   39     3      White/Asian       ASD      M 41.73   NA          NA
    ## 141   15     3            White        TD      M 28.10   NA          NA
    ## 142   61     3            White       ASD      M 50.63   NA          NA
    ## 143    1     3            White        TD      M 27.70   NA          NA
    ## 144   54     3            White        TD      M 27.07   NA          NA
    ## 145   27     3            White        TD      M 29.07   NA          NA
    ## 146   22     3            White        TD      F 26.67   NA          NA
    ## 147   19     3            White       ASD      M 43.90   NA          NA
    ## 148   41     3            White        TD      M 28.47   NA          NA
    ## 149   45     3            White        TD      M 28.63   NA          NA
    ## 150   34     3            White       ASD      M 43.00   NA          NA
    ## 151   14     3            White        TD      M 26.80   NA          NA
    ## 152   58     3            White       ASD      M 34.27   NA          NA
    ## 153   28     3            White        TD      M 27.03   NA          NA
    ## 154   40     3         Lebanese       ASD      M 34.00   NA          NA
    ## 155   59     3            White        TD      M 29.67   NA          NA
    ## 156    9     3            White        TD      F 26.63   NA          NA
    ## 157    5     3            White       ASD      M 41.57   NA          NA
    ## 158   47     3            White        TD      F 29.53   NA          NA
    ## 159    4     3     White/Latino       ASD      M 38.90   NA          NA
    ## 160   20     3            White       ASD      M 26.77   NA          NA
    ## 161   44     3            White        TD      M 27.67   NA          NA
    ## 162    3     3            White        TD      F 31.83   NA          NA
    ## 163   42     3            White        TD      M 28.90   NA          NA
    ## 164   13     3            White        TD      M 27.07   NA          NA
    ## 165   53     3            White        TD      M 32.50   NA          NA
    ## 166   49     3            White        TD      M 31.63   NA          NA
    ## 167   55     3            White        TD      M 28.03   NA          NA
    ## 168   37     3            White       ASD      M 47.73   NA          NA
    ## 169   60     3            White       ASD      M 42.47   NA          NA
    ## 170   35     3            White        TD      M 27.13   NA          NA
    ## 171   12     3            White        TD      M 28.27   NA          NA
    ## 172   16     3            White        TD      M 30.63   NA          NA
    ## 173   38     3            White        TD      F 28.07   NA          NA
    ## 174   25     3            White        TD      M 30.13   NA          NA
    ## 175   52     3            White        TD      M 30.77   NA          NA
    ## 176   18     3            White        TD      M 30.03   NA          NA
    ## 177    2     3            White       ASD      M 37.07   NA          NA
    ## 178   43     3            White       ASD      M 46.23   NA          NA
    ## 179   31     3            White        TD      M 32.07   NA          NA
    ## 180   26     3            White       ASD      M 38.60   NA          NA
    ## 181   57     4            White       ASD      F 53.90   NA          30
    ## 182   32     4            White       ASD      M 47.10   NA          27
    ## 183   29     4            White       ASD      M 46.63   NA          27
    ## 184   48     4            White       ASD      M 43.63   NA          33
    ## 185   46     4            White       ASD      M 50.23   NA          29
    ## 186   50     4            White       ASD      M 52.13   NA          22
    ## 187   35     4            White        TD      M 30.80   NA          36
    ## 188   30     4            White       ASD      M 48.10   NA          47
    ## 189   21     4 African American       ASD      M 40.27   NA          27
    ## 190   39     4      White/Asian       ASD      M 45.33   NA          45
    ## 191   33     4 African American       ASD      F 36.93   NA          36
    ## 192   24     4            White       ASD      M 52.13   NA          29
    ## 193   23     4     White/Latino       ASD      M 42.23   NA          29
    ## 194    6     4      Bangladeshi       ASD      F 38.47   NA          33
    ## 195   12     4            White        TD      M 32.07   NA          42
    ## 196    1     4            White        TD      M 32.90   NA          33
    ## 197   61     4            White       ASD      M 54.13   NA          28
    ## 198   51     4            Asian        TD      F 34.43   NA          30
    ## 199   11     4            White        TD      M 31.20   NA          35
    ## 200   44     4            White        TD      M 32.07   NA          40
    ## 201   27     4            White        TD      M 33.30   NA          42
    ## 202    9     4            White        TD      F 31.07   NA          45
    ## 203   10     4            White        TD      M 32.43   NA          31
    ## 204   45     4            White        TD      M 32.77   NA          26
    ## 205   41     4            White        TD      M 32.07   NA          41
    ## 206   34     4            White       ASD      M 46.13   NA          31
    ## 207   19     4            White       ASD      M 47.83   NA          35
    ## 208   36     4            White        TD      M 30.40   NA          35
    ## 209    4     4     White/Latino       ASD      M 43.13   NA          41
    ## 210    8     4            White        TD      M 32.07   NA          42
    ## 211   40     4         Lebanese       ASD      M    NA   NA          NA
    ## 212    7     4            White       ASD      F 52.27   NA          29
    ## 213   15     4            White        TD      M 31.27   NA          33
    ## 214   28     4            White        TD      M 31.00   NA          28
    ## 215   37     4            White       ASD      M 51.67   NA          49
    ## 216   13     4            White        TD      M 31.03   NA          40
    ## 217   14     4            White        TD      M 31.47   NA          29
    ## 218   58     4            White       ASD      M 38.20   NA          40
    ## 219   38     4            White        TD      F 32.17   NA          44
    ## 220   22     4            White        TD      F 30.93   NA          40
    ## 221    5     4            White       ASD      M 45.53   NA          38
    ## 222   59     4            White        TD      M 33.60   NA          34
    ## 223   52     4            White        TD      M 35.03   NA          45
    ## 224    2     4            White       ASD      M 41.07   NA          49
    ## 225   49     4            White        TD      M 35.63   NA          45
    ## 226   60     4            White       ASD      M 47.00   NA          47
    ## 227   54     4            White        TD      M 30.83   NA          39
    ## 228   26     4            White       ASD      M 42.63   NA          41
    ## 229   20     4            White       ASD      M 30.80   NA          43
    ## 230   31     4            White        TD      M 35.03   NA          43
    ## 231    3     4            White        TD      F 35.53   NA          39
    ## 232   16     4            White        TD      M 34.27   NA          45
    ## 233   42     4            White        TD      M 32.07   NA          39
    ## 234   43     4            White       ASD      M 49.30   NA          50
    ## 235   47     4            White        TD      F 32.13   NA          42
    ## 236   53     4            White        TD      M 36.40   NA          45
    ## 237   55     4            White        TD      M 32.03   NA          40
    ## 238   18     4            White        TD      M 34.43   NA          50
    ## 239   25     4            White        TD      M 34.00   NA          47
    ## 240   50     5            White       ASD      M 57.20   17          NA
    ## 241   21     5 African American       ASD      M 44.70   19          NA
    ## 242   17     5            White       ASD      M 50.93   15          NA
    ## 243   57     5            White       ASD      F 58.27   13          NA
    ## 244   46     5            White       ASD      M 54.33   17          NA
    ## 245   32     5            White       ASD      M 50.73   20          NA
    ## 246   24     5            White       ASD      M 57.20   17          NA
    ## 247   33     5 African American       ASD      F 42.97   25          NA
    ## 248   15     5            White        TD      M 35.90    3          NA
    ## 249   29     5            White       ASD      M 50.97   20          NA
    ## 250   30     5            White       ASD      M 52.90   15          NA
    ## 251    7     5            White       ASD      F 57.73   17          NA
    ## 252   19     5            White       ASD      M 51.97    9          NA
    ## 253   23     5     White/Latino       ASD      M 44.93   14          NA
    ## 254   51     5            Asian        TD      F 37.67    3          NA
    ## 255   48     5            White       ASD      M 52.77   17          NA
    ## 256   47     5            White        TD      F 39.10   NA          NA
    ## 257   27     5            White        TD      M 37.73    0          NA
    ## 258    8     5            White        TD      M 36.07    5          NA
    ## 259   39     5      White/Asian       ASD      M 48.63   11          NA
    ## 260    9     5            White        TD      F 35.00    1          NA
    ## 261    6     5      Bangladeshi       ASD      F 42.43    8          NA
    ## 262   60     5            White       ASD      M 51.13   15          NA
    ## 263    5     5            White       ASD      M 49.30    9          NA
    ## 264   10     5            White        TD      M 36.53    5          NA
    ## 265   44     5            White        TD      M 35.83    1          NA
    ## 266   36     5            White        TD      M 34.40    2          NA
    ## 267    1     5            White        TD      M 35.90    0          NA
    ## 268   45     5            White        TD      M 36.70   10          NA
    ## 269   61     5            White       ASD      M 58.57   15          NA
    ## 270   11     5            White        TD      M 35.63    0          NA
    ## 271   58     5            White       ASD      M 42.30   16          NA
    ## 272   34     5            White       ASD      M 50.30   16          NA
    ## 273    4     5     White/Latino       ASD      M 47.40    9          NA
    ## 274   16     5            White        TD      M 38.17    0          NA
    ## 275   49     5            White        TD      M 39.47    0          NA
    ## 276    3     5            White        TD      F 39.47    0          NA
    ## 277   28     5            White        TD      M 36.43    0          NA
    ## 278   59     5            White        TD      M 37.34    5          NA
    ## 279   41     5            White        TD      M 35.67    0          NA
    ## 280   22     5            White        TD      F 35.13    0          NA
    ## 281   31     5            White        TD      M 39.53    0          NA
    ## 282   52     5            White        TD      M 38.60    0          NA
    ## 283   54     5            White        TD      M 35.17    0          NA
    ## 284   35     5            White        TD      M 34.93    2          NA
    ## 285   14     5            White        TD      M 35.10    0          NA
    ## 286   18     5            White        TD      M 38.70    0          NA
    ## 287   20     5            White       ASD      M 34.13    8          NA
    ## 288   38     5            White        TD      F 36.13    0          NA
    ## 289   53     5            White        TD      M 40.07    0          NA
    ## 290   37     5            White       ASD      M 55.17    4          NA
    ## 291   42     5            White        TD      M 36.40    0          NA
    ## 292   12     5            White        TD      M 35.87    0          NA
    ## 293   13     5            White        TD      M 35.37    0          NA
    ## 294   43     5            White       ASD      M 53.40   11          NA
    ## 295   25     5            White        TD      M 37.93    0          NA
    ## 296   26     5            White       ASD      M 46.93    4          NA
    ## 297   21     6 African American       ASD      M 48.97   NA          26
    ## 298   50     6            White       ASD      M 62.40   NA          24
    ## 299   56     6            White       ASD      M    NA   NA          34
    ## 300   17     6            White       ASD      M 54.43   NA          27
    ## 301   47     6            White        TD      F 40.37   NA          43
    ## 302   57     6            White       ASD      F 61.70   NA          32
    ## 303   33     6 African American       ASD      F 46.17   NA          33
    ## 304   32     6            White       ASD      M 54.63   NA          28
    ## 305   39     6      White/Asian       ASD      M 53.63   NA          30
    ## 306   46     6            White       ASD      M 57.43   NA          32
    ## 307   24     6            White       ASD      M 62.40   NA          30
    ## 308   10     6            White        TD      M 40.43   NA          33
    ## 309   30     6            White       ASD      M 56.43   NA          49
    ## 310    8     6            White        TD      M 40.17   NA          43
    ## 311   29     6            White       ASD      M 55.17   NA          39
    ## 312   61     6            White       ASD      M 62.33   NA          41
    ## 313   23     6     White/Latino       ASD      M 47.50   NA          30
    ## 314   19     6            White       ASD      M 56.73   NA          30
    ## 315    6     6      Bangladeshi       ASD      F 46.53   NA          39
    ## 316   48     6            White       ASD      M    NA   NA          32
    ## 317   53     6            White        TD      M 44.43   NA          45
    ## 318   51     6            Asian        TD      F 42.10   NA          46
    ## 319   15     6            White        TD      M 40.30   NA          40
    ## 320   31     6            White        TD      M 43.80   NA          45
    ## 321   52     6            White        TD      M 43.03   NA          47
    ## 322    3     6            White        TD      F 45.07   NA          45
    ## 323   54     6            White        TD      M 39.30   NA          41
    ## 324   28     6            White        TD      M 39.43   NA          39
    ## 325   27     6            White        TD      M 41.17   NA          43
    ## 326    5     6            White       ASD      M 54.13   NA          40
    ## 327   36     6            White        TD      M 38.53   NA          46
    ## 328   40     6         Lebanese       ASD      M 46.40   NA          41
    ## 329   20     6            White       ASD      M 37.30   NA          43
    ## 330   34     6            White       ASD      M 53.77   NA          44
    ## 331   35     6            White        TD      M 39.07   NA          45
    ## 332   58     6            White       ASD      M 46.07   NA          45
    ## 333   12     6            White        TD      M 41.50   NA          44
    ## 334   49     6            White        TD      M 43.40   NA          45
    ## 335   59     6            White        TD      M 41.00   NA          NA
    ## 336   11     6            White        TD      M 40.27   NA          42
    ## 337    1     6            White        TD      M 40.13   NA          42
    ## 338    2     6            White       ASD      M 49.70   NA          48
    ## 339   41     6            White        TD      M 39.93   NA          45
    ## 340   16     6            White        TD      M 42.93   NA          49
    ## 341   60     6            White       ASD      M 54.73   NA          50
    ## 342   42     6            White        TD      M 40.13   NA          46
    ## 343    4     6     White/Latino       ASD      M 51.37   NA          44
    ## 344   14     6            White        TD      M 39.43   NA          44
    ## 345   22     6            White        TD      F 39.23   NA          43
    ## 346   13     6            White        TD      M 39.40   NA          47
    ## 347   37     6            White       ASD      M 58.77   NA          50
    ## 348   43     6            White       ASD      M 57.37   NA          49
    ## 349   25     6            White        TD      M 42.47   NA          48
    ## 350   18     6            White        TD      M 44.07   NA          50
    ## 351   26     6            White       ASD      M 51.00   NA          46
    ## 352   55     6            White        TD      M 41.93   NA          45
    ##     verbalIQ Socialization  MOT_MLU   CHI_MLU types_MOT types_CHI
    ## 1         10            66 3.833770 0.0000000       386         0
    ## 2          9            69 3.686347 1.5000000       228         3
    ## 3         13            98 3.921109 1.2307692       281         8
    ## 4         13            70 3.607088 0.9000000       366        11
    ## 5         11            67 2.917355 1.0833333       193         6
    ## 6         17            68 2.618729 1.0000000       212         4
    ## 7         11            76 2.287293 1.2500000       206        13
    ## 8         11            74 3.182390 1.0277778       281         9
    ## 9         14           102 3.435743 1.1818182       331         7
    ## 10        10            82 3.765528 1.2500000       303        17
    ## 11        10            64 3.704110 1.1000000       265         8
    ## 12        11            77 2.548969 1.0444444       195         9
    ## 13        13            86 2.997050 1.0175439       252         9
    ## 14        16           104 4.195335 1.0877193       235        17
    ## 15         9            75 4.883966 1.1666667       387        10
    ## 16        20            94 3.088757 1.2592593       275         9
    ## 17        18           104 4.204846 1.0375000       289        15
    ## 18        20           113 2.776181 0.5584416       258        20
    ## 19        18            96 3.616867 1.3661972       317        37
    ## 20        15            86 3.967078 1.1647059       277        27
    ## 21        15           106 3.380463 1.2168675       215        24
    ## 22        19            65 3.024548 1.4324324       328        41
    ## 23        17            92 3.420315 1.0396040       287         7
    ## 24        19            88 3.298748 1.2043011       274        36
    ## 25        22           115 3.487871 1.3139535       214        32
    ## 26        16           102 2.788793 1.2758621       178        34
    ## 27        14            72 3.304189 1.0086207       295         6
    ## 28        19           106 3.630476 1.2371134       343        36
    ## 29         8            72 4.390879 1.0000000       485         8
    ## 30        14            65 2.244755 1.2641509       152        29
    ## 31        18           100 3.544419 1.0378788       363        36
    ## 32        14           108 3.621993 1.2522523       378        14
    ## 33        31           102 3.265082 1.5600000       288        59
    ## 34        17            96 3.509138 1.1846154       178        11
    ## 35        16            79 3.030405 1.0375000       303        15
    ## 36        19            96 4.033333 1.3034483       373        47
    ## 37        22            82 2.743455 1.3766234       214        67
    ## 38        27            82 3.986357 1.3947368       324        57
    ## 39        20           102 3.943005 1.0761421       260        13
    ## 40        14            86 2.524740 0.1857143       321        16
    ## 41        20            88 2.539823 1.3595506       283        89
    ## 42        24            88 2.747100 1.1809045       338        98
    ## 43        17           102 3.093146 1.0262009       333        32
    ## 44        22           108 3.432387 1.1830065       345        62
    ## 45        17           102 3.960254 1.5740741       329        69
    ## 46        26           102 4.910190 1.5988372       375        90
    ## 47        18            88 3.757269 1.8776978       334        51
    ## 48        21           102 3.561364 1.2641509       291        24
    ## 49        27            82 3.459370 2.0972222       379       102
    ## 50        27           108 4.030075 1.4258373       255        73
    ## 51        22           100 3.494327 1.5333333       322        91
    ## 52        22            94 5.344227 1.4086957       441        92
    ## 53        26            70 4.135036 0.4805825       381        39
    ## 54        28           104 3.420975 1.3322785       342        96
    ## 55        33           106 3.587855 1.7948718       467       108
    ## 56        22            90 4.643200 1.5827338       373        71
    ## 57        27            65 3.341418 1.9144981       271       101
    ## 58        27            85 4.098446 2.2768595       317       146
    ## 59        30            87 3.604140 2.8763441       400       149
    ## 60        30            98 4.159159 1.9397163       236       120
    ## 61        33           100 4.690751 3.4000000       278       119
    ## 62        NA            66 3.555804 1.0000000       272         2
    ## 63        NA            80 4.142562 1.0000000       454         2
    ## 64        NA            74 2.879925 1.0000000       285         2
    ## 65        NA            65 3.430642 1.0000000       285         1
    ## 66        NA            78 3.815141 1.0000000       434         6
    ## 67        NA            74 3.830795 1.2258065       392         6
    ## 68        NA            66 3.688478 1.0000000       389         1
    ## 69        NA           106 4.378840 1.1200000       358        18
    ## 70        NA            74 3.381510 1.0400000       426         9
    ## 71        NA           104 2.947230 1.0125000       243         7
    ## 72        NA           109 4.073350 1.1976744       309        20
    ## 73        NA           102 3.942164 1.3103448       321        44
    ## 74        NA            79 3.843411 1.0265487       403        15
    ## 75        NA           104 2.821918 1.0630631       185        50
    ## 76        NA           106 3.445545 1.3043478       271        52
    ## 77        NA            72 2.760976 1.2571429       277        55
    ## 78        NA           106 4.079060 1.6162791       369        45
    ## 79        NA            77 3.563356 1.3628319       381        20
    ## 80        NA           110 3.857367 1.0136054       403        18
    ## 81        NA            38 2.900838 1.2195122       295        52
    ## 82        NA            74 1.995885 0.7606838       140        29
    ## 83        NA            65 2.903723 1.4273504       301        61
    ## 84        NA            86 4.224839 1.0460526       363        25
    ## 85        NA           118 3.171717 0.9051724       288        41
    ## 86        NA            63 2.417344 1.3211679       186        42
    ## 87        NA            68 4.003072 1.0114286       331        18
    ## 88        NA           115 3.444664 1.3647799       297        72
    ## 89        NA            98 3.869654 1.4228571       287        72
    ## 90        NA            96 3.789634 1.7902098       219        70
    ## 91        NA            76 3.367292 1.6557377       255        71
    ## 92        NA            72 3.372320 1.4432990       260        78
    ## 93        NA            92 3.751332 1.1974249       404        97
    ## 94        NA            59 3.609881 2.0466667       352        88
    ## 95        NA           102 3.239832 1.0744681       346        65
    ## 96        NA            81 4.567329 1.7359551       415       122
    ## 97        NA           102 3.170000 1.4867257       315        86
    ## 98        NA            70 4.745238 2.4967742       408       126
    ## 99        NA            79 3.302663 1.0990991       250        48
    ## 100       NA            84 2.841004 1.0800000       288        73
    ## 101       NA            74 2.667722 1.9481132       220       100
    ## 102       NA           100 3.817109 1.4761905       411       119
    ## 103       NA           118 4.001724 1.7709251       334       120
    ## 104       NA           102 3.978599 1.7804878       392       121
    ## 105       NA           105 4.100167 2.0046729       368       124
    ## 106       NA            83 4.054054 1.3432836       416       100
    ## 107       NA           116 4.073282 2.1727273       435       142
    ## 108       NA            86 4.557471 3.2179487       197       126
    ## 109       NA            94 4.886646 2.5743590       441       152
    ## 110       NA            89 4.572086 2.6418605       464       149
    ## 111       NA            72 3.505582 1.9832215       309       170
    ## 112       NA           103 4.222034 2.2481481       405       141
    ## 113       NA           102 3.967213 1.6019108       305       122
    ## 114       NA           115 4.130337 2.5630252       343       170
    ## 115       NA           105 4.964664 3.4530387       307       171
    ## 116       NA           100 4.183445 2.2481481       295       128
    ## 117       NA           108 4.600000 2.4847328       419       145
    ## 118       NA            46 4.604341 2.7840000       413       149
    ## 119       NA           102 4.750455 2.7441077       391       196
    ## 120       NA           107 4.357911 2.7220339       461       160
    ## 121       NA           105 3.636519 1.9342916       300       128
    ## 122       NA            68 3.990826 1.0000000       323         1
    ## 123       NA            61 2.821782 1.0000000       245         2
    ## 124       NA            70 2.963303 1.7500000       252         9
    ## 125       NA            74 3.404959 1.0833333       272         3
    ## 126       NA            72 4.382550 0.6842105       345        13
    ## 127       NA            74 3.910448 1.0000000       344         1
    ## 128       NA            65 4.145588 0.1621622       542         3
    ## 129       NA            68 2.919169 1.0000000       252         2
    ## 130       NA            70 1.856115 1.1698113        74        12
    ## 131       NA            72 3.979950 1.1666667       368        27
    ## 132       NA            77 3.646778 2.0000000       434        13
    ## 133       NA           105 4.022624 1.5529412       333        56
    ## 134       NA            70 3.215859 1.0863309       259        29
    ## 135       NA            79 4.015699 1.0066667       430        21
    ## 136       NA           111 4.298942 1.5520000       409        55
    ## 137       NA           102 3.940711 1.5234375       227        56
    ## 138       NA            63 2.633929 1.3974359       196        50
    ## 139       NA           103 4.595568 1.7500000       323        76
    ## 140       NA            63 3.607664 1.8880000       327        51
    ## 141       NA            86 4.182979 1.4408602       409        92
    ## 142       NA            94 4.520325 1.5828571       313        70
    ## 143       NA           109 4.321881 1.5568862       455        97
    ## 144       NA           107 4.298667 2.3309353       299        85
    ## 145       NA            98 3.846626 1.4976077       318        89
    ## 146       NA           114 3.094880 1.4549550       432       112
    ## 147       NA            79 3.615176 1.9052632       298        88
    ## 148       NA           103 4.050505 1.9086294       304       127
    ## 149       NA           112 3.395899 1.1722689       370        95
    ## 150       NA            92 3.866834 1.8056872       318       100
    ## 151       NA            91 3.848175 1.1855072       378        92
    ## 152       NA            86 3.774030 1.8000000       346       153
    ## 153       NA           100 3.210084 1.8369099       233       131
    ## 154       NA            87 3.723664 2.0704225       346       134
    ## 155       NA           116 4.210407 2.1157895       257       101
    ## 156       NA           105 4.592593 1.8320312       369       129
    ## 157       NA            77 4.198973 2.5324074       412       159
    ## 158       NA            98 3.960265 2.2893617       364       149
    ## 159       NA            81 3.818681 3.5180723       388       165
    ## 160       NA            95 4.063518 1.6109325       361       132
    ## 161       NA           100 4.106212 2.2313433       300       102
    ## 162       NA            91 5.134892 2.6916300       482       164
    ## 163       NA           109 4.057047 2.4911032       367       155
    ## 164       NA           109 3.871968 2.0798817       435       139
    ## 165       NA           107 4.078292 3.1313559       219       152
    ## 166       NA           105 4.737127 3.1124498       323       151
    ## 167       NA            91 4.231362 2.4932432       292       168
    ## 168       NA           100 3.642412 1.8812665       330       175
    ## 169       NA           100 4.907591 4.1318681       459       196
    ## 170       NA           112 3.675134 2.5710145       366       155
    ## 171       NA           105 4.974684 3.1857708       318       169
    ## 172       NA            95 4.164789 2.8076923       408       200
    ## 173       NA           125 4.577491 2.8690476       420       175
    ## 174       NA           100 4.127941 2.8042169       455       217
    ## 175       NA           103 5.288991 3.3037543       474       200
    ## 176       NA           112 4.116057 3.3400000       487       201
    ## 177       NA            77 4.147059 3.1193182       351       262
    ## 178       NA            70 3.573574 2.3053892       331       221
    ## 179       NA           107 3.552459 2.9875260       396       233
    ## 180       NA            83 4.316279 3.9196891       333       307
    ## 181       NA            68 3.536415 0.0000000       400         1
    ## 182       NA            72 3.743333 1.0000000       352         6
    ## 183       NA            72 3.885113 0.8947368       361        13
    ## 184       NA            68 3.339114 1.0000000       280         1
    ## 185       NA            55 2.377171 1.1200000       203        11
    ## 186       NA            68 4.009934 1.0000000       358         1
    ## 187       NA           105 4.001613 1.9743590       361        29
    ## 188       NA            74 3.961832 1.1250000       409        26
    ## 189       NA            68 3.600624 0.2727273       427        10
    ## 190       NA            66 3.714556 1.7000000       302        36
    ## 191       NA            86 3.110932 1.2100840       281        18
    ## 192       NA            72 3.814815 1.2032520       353        29
    ## 193       NA            63 3.012594 1.0974026       270        21
    ## 194       NA            66 2.035714 1.7142857       125        50
    ## 195       NA           103 3.988304 3.0000000       197       122
    ## 196       NA           102 4.415330 2.2515723       533       133
    ## 197       NA            92 3.312388 1.3204225       348        61
    ## 198       NA           120 4.137014 3.0289855       363       112
    ## 199       NA           100 4.732733 2.1348315       272        88
    ## 200       NA            95 4.256790 2.5370370       302       122
    ## 201       NA           100 3.973469 1.7405858       364       114
    ## 202       NA           103 4.262195 2.7756410       400       121
    ## 203       NA           103 3.790896 1.6479401       411        96
    ## 204       NA           114 4.019928 1.7600000       424       108
    ## 205       NA           100 4.118790 2.2325581       366       151
    ## 206       NA            92 3.997126 2.1962617       321       107
    ## 207       NA            74 2.762463 1.6179402       248        87
    ## 208       NA           100 3.705441 2.2594142       295       136
    ## 209       NA            77 4.301624 3.2571429       356       163
    ## 210       NA           109 4.658333 3.0265957       375       134
    ## 211       NA            86 3.812930 2.9900000       430       180
    ## 212       NA            61 2.736842 1.9115646       198       126
    ## 213       NA            87 4.584071 2.5613208       357       161
    ## 214       NA            96 3.683406 2.3500000       309       139
    ## 215       NA           101 5.050000 2.5159817       410       148
    ## 216       NA           107 4.083700 2.7982833       463       203
    ## 217       NA            96 3.801292 2.3178295       369       158
    ## 218       NA            83 3.471510 2.0127796       359       144
    ## 219       NA           125 4.169162 2.6475410       513       195
    ## 220       NA           109 3.991667 2.3740741       480       169
    ## 221       NA            77 4.744000 2.9072581       384       187
    ## 222       NA           116 4.697624 2.7148289       330       157
    ## 223       NA           100 5.338462 3.0775510       554       177
    ## 224       NA            75 5.309804 4.3023256       335       200
    ## 225       NA           105 4.880240 3.4809524       340       229
    ## 226       NA           100 4.085409 3.3598326       539       214
    ## 227       NA           105 3.819961 3.4065041       332       153
    ## 228       NA            86 4.857143 3.5238095       398       188
    ## 229       NA            98 4.560201 2.2485380       422       153
    ## 230       NA           107 3.667638 2.7272727       480       186
    ## 231       NA            93 5.301053 3.9292035       449       206
    ## 232       NA           100 4.251605 2.3225806       508       231
    ## 233       NA           109 4.611247 3.8300395       339       193
    ## 234       NA            75 3.905213 2.6837838       390       243
    ## 235       NA            93 4.190698 3.1622419       343       211
    ## 236       NA           108 4.458937 3.6340694       232       217
    ## 237       NA            93 4.177340 2.8620690       318       205
    ## 238       NA           105 4.131579 3.2128205       565       207
    ## 239       NA           102 5.362500 3.7310924       553       291
    ## 240       NA            68 3.864407 0.0000000       364         0
    ## 241       NA            72 4.019231 0.2000000       373         2
    ## 242       NA            66 3.996071 1.0000000       475         6
    ## 243       NA            66 3.693846 0.2000000       435         3
    ## 244       NA            63 3.637965 1.0000000       344         1
    ## 245       NA            66 3.414894 1.0000000       355         3
    ## 246       NA            72 4.421222 1.0000000       303         4
    ## 247       NA            85 4.185417 0.6756757       383         9
    ## 248       NA            86 4.165414 2.4691358       356        69
    ## 249       NA            68 4.872014 0.2553191       415         9
    ## 250       NA            77 3.859624 0.4130435       465        42
    ## 251       NA            63 3.992095 1.5299145       234        73
    ## 252       NA            72 4.157191 1.5840000       328        45
    ## 253       NA            65 2.822259 1.4133333       306        50
    ## 254       NA           112 5.185941 2.9215686       388       108
    ## 255       NA            68 3.162554 1.4102564       369        79
    ## 256       NA            95 3.673418 2.7388060       315       131
    ## 257       NA           112 3.479393 2.1791908       327       114
    ## 258       NA           100 4.564103 1.9408602       387        40
    ## 259       NA            63 2.902089 1.6511628       321        90
    ## 260       NA           107 4.384946 2.8358209       428       121
    ## 261       NA            68 1.948454 1.6363636        92        80
    ## 262       NA            92 4.223572 2.9655172       521       145
    ## 263       NA            75 4.292135 2.4232804       418       144
    ## 264       NA           103 3.867601 2.2835821       359        97
    ## 265       NA           107 4.113846 3.2000000       307       131
    ## 266       NA            91 4.137405 2.2787611       366       145
    ## 267       NA           107 5.209615 3.2380952       601       182
    ## 268       NA           122 4.329810 2.2558140       418       142
    ## 269       NA            95 2.965928 1.6688963       320       122
    ## 270       NA           100 4.543210 2.5482456       276       148
    ## 271       NA            83 3.290683 1.7591463       360       158
    ## 272       NA            90 3.419664 2.5758929       298       120
    ## 273       NA            79 4.602851 4.0434783       397       146
    ## 274       NA           103 5.433579 3.1095238       517       219
    ## 275       NA           101 5.743772 3.5371429       425       209
    ## 276       NA            86 4.566038 3.2985782       534       207
    ## 277       NA           100 3.708934 3.5852535       272       177
    ## 278       NA           116 4.424837 2.6038462       341       132
    ## 279       NA           105 3.969147 2.2892308       415       187
    ## 280       NA           114 4.746606 3.7000000       436       178
    ## 281       NA           106 3.906856 2.1420613       491       211
    ## 282       NA            94 4.983389 2.8321678       563       219
    ## 283       NA           105 3.750000 3.6072874       394       244
    ## 284       NA           103 4.069659 2.5541796       436       161
    ## 285       NA            87 4.446281 3.3750000       390       238
    ## 286       NA           118 3.877102 3.0902778       575       219
    ## 287       NA           107 4.837884 2.2506739       506       219
    ## 288       NA           120 4.917927 3.5185185       447       210
    ## 289       NA           103 4.857143 3.8225806       278       217
    ## 290       NA           105 4.658802 2.7468750       407       228
    ## 291       NA           100 3.921444 3.7749077       358       213
    ## 292       NA           101 4.910494 4.3647541       290       222
    ## 293       NA           100 4.487842 3.2301136       511       247
    ## 294       NA            77 4.232258 2.8665049       396       262
    ## 295       NA           101 4.267409 2.7413793       578       298
    ## 296       NA            97 4.345515 3.2919897       437       261
    ## 297       16            65 3.943636 0.5000000       388         2
    ## 298       11            66 3.886842 1.3333333       370         4
    ## 299       17            NA 3.474725 1.0588235       284         6
    ## 300       11            65 3.650235 1.0000000       281         3
    ## 301       46           101 4.676101 2.7600000       322        34
    ## 302        9            68 3.241422 0.0156250       444         2
    ## 303       18            83 3.965392 0.7536232       326        14
    ## 304       10            66 4.003257 2.6315789       585        12
    ## 305       29            63 4.100707 1.6447368       330        55
    ## 306       10            59 3.460548 1.1610169       351        10
    ## 307       12            68 3.860795 1.1680000       309        62
    ## 308       33           103 4.235585 2.7051282       400        73
    ## 309       22            85 4.341549 1.0843373       425       101
    ## 310       39           108 4.366972 2.2258065       322        64
    ## 311       21            72 4.349353 1.2883436       454        52
    ## 312       31           103 3.514403 1.4012346       311        58
    ## 313       18            65 3.534173 1.3052632       372        47
    ## 314       32            72 3.156695 2.1450382       256        55
    ## 315       26            74 2.483146 1.4967320       158        66
    ## 316       21            63 3.370093 1.4734513       319        98
    ## 317       36           101 3.706790 3.2439024       249       102
    ## 318       40           108 5.153639 2.7612903       383       140
    ## 319       40            94 4.347709 2.8690476       327       156
    ## 320       45           108 3.603473 2.0717489       475       185
    ## 321       44            97 5.587332 2.4292929       548       163
    ## 322       45            86 5.229885 3.7103448       486       173
    ## 323       41           101 4.186161 2.8921569       437       183
    ## 324       32            95 4.239437 2.9607843       391       164
    ## 325       35           106 4.254505 2.4800000       385       154
    ## 326       37            75 4.587179 2.7665198       462       179
    ## 327       31            97 4.388235 2.5614754       324       165
    ## 328       37            94 3.926928 2.1586207       417       170
    ## 329       48           103 5.379798 2.9027778       433       155
    ## 330       32            92 3.370937 2.2745098       342       157
    ## 331       34            97 4.239198 2.3815789       411       156
    ## 332       30            79 3.320000 2.1553398       335       197
    ## 333       47            97 4.468493 3.5049505       339       201
    ## 334       48           101 5.247093 3.5950000       383       217
    ## 335       NA            NA 4.113158 2.8480000       303       158
    ## 336       27           101 4.287582 2.7571429       260       168
    ## 337       44           100 4.664013 2.8651685       595       210
    ## 338       48            81 4.588477 3.4135021       304       245
    ## 339       36           101 4.061475 2.8526316       397       213
    ## 340       46            97 4.445872 2.9482072       491       250
    ## 341       50            97 4.080446 3.4415584       505       226
    ## 342       46           101 3.391525 3.0727969       357       219
    ## 343       44            74 3.532374 3.2781955       410       166
    ## 344       37            83 4.664286 3.8114035       359       178
    ## 345       40           110 4.211321 3.0911950       478       221
    ## 346       41            92 4.847418 3.7011952       388       210
    ## 347       48           116 4.240798 3.0774194       429       217
    ## 348       45            77 4.250853 2.6794872       374       219
    ## 349       39           103 4.472993 3.0614525       555       237
    ## 350       50           116 4.013353 2.9095355       516       235
    ## 351       46            90 4.111413 3.3643411       452       273
    ## 352       38            95 3.957230 2.9092742       367       260
    ##     tokens_MOT tokens_CHI ADOS1 nonverbalIQ1 verbalIQ1 Socialization1
    ## 1         2613          0    15           28        10             66
    ## 2          927          3    21           21         9             69
    ## 3         1631         16     3           19        13             98
    ## 4         2054         21    12           31        13             70
    ## 5          654         26    20           13        11             67
    ## 6          761         29    17           20        17             68
    ## 7          788         35    14           25        11             76
    ## 8         1418         37    14           25        11             74
    ## 9         1503         38     1           22        14            102
    ## 10        2147         40    17           28        10             82
    ## 11        1215         43    19           17        10             64
    ## 12         955         47    14           27        11             77
    ## 13        1827         58    13           27        13             86
    ## 14        1262         62     0           30        16            104
    ## 15        2144         63    20           21         9             75
    ## 16        1417         68     3           30        20             94
    ## 17        1808         83     3           27        18            104
    ## 18        1188         91     5           24        20            113
    ## 19        1361         95     0           26        18             96
    ## 20        1643         99     0           24        15             86
    ## 21        1136        101     0           21        15            106
    ## 22        2138        103    14           25        19             65
    ## 23        1625        105     0           23        17             92
    ## 24        1537        109    11           26        19             88
    ## 25        1118        109     1           24        22            115
    ## 26         584        111     0           20        16            102
    ## 27        1643        117    17           26        14             72
    ## 28        1698        118     0           21        19            106
    ## 29        2826        122    21           22         8             72
    ## 30         578        130    18           24        14             65
    ## 31        1408        137     0           24        18            100
    ## 32        1835        139     0           28        14            108
    ## 33        1564        143     5           32        31            102
    ## 34        1144        154     0           26        17             96
    ## 35        1579        166    15           27        16             79
    ## 36        2334        176     0           24        19             96
    ## 37         893        180    10           27        22             82
    ## 38        2859        197     9           34        27             82
    ## 39        1347        212     0           27        20            102
    ## 40        1787        214     9           26        14             86
    ## 41        1019        227    11           28        20             88
    ## 42        2084        233    15           30        24             88
    ## 43        1547        235     0           25        17            102
    ## 44        1805        244     4           29        22            108
    ## 45        2139        249     0           25        17            102
    ## 46        2585        254     0           29        26            102
    ## 47        2674        260     1           29        18             88
    ## 48        1344        260     1           23        21            102
    ## 49        2009        269     8           31        27             82
    ## 50        1452        286     0           27        27            108
    ## 51        1870        319     0           27        22            100
    ## 52        2267        319     0           29        22             94
    ## 53        1988        337     7           33        26             70
    ## 54        2035        398     1           29        28            104
    ## 55        2555        406     0           29        33            106
    ## 56        2740        433     0           29        22             90
    ## 57        1591        450    14           42        27             65
    ## 58        1428        461    13           34        27             85
    ## 59        2587        469    13           30        30             87
    ## 60        1170        473     0           30        30             98
    ## 61        1450        483    11           32        33            100
    ## 62        1344          4    17           28        10             82
    ## 63        1856          7    21           21         9             69
    ## 64        1393         10    14           25        11             76
    ## 65        1417         19    14           27        11             77
    ## 66        1995         32    20           13        11             67
    ## 67        1973         38    20           21         9             75
    ## 68        2274         44    15           28        10             66
    ## 69        2263         55     3           27        18            104
    ## 70        2524         78    21           22         8             72
    ## 71         999         82    14           25        11             74
    ## 72        1483         97     1           22        14            102
    ## 73        1843        107     3           19        13             98
    ## 74        2211        116    12           31        13             70
    ## 75         703        117     0           21        15            106
    ## 76        1515        120     3           30        20             94
    ## 77        1481        126    11           26        19             88
    ## 78        1702        132     0           24        18            100
    ## 79        1851        147    19           17        10             64
    ## 80        2160        148     0           28        14            108
    ## 81        1882        148    13           27        13             86
    ## 82         464        149    17           20        17             68
    ## 83        1990        157    14           25        19             65
    ## 84        1819        159     0           24        15             86
    ## 85        1375        165     5           24        20            113
    ## 86         755        169    18           24        14             65
    ## 87        2448        177    17           26        14             72
    ## 88        1551        203     0           30        16            104
    ## 89        1663        236     0           26        18             96
    ## 90        1058        244     0           26        17             96
    ## 91        1139        258     0           20        16            102
    ## 92        1511        260    15           30        24             88
    ## 93        1847        264     0           25        17            102
    ## 94        1905        287     1           24        22            115
    ## 95        1982        297     0           23        17             92
    ## 96        1845        298     0           29        22             94
    ## 97        1316        335    11           28        20             88
    ## 98        1867        356     0           27        27            108
    ## 99        1205        356    15           27        16             79
    ## 100       1822        362     9           26        14             86
    ## 101        748        368    10           27        22             82
    ## 102       2260        393     0           25        17            102
    ## 103       1959        401     4           29        22            108
    ## 104       1843        414     0           21        19            106
    ## 105       2201        423     0           24        19             96
    ## 106       2340        433     7           33        26             70
    ## 107       2290        439     1           29        28            104
    ## 108        686        449     0           30        30             98
    ## 109       2955        487     9           34        27             82
    ## 110       2694        530     1           29        18             88
    ## 111       2031        536    14           42        27             65
    ## 112       2219        542     0           27        22            100
    ## 113       2099        554     5           32        31            102
    ## 114       1657        555     8           31        27             82
    ## 115       1270        562    13           34        27             85
    ## 116       1653        571     0           27        20            102
    ## 117       2562        623     0           29        22             90
    ## 118       2534        670    13           30        30             87
    ## 119       2303        733     0           29        26            102
    ## 120       2687        738     0           29        33            106
    ## 121       1784        879     1           23        21            102
    ## 122       1590          1    21           21         9             69
    ## 123       1018          3    20           21         9             75
    ## 124       1393         14    14           27        11             77
    ## 125       1504         26    14           25        11             76
    ## 126       2417         39    17           26        14             72
    ## 127       1393         40    20           13        11             67
    ## 128       2717         41    15           28        10             66
    ## 129       1145         58    14           25        11             74
    ## 130        250         62    17           20        17             68
    ## 131       1423         77    19           17        10             64
    ## 132       2764         96    21           22         8             72
    ## 133       1562        122     1           22        14            102
    ## 134       1332        148    14           25        19             65
    ## 135       2278        151    12           31        13             70
    ## 136       2936        188     3           27        18            104
    ## 137        878        193     0           21        15            106
    ## 138        768        210    18           24        14             65
    ## 139       1504        218     3           19        13             98
    ## 140       1709        232    11           26        19             88
    ## 141       1743        252     0           24        15             86
    ## 142       1618        254    15           27        16             79
    ## 143       2149        255     0           28        14            108
    ## 144       1418        261     1           24        22            115
    ## 145       1706        299     0           26        18             96
    ## 146       1811        305     0           21        19            106
    ## 147       1159        330    11           28        20             88
    ## 148       1426        352     0           25        17            102
    ## 149       1912        354     5           24        20            113
    ## 150       1350        365    10           27        22             82
    ## 151       2328        368     0           23        17             92
    ## 152       1891        393    15           30        24             88
    ## 153       1006        403     0           20        16            102
    ## 154       2148        404    13           27        13             86
    ## 155       1610        424     4           29        22            108
    ## 156       1991        453     0           24        18            100
    ## 157       2939        468     9           34        27             82
    ## 158       1568        486     0           27        20            102
    ## 159       1788        490     8           31        27             82
    ## 160       2092        503     9           26        14             86
    ## 161       1740        528     3           30        20             94
    ## 162       2630        542     1           29        18             88
    ## 163       2124        604     0           24        19             96
    ## 164       2571        630     0           25        17            102
    ## 165       1020        660     0           30        30             98
    ## 166       1609        671     0           27        27            108
    ## 167       1470        672     0           26        17             96
    ## 168       1570        694     7           33        26             70
    ## 169       2841        698    13           30        30             87
    ## 170       2328        702     1           23        21            102
    ## 171       1463        733     0           30        16            104
    ## 172       2675        825     0           29        26            102
    ## 173       2146        825     1           29        28            104
    ## 174       2589        826     0           29        22             90
    ## 175       2142        897     0           29        22             94
    ## 176       2479        940     0           29        33            106
    ## 177       1445        983    13           34        27             85
    ## 178       2141       1023    14           42        27             65
    ## 179       1872       1246     0           27        22            100
    ## 180       1668       1293    11           32        33            100
    ## 181       2196          1    15           28        10             66
    ## 182       2054         14    21           21         9             69
    ## 183       2214         39    17           26        14             72
    ## 184       1482         45    17           28        10             82
    ## 185        861         55    20           21         9             75
    ## 186       1145         70    19           17        10             64
    ## 187       2145         74     1           23        21            102
    ## 188       1863         79    12           31        13             70
    ## 189       2283         93    21           22         8             72
    ## 190       1675        135    11           26        19             88
    ## 191       1726        142    14           25        11             76
    ## 192       1482        143    20           13        11             67
    ## 193       1054        162    14           25        19             65
    ## 194        360        205    17           20        17             68
    ## 195        632        243     0           30        16            104
    ## 196       2260        321     0           28        14            108
    ## 197       1644        334    15           27        16             79
    ## 198       1829        348     1           22        14            102
    ## 199       1387        354     0           21        15            106
    ## 200       1556        378     3           30        20             94
    ## 201       1754        384     0           26        18             96
    ## 202       1934        390     0           24        18            100
    ## 203       2345        414     3           27        18            104
    ## 204       1909        433     5           24        20            113
    ## 205       1689        434     0           25        17            102
    ## 206       1224        438    10           27        22             82
    ## 207        855        455    11           28        20             88
    ## 208       1715        477     3           19        13             98
    ## 209       1711        479     8           31        27             82
    ## 210       2069        493     5           32        31            102
    ## 211       2377        493    13           27        13             86
    ## 212        814        494    18           24        14             65
    ## 213       1517        495     0           24        15             86
    ## 214       1470        502     0           20        16            102
    ## 215       2252        510     7           33        26             70
    ## 216       2361        538     0           25        17            102
    ## 217       1984        557     0           23        17             92
    ## 218       2109        574    15           30        24             88
    ## 219       2482        576     1           29        28            104
    ## 220       2256        592     0           21        19            106
    ## 221       2685        604     9           34        27             82
    ## 222       1974        637     4           29        22            108
    ## 223       2585        642     0           29        22             94
    ## 224       1286        674    13           34        27             85
    ## 225       1452        684     0           27        27            108
    ## 226       3163        693    13           30        30             87
    ## 227       1699        694     1           24        22            115
    ## 228       2518        714    11           32        33            100
    ## 229       2485        717     9           26        14             86
    ## 230       2233        750     0           27        22            100
    ## 231       2397        754     1           29        18             88
    ## 232       3077        793     0           29        26            102
    ## 233       1726        820     0           24        19             96
    ## 234       2158        822    14           42        27             65
    ## 235       1559        973     0           27        20            102
    ## 236        798        978     0           30        30             98
    ## 237       1524       1051     0           26        17             96
    ## 238       2965       1092     0           29        33            106
    ## 239       2978       1225     0           29        22             90
    ## 240       1447          0    19           17        10             64
    ## 241       1963          5    21           22         8             72
    ## 242       2088          9    14           25        11             74
    ## 243       2070         30    15           28        10             66
    ## 244       1609         32    20           21         9             75
    ## 245       1388         33    21           21         9             69
    ## 246       1278         34    20           13        11             67
    ## 247       1917         42    14           25        11             76
    ## 248       1426        160     0           24        15             86
    ## 249       2560        179    17           26        14             72
    ## 250       2360        193    12           31        13             70
    ## 251        891        194    18           24        14             65
    ## 252       1142        197    11           28        20             88
    ## 253       1518        197    14           25        19             65
    ## 254       1986        238     1           22        14            102
    ## 255       1871        255    17           28        10             82
    ## 256       1224        304     0           27        20            102
    ## 257       1385        318     0           26        18             96
    ## 258       2345        323     5           32        31            102
    ## 259       1951        340    11           26        19             88
    ## 260       1879        346     0           24        18            100
    ## 261        209        349    17           20        17             68
    ## 262       3090        357    13           30        30             87
    ## 263       2749        404     9           34        27             82
    ## 264       2208        431     3           27        18            104
    ## 265       1207        433     3           30        20             94
    ## 266       1979        459     3           19        13             98
    ## 267       2553        472     0           28        14            108
    ## 268       1791        502     5           24        20            113
    ## 269       1527        511    15           27        16             79
    ## 270       1300        513     0           21        15            106
    ## 271       2276        531    15           30        24             88
    ## 272       1223        537    10           27        22             82
    ## 273       2082        539     8           31        27             82
    ## 274       2762        583     0           29        26            102
    ## 275       1525        586     0           27        27            108
    ## 276       2672        588     1           29        18             88
    ## 277       1098        622     0           20        16            102
    ## 278       1916        636     4           29        22            108
    ## 279       1936        651     0           25        17            102
    ## 280       1965        686     0           21        19            106
    ## 281       2729        690     0           27        22            100
    ## 282       2772        723     0           29        22             94
    ## 283       1977        725     1           24        22            115
    ## 284       2279        736     1           23        21            102
    ## 285       1864        755     0           23        17             92
    ## 286       2881        769     0           29        33            106
    ## 287       2504        792     9           26        14             86
    ## 288       1999        800     1           29        28            104
    ## 289       1067        814     0           30        30             98
    ## 290       2314        815     7           33        26             70
    ## 291       1600        875     0           24        19             96
    ## 292       1467        916     0           30        16            104
    ## 293       2668        932     0           25        17            102
    ## 294       2326       1054    14           42        27             65
    ## 295       2940       1145     0           29        22             90
    ## 296       2410       1154    11           32        33            100
    ## 297       2077          2    21           22         8             72
    ## 298       1396          8    19           17        10             64
    ## 299       1390         36    14           27        11             77
    ## 300       1454         37    14           25        11             74
    ## 301       1371         61     0           27        20            102
    ## 302       2450         64    15           28        10             66
    ## 303       1852         79    14           25        11             76
    ## 304       2202        100    21           21         9             69
    ## 305       1987        110    11           26        19             88
    ## 306       1918        137    20           21         9             75
    ## 307       1349        143    20           13        11             67
    ## 308       2271        189     3           27        18            104
    ## 309       2219        195    12           31        13             70
    ## 310       1352        197     5           32        31            102
    ## 311       2391        204    17           26        14             72
    ## 312       1481        210    15           27        16             79
    ## 313       1748        236    14           25        19             65
    ## 314        995        274    11           28        20             88
    ## 315        536        300    17           20        17             68
    ## 316       1565        306    17           28        10             82
    ## 317       1024        358     0           30        30             98
    ## 318       1866        395     1           22        14            102
    ## 319       1395        410     0           24        15             86
    ## 320       2363        418     0           27        22            100
    ## 321       2887        436     0           29        22             94
    ## 322       2564        460     1           29        18             88
    ## 323       2347        517     1           24        22            115
    ## 324       1889        521     0           20        16            102
    ## 325       1788        530     0           26        18             96
    ## 326       3182        538     9           34        27             82
    ## 327       1978        568     3           19        13             98
    ## 328       2508        571    13           27        13             86
    ## 329       2389        590     9           26        14             86
    ## 330       1540        605    10           27        22             82
    ## 331       2438        611     1           23        21            102
    ## 332       2221        618    15           30        24             88
    ## 333       1498        640     0           30        16            104
    ## 334       1701        646     0           27        27            108
    ## 335       1460        659     4           29        22            108
    ## 336       1138        666     0           21        15            106
    ## 337       2586        686     0           28        14            108
    ## 338        999        698    13           34        27             85
    ## 339       1741        702     0           25        17            102
    ## 340       2264        710     0           29        26            102
    ## 341       3072        713    13           30        30             87
    ## 342       1764        719     0           24        19             96
    ## 343       2171        738     8           31        27             82
    ## 344       1802        793     0           23        17             92
    ## 345       2044        847     0           21        19            106
    ## 346       1959        864     0           25        17            102
    ## 347       2510        897     7           33        26             70
    ## 348       2227        921    14           42        27             65
    ## 349       2895       1010     0           29        22             90
    ## 350       2576       1079     0           29        33            106
    ## 351       3076       1249    11           32        33            100
    ## 352       1731       1294     0           26        17             96

``` r
#kid nr. 57
```

USING SELECT

1.  Make a subset of the data including only kids with ASD, mlu and word
    tokens
2.  What happens if you include the name of a variable multiple times in
    a select() call?

``` r
#1
selected <- df_merged %>%  filter(Diagnosis=="ASD") %>% 
  select(SUBJ, VISIT, CHI_MLU, MOT_MLU, tokens_CHI, tokens_MOT)

#2
selected2 <- df_merged %>% 
  filter(Diagnosis == "ASD") %>% 
  select(SUBJ, SUBJ, VISIT, CHI_MLU, MOT_MLU, tokens_MOT, tokens_CHI)
#nothing happens
```

USING MUTATE, SUMMARISE and PIPES 1. Add a column to the data set that
represents the mean number of words spoken during all visits. 2. Use the
summarise function and pipes to add an column in the data set containing
the mean amount of words produced by each trial across all visits. HINT:
group by Child.ID 3. The solution to task above enables us to assess the
average amount of words produced by each child. Why don’t we just use
these average values to describe the language production of the
children? What is the advantage of keeping all the data?

``` r
#1.
df_merged %>% group_by(SUBJ) %>% mutate(mean(tokens_CHI))
```

    ## # A tibble: 352 x 21
    ## # Groups:   SUBJ [61]
    ##    SUBJ  VISIT Ethnicity Diagnosis Gender   Age  ADOS nonverbalIQ verbalIQ
    ##    <fct> <int> <fct>     <fct>     <fct>  <dbl> <int>       <int>    <int>
    ##  1 1         1 White     TD        M       19.8     0          28       14
    ##  2 1         2 White     TD        M       23.9    NA          NA       NA
    ##  3 1         3 White     TD        M       27.7    NA          NA       NA
    ##  4 1         4 White     TD        M       32.9    NA          33       NA
    ##  5 1         5 White     TD        M       35.9     0          NA       NA
    ##  6 1         6 White     TD        M       40.1    NA          42       44
    ##  7 2         1 White     ASD       M       28.8    13          34       27
    ##  8 2         2 White     ASD       M       33.2    NA          NA       NA
    ##  9 2         3 White     ASD       M       37.1    NA          NA       NA
    ## 10 2         4 White     ASD       M       41.1    NA          49       NA
    ## # ... with 342 more rows, and 12 more variables: Socialization <int>,
    ## #   MOT_MLU <dbl>, CHI_MLU <dbl>, types_MOT <int>, types_CHI <int>,
    ## #   tokens_MOT <int>, tokens_CHI <int>, ADOS1 <int>, nonverbalIQ1 <int>,
    ## #   verbalIQ1 <int>, Socialization1 <int>, `mean(tokens_CHI)` <dbl>

``` r
df_merged %>% mutate(mean(tokens_CHI))
```

    ##     SUBJ VISIT        Ethnicity Diagnosis Gender   Age ADOS nonverbalIQ
    ## 1      1     1            White        TD      M 19.80    0          28
    ## 2      1     2            White        TD      M 23.93   NA          NA
    ## 3      1     3            White        TD      M 27.70   NA          NA
    ## 4      1     4            White        TD      M 32.90   NA          33
    ## 5      1     5            White        TD      M 35.90    0          NA
    ## 6      1     6            White        TD      M 40.13   NA          42
    ## 7      2     1            White       ASD      M 28.80   13          34
    ## 8      2     2            White       ASD      M 33.17   NA          NA
    ## 9      2     3            White       ASD      M 37.07   NA          NA
    ## 10     2     4            White       ASD      M 41.07   NA          49
    ## 11     2     6            White       ASD      M 49.70   NA          48
    ## 12     3     1            White        TD      F 23.50    1          29
    ## 13     3     2            White        TD      F 27.83   NA          NA
    ## 14     3     3            White        TD      F 31.83   NA          NA
    ## 15     3     4            White        TD      F 35.53   NA          39
    ## 16     3     5            White        TD      F 39.47    0          NA
    ## 17     3     6            White        TD      F 45.07   NA          45
    ## 18     4     1     White/Latino       ASD      M 31.03    8          31
    ## 19     4     2     White/Latino       ASD      M 35.30   NA          NA
    ## 20     4     3     White/Latino       ASD      M 38.90   NA          NA
    ## 21     4     4     White/Latino       ASD      M 43.13   NA          41
    ## 22     4     5     White/Latino       ASD      M 47.40    9          NA
    ## 23     4     6     White/Latino       ASD      M 51.37   NA          44
    ## 24     5     1            White       ASD      M 34.03    9          34
    ## 25     5     2            White       ASD      M 37.57   NA          NA
    ## 26     5     3            White       ASD      M 41.57   NA          NA
    ## 27     5     4            White       ASD      M 45.53   NA          38
    ## 28     5     5            White       ASD      M 49.30    9          NA
    ## 29     5     6            White       ASD      M 54.13   NA          40
    ## 30     6     1      Bangladeshi       ASD      F 26.17   17          20
    ## 31     6     2      Bangladeshi       ASD      F 30.27   NA          NA
    ## 32     6     3      Bangledeshi       ASD      F 34.57   NA          NA
    ## 33     6     4      Bangladeshi       ASD      F 38.47   NA          33
    ## 34     6     5      Bangladeshi       ASD      F 42.43    8          NA
    ## 35     6     6      Bangladeshi       ASD      F 46.53   NA          39
    ## 36     7     1            White       ASD      F 41.00   18          24
    ## 37     7     2            White       ASD      F 49.20   NA          NA
    ## 38     7     3            White       ASD      F 49.20   NA          NA
    ## 39     7     4            White       ASD      F 52.27   NA          29
    ## 40     7     5            White       ASD      F 57.73   17          NA
    ## 41     8     1            White        TD      M 20.10    5          32
    ## 42     8     2            White        TD      M 24.07   NA          NA
    ## 43     8     4            White        TD      M 32.07   NA          42
    ## 44     8     5            White        TD      M 36.07    5          NA
    ## 45     8     6            White        TD      M 40.17   NA          43
    ## 46     9     1            White        TD      F 18.30    0          24
    ## 47     9     2            White        TD      F 23.73   NA          NA
    ## 48     9     3            White        TD      F 26.63   NA          NA
    ## 49     9     4            White        TD      F 31.07   NA          45
    ## 50     9     5            White        TD      F 35.00    1          NA
    ## 51    10     1            White        TD      M 19.27    3          27
    ## 52    10     2            White        TD      M 23.83   NA          NA
    ## 53    10     3            White        TD      M 28.53   NA          NA
    ## 54    10     4            White        TD      M 32.43   NA          31
    ## 55    10     5            White        TD      M 36.53    5          NA
    ## 56    10     6            White        TD      M 40.43   NA          33
    ## 57    11     1            White        TD      M 19.23    0          21
    ## 58    11     2            White        TD      M    NA   NA          NA
    ## 59    11     3            White        TD      M 27.27   NA          NA
    ## 60    11     4            White        TD      M 31.20   NA          35
    ## 61    11     5            White        TD      M 35.63    0          NA
    ## 62    11     6            White        TD      M 40.27   NA          42
    ## 63    12     1            White        TD      M 20.07    0          30
    ## 64    12     2            White        TD      M 23.83   NA          NA
    ## 65    12     3            White        TD      M 28.27   NA          NA
    ## 66    12     4            White        TD      M 32.07   NA          42
    ## 67    12     5            White        TD      M 35.87    0          NA
    ## 68    12     6            White        TD      M 41.50   NA          44
    ## 69    13     1            White        TD      M 19.00    0          25
    ## 70    13     2            White        TD      M 22.97   NA          NA
    ## 71    13     3            White        TD      M 27.07   NA          NA
    ## 72    13     4            White        TD      M 31.03   NA          40
    ## 73    13     5            White        TD      M 35.37    0          NA
    ## 74    13     6            White        TD      M 39.40   NA          47
    ## 75    14     1            White        TD      M 18.97    0          23
    ## 76    14     2            White        TD      M 22.87   NA          NA
    ## 77    14     3            White        TD      M 26.80   NA          NA
    ## 78    14     4            White        TD      M 31.47   NA          29
    ## 79    14     5            White        TD      M 35.10    0          NA
    ## 80    14     6            White        TD      M 39.43   NA          44
    ## 81    15     1            White        TD      M 19.27    0          24
    ## 82    15     2            White        TD      M 24.80   NA          NA
    ## 83    15     3            White        TD      M 28.10   NA          NA
    ## 84    15     4            White        TD      M 31.27   NA          33
    ## 85    15     5            White        TD      M 35.90    3          NA
    ## 86    15     6            White        TD      M 40.30   NA          40
    ## 87    16     1            White        TD      M 21.67    0          29
    ## 88    16     2            White        TD      M 26.27   NA          NA
    ## 89    16     3            White        TD      M 30.63   NA          NA
    ## 90    16     4            White        TD      M 34.27   NA          45
    ## 91    16     5            White        TD      M 38.17    0          NA
    ## 92    16     6            White        TD      M 42.93   NA          49
    ## 93    17     1            White       ASD      M 34.80   14          25
    ## 94    17     2            White       ASD      M 38.73   NA          NA
    ## 95    17     3            White       ASD      M    NA   NA          NA
    ## 96    17     5            White       ASD      M 50.93   15          NA
    ## 97    17     6            White       ASD      M 54.43   NA          27
    ## 98    18     1            White        TD      M 20.77    0          29
    ## 99    18     2            White        TD      M 26.13   NA          NA
    ## 100   18     3            White        TD      M 30.03   NA          NA
    ## 101   18     4            White        TD      M 34.43   NA          50
    ## 102   18     5            White        TD      M 38.70    0          NA
    ## 103   18     6            White        TD      M 44.07   NA          50
    ## 104   19     1            White       ASD      M 35.80   11          28
    ## 105   19     2            White       ASD      M 39.93   NA          NA
    ## 106   19     3            White       ASD      M 43.90   NA          NA
    ## 107   19     4            White       ASD      M 47.83   NA          35
    ## 108   19     5            White       ASD      M 51.97    9          NA
    ## 109   19     6            White       ASD      M 56.73   NA          30
    ## 110   20     1            White       ASD      M 18.77    9          26
    ## 111   20     2            White       ASD      M 22.50   NA          NA
    ## 112   20     3            White       ASD      M 26.77   NA          NA
    ## 113   20     4            White       ASD      M 30.80   NA          43
    ## 114   20     5            White       ASD      M 34.13    8          NA
    ## 115   20     6            White       ASD      M 37.30   NA          43
    ## 116   21     1 African American       ASD      M 27.53   21          22
    ## 117   21     2 African American       ASD      M 31.80   NA          NA
    ## 118   21     3 African American       ASD      M 36.17   NA          NA
    ## 119   21     4 African American       ASD      M 40.27   NA          27
    ## 120   21     5 African American       ASD      M 44.70   19          NA
    ## 121   21     6 African American       ASD      M 48.97   NA          26
    ## 122   22     1            White        TD      F 18.93    0          21
    ## 123   22     2            White        TD      F 22.90   NA          NA
    ## 124   22     3            White        TD      F 26.67   NA          NA
    ## 125   22     4            White        TD      F 30.93   NA          40
    ## 126   22     5            White        TD      F 35.13    0          NA
    ## 127   22     6            White        TD      F 39.23   NA          43
    ## 128   23     1     White/Latino       ASD      M 27.37   14          25
    ## 129   23     2     White/Latino       ASD      M 32.10   NA          NA
    ## 130   23     3     White/Latino       ASD      M 35.93   NA          NA
    ## 131   23     4     White/Latino       ASD      M 42.23   NA          29
    ## 132   23     5     White/Latino       ASD      M 44.93   14          NA
    ## 133   23     6     White/Latino       ASD      M 47.50   NA          30
    ## 134   24     1            White       ASD      M 37.47   20          13
    ## 135   24     2            White       ASD      M 42.20   NA          NA
    ## 136   24     3            White       ASD      M 46.37   NA          NA
    ## 137   24     4            White       ASD      M 52.13   NA          29
    ## 138   24     5            White       ASD      M 57.20   17          NA
    ## 139   24     6            White       ASD      M 62.40   NA          30
    ## 140   25     1            White        TD      M 21.77    0          29
    ## 141   25     2            White        TD      M 25.47   NA          NA
    ## 142   25     3            White        TD      M 30.13   NA          NA
    ## 143   25     4            White        TD      M 34.00   NA          47
    ## 144   25     5            White        TD      M 37.93    0          NA
    ## 145   25     6            White        TD      M 42.47   NA          48
    ## 146   26     1            White       ASD      M 30.40   11          32
    ## 147   26     3            White       ASD      M 38.60   NA          NA
    ## 148   26     4            White       ASD      M 42.63   NA          41
    ## 149   26     5            White       ASD      M 46.93    4          NA
    ## 150   26     6            White       ASD      M 51.00   NA          46
    ## 151   27     1            White        TD      M 21.03    0          26
    ## 152   27     2            White        TD      M 25.23   NA          NA
    ## 153   27     3            White        TD      M 29.07   NA          NA
    ## 154   27     4            White        TD      M 33.30   NA          42
    ## 155   27     5            White        TD      M 37.73    0          NA
    ## 156   27     6            White        TD      M 41.17   NA          43
    ## 157   28     1            White        TD      M 19.93    0          20
    ## 158   28     2            White        TD      M 23.57   NA          NA
    ## 159   28     3            White        TD      M 27.03   NA          NA
    ## 160   28     4            White        TD      M 31.00   NA          28
    ## 161   28     5            White        TD      M 36.43    0          NA
    ## 162   28     6            White        TD      M 39.43   NA          39
    ## 163   29     1            White       ASD      M 34.87   17          26
    ## 164   29     2            White       ASD      M 38.13   NA          NA
    ## 165   29     3            White       ASD      M 43.10   NA          NA
    ## 166   29     4            White       ASD      M 46.63   NA          27
    ## 167   29     5            White       ASD      M 50.97   20          NA
    ## 168   29     6            White       ASD      M 55.17   NA          39
    ## 169   30     1            White       ASD      M 36.53   12          31
    ## 170   30     2            White       ASD      M 40.50   NA          NA
    ## 171   30     3            White       ASD      M 44.67   NA          NA
    ## 172   30     4            White       ASD      M 48.10   NA          47
    ## 173   30     5            White       ASD      M 52.90   15          NA
    ## 174   30     6            White       ASD      M 56.43   NA          49
    ## 175   31     1            White        TD      M 23.13    0          27
    ## 176   31     2            White        TD      M 27.70   NA          NA
    ## 177   31     3            White        TD      M 32.07   NA          NA
    ## 178   31     4            White        TD      M 35.03   NA          43
    ## 179   31     5            White        TD      M 39.53    0          NA
    ## 180   31     6            White        TD      M 43.80   NA          45
    ## 181   32     1            White       ASD      M 34.27   21          21
    ## 182   32     2            White       ASD      M 38.33   NA          NA
    ## 183   32     3            White       ASD      M 42.13   NA          NA
    ## 184   32     4            White       ASD      M 47.10   NA          27
    ## 185   32     5            White       ASD      M 50.73   20          NA
    ## 186   32     6            White       ASD      M 54.63   NA          28
    ## 187   33     1 African American       ASD      F 25.33   14          25
    ## 188   33     2 African American       ASD      F 29.70   NA          NA
    ## 189   33     3 African American       ASD      F 34.10   NA          NA
    ## 190   33     4 African American       ASD      F 36.93   NA          36
    ## 191   33     5 African American       ASD      F 42.97   25          NA
    ## 192   33     6 African American       ASD      F 46.17   NA          33
    ## 193   34     1            White       ASD      M 33.77   10          27
    ## 194   34     2            White       ASD      M 38.13   NA          NA
    ## 195   34     3            White       ASD      M 43.00   NA          NA
    ## 196   34     4            White       ASD      M 46.13   NA          31
    ## 197   34     5            White       ASD      M 50.30   16          NA
    ## 198   34     6            White       ASD      M 53.77   NA          44
    ## 199   35     1            White        TD      M 19.30    1          23
    ## 200   35     2            White        TD      M 23.43   NA          NA
    ## 201   35     3            White        TD      M 27.13   NA          NA
    ## 202   35     4            White        TD      M 30.80   NA          36
    ## 203   35     5            White        TD      M 34.93    2          NA
    ## 204   35     6            White        TD      M 39.07   NA          45
    ## 205   36     1            White        TD      M 19.20    3          19
    ## 206   36     2            White        TD      M 22.87   NA          NA
    ## 207   36     3            White        TD      M 26.87   NA          NA
    ## 208   36     4            White        TD      M 30.40   NA          35
    ## 209   36     5            White        TD      M 34.40    2          NA
    ## 210   36     6            White        TD      M 38.53   NA          46
    ## 211   37     1            White       ASD      M 39.50    7          33
    ## 212   37     2            White       ASD      M 43.68   NA          NA
    ## 213   37     3            White       ASD      M 47.73   NA          NA
    ## 214   37     4            White       ASD      M 51.67   NA          49
    ## 215   37     5            White       ASD      M 55.17    4          NA
    ## 216   37     6            White       ASD      M 58.77   NA          50
    ## 217   38     1            White        TD      F 19.87    1          29
    ## 218   38     2            White        TD      F 24.17   NA          NA
    ## 219   38     3            White        TD      F 28.07   NA          NA
    ## 220   38     4            White        TD      F 32.17   NA          44
    ## 221   38     5            White        TD      F 36.13    0          NA
    ## 222   39     1      White/Asian       ASD      M 33.20   11          26
    ## 223   39     2      White/Asian       ASD      M 37.37   NA          NA
    ## 224   39     3      White/Asian       ASD      M 41.73   NA          NA
    ## 225   39     4      White/Asian       ASD      M 45.33   NA          45
    ## 226   39     5      White/Asian       ASD      M 48.63   11          NA
    ## 227   39     6      White/Asian       ASD      M 53.63   NA          30
    ## 228   40     1         Lebanese       ASD      M 24.90   13          27
    ## 229   40     2         Lebanese       ASD      M 32.20   NA          NA
    ## 230   40     3         Lebanese       ASD      M 34.00   NA          NA
    ## 231   40     4         Lebanese       ASD      M    NA   NA          NA
    ## 232   40     6         Lebanese       ASD      M 46.40   NA          41
    ## 233   41     1            White        TD      M 19.23    0          25
    ## 234   41     2            White        TD      M 23.07   NA          NA
    ## 235   41     3            White        TD      M 28.47   NA          NA
    ## 236   41     4            White        TD      M 32.07   NA          41
    ## 237   41     5            White        TD      M 35.67    0          NA
    ## 238   41     6            White        TD      M 39.93   NA          45
    ## 239   42     1            White        TD      M 19.37    0          24
    ## 240   42     2            White        TD      M    NA   NA          NA
    ## 241   42     3            White        TD      M 28.90   NA          NA
    ## 242   42     4            White        TD      M 32.07   NA          39
    ## 243   42     5            White        TD      M 36.40    0          NA
    ## 244   42     6            White        TD      M 40.13   NA          46
    ## 245   43     1            White       ASD      M 37.03   14          42
    ## 246   43     2            White       ASD      M 42.77   NA          NA
    ## 247   43     3            White       ASD      M 46.23   NA          NA
    ## 248   43     4            White       ASD      M 49.30   NA          50
    ## 249   43     5            White       ASD      M 53.40   11          NA
    ## 250   43     6            White       ASD      M 57.37   NA          49
    ## 251   44     1            White        TD      M 19.77    3          30
    ## 252   44     2            White        TD      M 23.63   NA          NA
    ## 253   44     3            White        TD      M 27.67   NA          NA
    ## 254   44     4            White        TD      M 32.07   NA          40
    ## 255   44     5            White        TD      M 35.83    1          NA
    ## 256   45     1            White        TD      M 20.03    5          24
    ## 257   45     2            White        TD      M 24.68   NA          NA
    ## 258   45     3            White        TD      M 28.63   NA          NA
    ## 259   45     4            White        TD      M 32.77   NA          26
    ## 260   45     5            White        TD      M 36.70   10          NA
    ## 261   46     1            White       ASD      M 36.73   20          21
    ## 262   46     2            White       ASD      M 41.50   NA          NA
    ## 263   46     3            White       ASD      M 45.57   NA          NA
    ## 264   46     4            White       ASD      M 50.23   NA          29
    ## 265   46     5            White       ASD      M 54.33   17          NA
    ## 266   46     6            White       ASD      M 57.43   NA          32
    ## 267   47     1            White        TD      F 20.03    0          27
    ## 268   47     2            White        TD      F 24.40   NA          NA
    ## 269   47     3            White        TD      F 29.53   NA          NA
    ## 270   47     4            White        TD      F 32.13   NA          42
    ## 271   47     5            White        TD      F 39.10   NA          NA
    ## 272   47     6            White        TD      F 40.37   NA          43
    ## 273   48     1            White       ASD      M 31.63   17          28
    ## 274   48     2            White       ASD      M 37.20   NA          NA
    ## 275   48     4            White       ASD      M 43.63   NA          33
    ## 276   48     5            White       ASD      M 52.77   17          NA
    ## 277   48     6            White       ASD      M    NA   NA          32
    ## 278   49     1            White        TD      M 23.07    0          27
    ## 279   49     2            White        TD      M 27.47   NA          NA
    ## 280   49     3            White        TD      M 31.63   NA          NA
    ## 281   49     4            White        TD      M 35.63   NA          45
    ## 282   49     5            White        TD      M 39.47    0          NA
    ## 283   49     6            White        TD      M 43.40   NA          45
    ## 284   50     1            White       ASD      M 37.47   19          17
    ## 285   50     2            White       ASD      M 42.20   NA          NA
    ## 286   50     3            White       ASD      M 46.37   NA          NA
    ## 287   50     4            White       ASD      M 52.13   NA          22
    ## 288   50     5            White       ASD      M 57.20   17          NA
    ## 289   50     6            White       ASD      M 62.40   NA          24
    ## 290   51     1            Asian        TD      F 20.87    1          22
    ## 291   51     2            Asian        TD      F 25.40   NA          NA
    ## 292   51     3            Asian        TD      F 29.67   NA          NA
    ## 293   51     4            Asian        TD      F 34.43   NA          30
    ## 294   51     5            Asian        TD      F 37.67    3          NA
    ## 295   51     6            Asian        TD      F 42.10   NA          46
    ## 296   52     1            White        TD      M 22.57    0          29
    ## 297   52     2            White        TD      M 26.63   NA          NA
    ## 298   52     3            White        TD      M 30.77   NA          NA
    ## 299   52     4            White        TD      M 35.03   NA          45
    ## 300   52     5            White        TD      M 38.60    0          NA
    ## 301   52     6            White        TD      M 43.03   NA          47
    ## 302   53     1            White        TD      M 23.90    0          30
    ## 303   53     2            White        TD      M 28.60   NA          NA
    ## 304   53     3            White        TD      M 32.50   NA          NA
    ## 305   53     4            White        TD      M 36.40   NA          45
    ## 306   53     5            White        TD      M 40.07    0          NA
    ## 307   53     6            White        TD      M 44.43   NA          45
    ## 308   54     1            White        TD      M 19.10    1          24
    ## 309   54     2            White        TD      M 23.17   NA          NA
    ## 310   54     3            White        TD      M 27.07   NA          NA
    ## 311   54     4            White        TD      M 30.83   NA          39
    ## 312   54     5            White        TD      M 35.17    0          NA
    ## 313   54     6            White        TD      M 39.30   NA          41
    ## 314   55     1            White        TD      M 19.97    0          26
    ## 315   55     2            White        TD      M 23.93   NA          NA
    ## 316   55     3            White        TD      M 28.03   NA          NA
    ## 317   55     4            White        TD      M 32.03   NA          40
    ## 318   55     6            White        TD      M 41.93   NA          45
    ## 319   56     1            White       ASD      M 35.50   14          27
    ## 320   56     2            White       ASD      M 39.40   NA          NA
    ## 321   56     3            White       ASD      M 44.47   NA          NA
    ## 322   56     6            White       ASD      M    NA   NA          34
    ## 323   57     1            White       ASD      F 41.07   15          28
    ## 324   57     2            White       ASD      F 41.07   NA          NA
    ## 325   57     3            White       ASD      F 49.97   NA          NA
    ## 326   57     4            White       ASD      F 53.90   NA          30
    ## 327   57     5            White       ASD      F 58.27   13          NA
    ## 328   57     6            White       ASD      F 61.70   NA          32
    ## 329   58     1            White       ASD      M 26.00   15          30
    ## 330   58     2            White       ASD      M 30.10   NA          NA
    ## 331   58     3            White       ASD      M 34.27   NA          NA
    ## 332   58     4            White       ASD      M 38.20   NA          40
    ## 333   58     5            White       ASD      M 42.30   16          NA
    ## 334   58     6            White       ASD      M 46.07   NA          45
    ## 335   59     1            White        TD      M 20.80    4          29
    ## 336   59     2            White        TD      M 25.87   NA          NA
    ## 337   59     3            White        TD      M 29.67   NA          NA
    ## 338   59     4            White        TD      M 33.60   NA          34
    ## 339   59     5            White        TD      M 37.34    5          NA
    ## 340   59     6            White        TD      M 41.00   NA          NA
    ## 341   60     1            White       ASD      M 34.00   13          30
    ## 342   60     2            White       ASD      M 38.63   NA          NA
    ## 343   60     3            White       ASD      M 42.47   NA          NA
    ## 344   60     4            White       ASD      M 47.00   NA          47
    ## 345   60     5            White       ASD      M 51.13   15          NA
    ## 346   60     6            White       ASD      M 54.73   NA          50
    ## 347   61     1            White       ASD      M 42.00   15          27
    ## 348   61     2            White       ASD      M 46.77   NA          NA
    ## 349   61     3            White       ASD      M 50.63   NA          NA
    ## 350   61     4            White       ASD      M 54.13   NA          28
    ## 351   61     5            White       ASD      M 58.57   15          NA
    ## 352   61     6            White       ASD      M 62.33   NA          41
    ##     verbalIQ Socialization  MOT_MLU   CHI_MLU types_MOT types_CHI
    ## 1         14           108 3.621993 1.2522523       378        14
    ## 2         NA           110 3.857367 1.0136054       403        18
    ## 3         NA           109 4.321881 1.5568862       455        97
    ## 4         NA           102 4.415330 2.2515723       533       133
    ## 5         NA           107 5.209615 3.2380952       601       182
    ## 6         44           100 4.664013 2.8651685       595       210
    ## 7         27            85 4.098446 2.2768595       317       146
    ## 8         NA           105 4.964664 3.4530387       307       171
    ## 9         NA            77 4.147059 3.1193182       351       262
    ## 10        NA            75 5.309804 4.3023256       335       200
    ## 11        48            81 4.588477 3.4135021       304       245
    ## 12        18            88 3.757269 1.8776978       334        51
    ## 13        NA            89 4.572086 2.6418605       464       149
    ## 14        NA            91 5.134892 2.6916300       482       164
    ## 15        NA            93 5.301053 3.9292035       449       206
    ## 16        NA            86 4.566038 3.2985782       534       207
    ## 17        45            86 5.229885 3.7103448       486       173
    ## 18        27            82 3.459370 2.0972222       379       102
    ## 19        NA           115 4.130337 2.5630252       343       170
    ## 20        NA            81 3.818681 3.5180723       388       165
    ## 21        NA            77 4.301624 3.2571429       356       163
    ## 22        NA            79 4.602851 4.0434783       397       146
    ## 23        44            74 3.532374 3.2781955       410       166
    ## 24        27            82 3.986357 1.3947368       324        57
    ## 25        NA            94 4.886646 2.5743590       441       152
    ## 26        NA            77 4.198973 2.5324074       412       159
    ## 27        NA            77 4.744000 2.9072581       384       187
    ## 28        NA            75 4.292135 2.4232804       418       144
    ## 29        37            75 4.587179 2.7665198       462       179
    ## 30        17            68 2.618729 1.0000000       212         4
    ## 31        NA            74 1.995885 0.7606838       140        29
    ## 32        NA            70 1.856115 1.1698113        74        12
    ## 33        NA            66 2.035714 1.7142857       125        50
    ## 34        NA            68 1.948454 1.6363636        92        80
    ## 35        26            74 2.483146 1.4967320       158        66
    ## 36        14            65 2.244755 1.2641509       152        29
    ## 37        NA            63 2.417344 1.3211679       186        42
    ## 38        NA            63 2.633929 1.3974359       196        50
    ## 39        NA            61 2.736842 1.9115646       198       126
    ## 40        NA            63 3.992095 1.5299145       234        73
    ## 41        31           102 3.265082 1.5600000       288        59
    ## 42        NA           102 3.967213 1.6019108       305       122
    ## 43        NA           109 4.658333 3.0265957       375       134
    ## 44        NA           100 4.564103 1.9408602       387        40
    ## 45        39           108 4.366972 2.2258065       322        64
    ## 46        18           100 3.544419 1.0378788       363        36
    ## 47        NA           106 4.079060 1.6162791       369        45
    ## 48        NA           105 4.592593 1.8320312       369       129
    ## 49        NA           103 4.262195 2.7756410       400       121
    ## 50        NA           107 4.384946 2.8358209       428       121
    ## 51        18           104 4.204846 1.0375000       289        15
    ## 52        NA           106 4.378840 1.1200000       358        18
    ## 53        NA           111 4.298942 1.5520000       409        55
    ## 54        NA           103 3.790896 1.6479401       411        96
    ## 55        NA           103 3.867601 2.2835821       359        97
    ## 56        33           103 4.235585 2.7051282       400        73
    ## 57        15           106 3.380463 1.2168675       215        24
    ## 58        NA           104 2.821918 1.0630631       185        50
    ## 59        NA           102 3.940711 1.5234375       227        56
    ## 60        NA           100 4.732733 2.1348315       272        88
    ## 61        NA           100 4.543210 2.5482456       276       148
    ## 62        27           101 4.287582 2.7571429       260       168
    ## 63        16           104 4.195335 1.0877193       235        17
    ## 64        NA           115 3.444664 1.3647799       297        72
    ## 65        NA           105 4.974684 3.1857708       318       169
    ## 66        NA           103 3.988304 3.0000000       197       122
    ## 67        NA           101 4.910494 4.3647541       290       222
    ## 68        47            97 4.468493 3.5049505       339       201
    ## 69        17           102 3.960254 1.5740741       329        69
    ## 70        NA           100 3.817109 1.4761905       411       119
    ## 71        NA           109 3.871968 2.0798817       435       139
    ## 72        NA           107 4.083700 2.7982833       463       203
    ## 73        NA           100 4.487842 3.2301136       511       247
    ## 74        41            92 4.847418 3.7011952       388       210
    ## 75        17            92 3.420315 1.0396040       287         7
    ## 76        NA           102 3.239832 1.0744681       346        65
    ## 77        NA            91 3.848175 1.1855072       378        92
    ## 78        NA            96 3.801292 2.3178295       369       158
    ## 79        NA            87 4.446281 3.3750000       390       238
    ## 80        37            83 4.664286 3.8114035       359       178
    ## 81        15            86 3.967078 1.1647059       277        27
    ## 82        NA            86 4.224839 1.0460526       363        25
    ## 83        NA            86 4.182979 1.4408602       409        92
    ## 84        NA            87 4.584071 2.5613208       357       161
    ## 85        NA            86 4.165414 2.4691358       356        69
    ## 86        40            94 4.347709 2.8690476       327       156
    ## 87        26           102 4.910190 1.5988372       375        90
    ## 88        NA           102 4.750455 2.7441077       391       196
    ## 89        NA            95 4.164789 2.8076923       408       200
    ## 90        NA           100 4.251605 2.3225806       508       231
    ## 91        NA           103 5.433579 3.1095238       517       219
    ## 92        46            97 4.445872 2.9482072       491       250
    ## 93        11            74 3.182390 1.0277778       281         9
    ## 94        NA           104 2.947230 1.0125000       243         7
    ## 95        NA            68 2.919169 1.0000000       252         2
    ## 96        NA            66 3.996071 1.0000000       475         6
    ## 97        11            65 3.650235 1.0000000       281         3
    ## 98        33           106 3.587855 1.7948718       467       108
    ## 99        NA           107 4.357911 2.7220339       461       160
    ## 100       NA           112 4.116057 3.3400000       487       201
    ## 101       NA           105 4.131579 3.2128205       565       207
    ## 102       NA           118 3.877102 3.0902778       575       219
    ## 103       50           116 4.013353 2.9095355       516       235
    ## 104       20            88 2.539823 1.3595506       283        89
    ## 105       NA           102 3.170000 1.4867257       315        86
    ## 106       NA            79 3.615176 1.9052632       298        88
    ## 107       NA            74 2.762463 1.6179402       248        87
    ## 108       NA            72 4.157191 1.5840000       328        45
    ## 109       32            72 3.156695 2.1450382       256        55
    ## 110       14            86 2.524740 0.1857143       321        16
    ## 111       NA            84 2.841004 1.0800000       288        73
    ## 112       NA            95 4.063518 1.6109325       361       132
    ## 113       NA            98 4.560201 2.2485380       422       153
    ## 114       NA           107 4.837884 2.2506739       506       219
    ## 115       48           103 5.379798 2.9027778       433       155
    ## 116        8            72 4.390879 1.0000000       485         8
    ## 117       NA            74 3.381510 1.0400000       426         9
    ## 118       NA            77 3.646778 2.0000000       434        13
    ## 119       NA            68 3.600624 0.2727273       427        10
    ## 120       NA            72 4.019231 0.2000000       373         2
    ## 121       16            65 3.943636 0.5000000       388         2
    ## 122       19           106 3.630476 1.2371134       343        36
    ## 123       NA           102 3.978599 1.7804878       392       121
    ## 124       NA           114 3.094880 1.4549550       432       112
    ## 125       NA           109 3.991667 2.3740741       480       169
    ## 126       NA           114 4.746606 3.7000000       436       178
    ## 127       40           110 4.211321 3.0911950       478       221
    ## 128       19            65 3.024548 1.4324324       328        41
    ## 129       NA            65 2.903723 1.4273504       301        61
    ## 130       NA            70 3.215859 1.0863309       259        29
    ## 131       NA            63 3.012594 1.0974026       270        21
    ## 132       NA            65 2.822259 1.4133333       306        50
    ## 133       18            65 3.534173 1.3052632       372        47
    ## 134       11            67 2.917355 1.0833333       193         6
    ## 135       NA            78 3.815141 1.0000000       434         6
    ## 136       NA            74 3.910448 1.0000000       344         1
    ## 137       NA            72 3.814815 1.2032520       353        29
    ## 138       NA            72 4.421222 1.0000000       303         4
    ## 139       12            68 3.860795 1.1680000       309        62
    ## 140       22            90 4.643200 1.5827338       373        71
    ## 141       NA           108 4.600000 2.4847328       419       145
    ## 142       NA           100 4.127941 2.8042169       455       217
    ## 143       NA           102 5.362500 3.7310924       553       291
    ## 144       NA           101 4.267409 2.7413793       578       298
    ## 145       39           103 4.472993 3.0614525       555       237
    ## 146       33           100 4.690751 3.4000000       278       119
    ## 147       NA            83 4.316279 3.9196891       333       307
    ## 148       NA            86 4.857143 3.5238095       398       188
    ## 149       NA            97 4.345515 3.2919897       437       261
    ## 150       46            90 4.111413 3.3643411       452       273
    ## 151       18            96 3.616867 1.3661972       317        37
    ## 152       NA            98 3.869654 1.4228571       287        72
    ## 153       NA            98 3.846626 1.4976077       318        89
    ## 154       NA           100 3.973469 1.7405858       364       114
    ## 155       NA           112 3.479393 2.1791908       327       114
    ## 156       35           106 4.254505 2.4800000       385       154
    ## 157       16           102 2.788793 1.2758621       178        34
    ## 158       NA            76 3.367292 1.6557377       255        71
    ## 159       NA           100 3.210084 1.8369099       233       131
    ## 160       NA            96 3.683406 2.3500000       309       139
    ## 161       NA           100 3.708934 3.5852535       272       177
    ## 162       32            95 4.239437 2.9607843       391       164
    ## 163       14            72 3.304189 1.0086207       295         6
    ## 164       NA            68 4.003072 1.0114286       331        18
    ## 165       NA            72 4.382550 0.6842105       345        13
    ## 166       NA            72 3.885113 0.8947368       361        13
    ## 167       NA            68 4.872014 0.2553191       415         9
    ## 168       21            72 4.349353 1.2883436       454        52
    ## 169       13            70 3.607088 0.9000000       366        11
    ## 170       NA            79 3.843411 1.0265487       403        15
    ## 171       NA            79 4.015699 1.0066667       430        21
    ## 172       NA            74 3.961832 1.1250000       409        26
    ## 173       NA            77 3.859624 0.4130435       465        42
    ## 174       22            85 4.341549 1.0843373       425       101
    ## 175       22           100 3.494327 1.5333333       322        91
    ## 176       NA           103 4.222034 2.2481481       405       141
    ## 177       NA           107 3.552459 2.9875260       396       233
    ## 178       NA           107 3.667638 2.7272727       480       186
    ## 179       NA           106 3.906856 2.1420613       491       211
    ## 180       45           108 3.603473 2.0717489       475       185
    ## 181        9            69 3.686347 1.5000000       228         3
    ## 182       NA            80 4.142562 1.0000000       454         2
    ## 183       NA            68 3.990826 1.0000000       323         1
    ## 184       NA            72 3.743333 1.0000000       352         6
    ## 185       NA            66 3.414894 1.0000000       355         3
    ## 186       10            66 4.003257 2.6315789       585        12
    ## 187       11            76 2.287293 1.2500000       206        13
    ## 188       NA            74 2.879925 1.0000000       285         2
    ## 189       NA            74 3.404959 1.0833333       272         3
    ## 190       NA            86 3.110932 1.2100840       281        18
    ## 191       NA            85 4.185417 0.6756757       383         9
    ## 192       18            83 3.965392 0.7536232       326        14
    ## 193       22            82 2.743455 1.3766234       214        67
    ## 194       NA            74 2.667722 1.9481132       220       100
    ## 195       NA            92 3.866834 1.8056872       318       100
    ## 196       NA            92 3.997126 2.1962617       321       107
    ## 197       NA            90 3.419664 2.5758929       298       120
    ## 198       32            92 3.370937 2.2745098       342       157
    ## 199       21           102 3.561364 1.2641509       291        24
    ## 200       NA           105 3.636519 1.9342916       300       128
    ## 201       NA           112 3.675134 2.5710145       366       155
    ## 202       NA           105 4.001613 1.9743590       361        29
    ## 203       NA           103 4.069659 2.5541796       436       161
    ## 204       34            97 4.239198 2.3815789       411       156
    ## 205       13            98 3.921109 1.2307692       281         8
    ## 206       NA           102 3.942164 1.3103448       321        44
    ## 207       NA           103 4.595568 1.7500000       323        76
    ## 208       NA           100 3.705441 2.2594142       295       136
    ## 209       NA            91 4.137405 2.2787611       366       145
    ## 210       31            97 4.388235 2.5614754       324       165
    ## 211       26            70 4.135036 0.4805825       381        39
    ## 212       NA            83 4.054054 1.3432836       416       100
    ## 213       NA           100 3.642412 1.8812665       330       175
    ## 214       NA           101 5.050000 2.5159817       410       148
    ## 215       NA           105 4.658802 2.7468750       407       228
    ## 216       48           116 4.240798 3.0774194       429       217
    ## 217       28           104 3.420975 1.3322785       342        96
    ## 218       NA           116 4.073282 2.1727273       435       142
    ## 219       NA           125 4.577491 2.8690476       420       175
    ## 220       NA           125 4.169162 2.6475410       513       195
    ## 221       NA           120 4.917927 3.5185185       447       210
    ## 222       19            88 3.298748 1.2043011       274        36
    ## 223       NA            72 2.760976 1.2571429       277        55
    ## 224       NA            63 3.607664 1.8880000       327        51
    ## 225       NA            66 3.714556 1.7000000       302        36
    ## 226       NA            63 2.902089 1.6511628       321        90
    ## 227       29            63 4.100707 1.6447368       330        55
    ## 228       13            86 2.997050 1.0175439       252         9
    ## 229       NA            38 2.900838 1.2195122       295        52
    ## 230       NA            87 3.723664 2.0704225       346       134
    ## 231       NA            86 3.812930 2.9900000       430       180
    ## 232       37            94 3.926928 2.1586207       417       170
    ## 233       17           102 3.093146 1.0262009       333        32
    ## 234       NA            92 3.751332 1.1974249       404        97
    ## 235       NA           103 4.050505 1.9086294       304       127
    ## 236       NA           100 4.118790 2.2325581       366       151
    ## 237       NA           105 3.969147 2.2892308       415       187
    ## 238       36           101 4.061475 2.8526316       397       213
    ## 239       19            96 4.033333 1.3034483       373        47
    ## 240       NA           105 4.100167 2.0046729       368       124
    ## 241       NA           109 4.057047 2.4911032       367       155
    ## 242       NA           109 4.611247 3.8300395       339       193
    ## 243       NA           100 3.921444 3.7749077       358       213
    ## 244       46           101 3.391525 3.0727969       357       219
    ## 245       27            65 3.341418 1.9144981       271       101
    ## 246       NA            72 3.505582 1.9832215       309       170
    ## 247       NA            70 3.573574 2.3053892       331       221
    ## 248       NA            75 3.905213 2.6837838       390       243
    ## 249       NA            77 4.232258 2.8665049       396       262
    ## 250       45            77 4.250853 2.6794872       374       219
    ## 251       20            94 3.088757 1.2592593       275         9
    ## 252       NA           106 3.445545 1.3043478       271        52
    ## 253       NA           100 4.106212 2.2313433       300       102
    ## 254       NA            95 4.256790 2.5370370       302       122
    ## 255       NA           107 4.113846 3.2000000       307       131
    ## 256       20           113 2.776181 0.5584416       258        20
    ## 257       NA           118 3.171717 0.9051724       288        41
    ## 258       NA           112 3.395899 1.1722689       370        95
    ## 259       NA           114 4.019928 1.7600000       424       108
    ## 260       NA           122 4.329810 2.2558140       418       142
    ## 261        9            75 4.883966 1.1666667       387        10
    ## 262       NA            74 3.830795 1.2258065       392         6
    ## 263       NA            61 2.821782 1.0000000       245         2
    ## 264       NA            55 2.377171 1.1200000       203        11
    ## 265       NA            63 3.637965 1.0000000       344         1
    ## 266       10            59 3.460548 1.1610169       351        10
    ## 267       20           102 3.943005 1.0761421       260        13
    ## 268       NA           100 4.183445 2.2481481       295       128
    ## 269       NA            98 3.960265 2.2893617       364       149
    ## 270       NA            93 4.190698 3.1622419       343       211
    ## 271       NA            95 3.673418 2.7388060       315       131
    ## 272       46           101 4.676101 2.7600000       322        34
    ## 273       10            82 3.765528 1.2500000       303        17
    ## 274       NA            66 3.555804 1.0000000       272         2
    ## 275       NA            68 3.339114 1.0000000       280         1
    ## 276       NA            68 3.162554 1.4102564       369        79
    ## 277       21            63 3.370093 1.4734513       319        98
    ## 278       27           108 4.030075 1.4258373       255        73
    ## 279       NA            70 4.745238 2.4967742       408       126
    ## 280       NA           105 4.737127 3.1124498       323       151
    ## 281       NA           105 4.880240 3.4809524       340       229
    ## 282       NA           101 5.743772 3.5371429       425       209
    ## 283       48           101 5.247093 3.5950000       383       217
    ## 284       10            64 3.704110 1.1000000       265         8
    ## 285       NA            77 3.563356 1.3628319       381        20
    ## 286       NA            72 3.979950 1.1666667       368        27
    ## 287       NA            68 4.009934 1.0000000       358         1
    ## 288       NA            68 3.864407 0.0000000       364         0
    ## 289       11            66 3.886842 1.3333333       370         4
    ## 290       14           102 3.435743 1.1818182       331         7
    ## 291       NA           109 4.073350 1.1976744       309        20
    ## 292       NA           105 4.022624 1.5529412       333        56
    ## 293       NA           120 4.137014 3.0289855       363       112
    ## 294       NA           112 5.185941 2.9215686       388       108
    ## 295       40           108 5.153639 2.7612903       383       140
    ## 296       22            94 5.344227 1.4086957       441        92
    ## 297       NA            81 4.567329 1.7359551       415       122
    ## 298       NA           103 5.288991 3.3037543       474       200
    ## 299       NA           100 5.338462 3.0775510       554       177
    ## 300       NA            94 4.983389 2.8321678       563       219
    ## 301       44            97 5.587332 2.4292929       548       163
    ## 302       30            98 4.159159 1.9397163       236       120
    ## 303       NA            86 4.557471 3.2179487       197       126
    ## 304       NA           107 4.078292 3.1313559       219       152
    ## 305       NA           108 4.458937 3.6340694       232       217
    ## 306       NA           103 4.857143 3.8225806       278       217
    ## 307       36           101 3.706790 3.2439024       249       102
    ## 308       22           115 3.487871 1.3139535       214        32
    ## 309       NA            59 3.609881 2.0466667       352        88
    ## 310       NA           107 4.298667 2.3309353       299        85
    ## 311       NA           105 3.819961 3.4065041       332       153
    ## 312       NA           105 3.750000 3.6072874       394       244
    ## 313       41           101 4.186161 2.8921569       437       183
    ## 314       17            96 3.509138 1.1846154       178        11
    ## 315       NA            96 3.789634 1.7902098       219        70
    ## 316       NA            91 4.231362 2.4932432       292       168
    ## 317       NA            93 4.177340 2.8620690       318       205
    ## 318       38            95 3.957230 2.9092742       367       260
    ## 319       11            77 2.548969 1.0444444       195         9
    ## 320       NA            65 3.430642 1.0000000       285         1
    ## 321       NA            70 2.963303 1.7500000       252         9
    ## 322       17            NA 3.474725 1.0588235       284         6
    ## 323       10            66 3.833770 0.0000000       386         0
    ## 324       NA            66 3.688478 1.0000000       389         1
    ## 325       NA            65 4.145588 0.1621622       542         3
    ## 326       NA            68 3.536415 0.0000000       400         1
    ## 327       NA            66 3.693846 0.2000000       435         3
    ## 328        9            68 3.241422 0.0156250       444         2
    ## 329       24            88 2.747100 1.1809045       338        98
    ## 330       NA            72 3.372320 1.4432990       260        78
    ## 331       NA            86 3.774030 1.8000000       346       153
    ## 332       NA            83 3.471510 2.0127796       359       144
    ## 333       NA            83 3.290683 1.7591463       360       158
    ## 334       30            79 3.320000 2.1553398       335       197
    ## 335       22           108 3.432387 1.1830065       345        62
    ## 336       NA           118 4.001724 1.7709251       334       120
    ## 337       NA           116 4.210407 2.1157895       257       101
    ## 338       NA           116 4.697624 2.7148289       330       157
    ## 339       NA           116 4.424837 2.6038462       341       132
    ## 340       NA            NA 4.113158 2.8480000       303       158
    ## 341       30            87 3.604140 2.8763441       400       149
    ## 342       NA            46 4.604341 2.7840000       413       149
    ## 343       NA           100 4.907591 4.1318681       459       196
    ## 344       NA           100 4.085409 3.3598326       539       214
    ## 345       NA            92 4.223572 2.9655172       521       145
    ## 346       50            97 4.080446 3.4415584       505       226
    ## 347       16            79 3.030405 1.0375000       303        15
    ## 348       NA            79 3.302663 1.0990991       250        48
    ## 349       NA            94 4.520325 1.5828571       313        70
    ## 350       NA            92 3.312388 1.3204225       348        61
    ## 351       NA            95 2.965928 1.6688963       320       122
    ## 352       31           103 3.514403 1.4012346       311        58
    ##     tokens_MOT tokens_CHI ADOS1 nonverbalIQ1 verbalIQ1 Socialization1
    ## 1         1835        139     0           28        14            108
    ## 2         2160        148     0           28        14            108
    ## 3         2149        255     0           28        14            108
    ## 4         2260        321     0           28        14            108
    ## 5         2553        472     0           28        14            108
    ## 6         2586        686     0           28        14            108
    ## 7         1428        461    13           34        27             85
    ## 8         1270        562    13           34        27             85
    ## 9         1445        983    13           34        27             85
    ## 10        1286        674    13           34        27             85
    ## 11         999        698    13           34        27             85
    ## 12        2674        260     1           29        18             88
    ## 13        2694        530     1           29        18             88
    ## 14        2630        542     1           29        18             88
    ## 15        2397        754     1           29        18             88
    ## 16        2672        588     1           29        18             88
    ## 17        2564        460     1           29        18             88
    ## 18        2009        269     8           31        27             82
    ## 19        1657        555     8           31        27             82
    ## 20        1788        490     8           31        27             82
    ## 21        1711        479     8           31        27             82
    ## 22        2082        539     8           31        27             82
    ## 23        2171        738     8           31        27             82
    ## 24        2859        197     9           34        27             82
    ## 25        2955        487     9           34        27             82
    ## 26        2939        468     9           34        27             82
    ## 27        2685        604     9           34        27             82
    ## 28        2749        404     9           34        27             82
    ## 29        3182        538     9           34        27             82
    ## 30         761         29    17           20        17             68
    ## 31         464        149    17           20        17             68
    ## 32         250         62    17           20        17             68
    ## 33         360        205    17           20        17             68
    ## 34         209        349    17           20        17             68
    ## 35         536        300    17           20        17             68
    ## 36         578        130    18           24        14             65
    ## 37         755        169    18           24        14             65
    ## 38         768        210    18           24        14             65
    ## 39         814        494    18           24        14             65
    ## 40         891        194    18           24        14             65
    ## 41        1564        143     5           32        31            102
    ## 42        2099        554     5           32        31            102
    ## 43        2069        493     5           32        31            102
    ## 44        2345        323     5           32        31            102
    ## 45        1352        197     5           32        31            102
    ## 46        1408        137     0           24        18            100
    ## 47        1702        132     0           24        18            100
    ## 48        1991        453     0           24        18            100
    ## 49        1934        390     0           24        18            100
    ## 50        1879        346     0           24        18            100
    ## 51        1808         83     3           27        18            104
    ## 52        2263         55     3           27        18            104
    ## 53        2936        188     3           27        18            104
    ## 54        2345        414     3           27        18            104
    ## 55        2208        431     3           27        18            104
    ## 56        2271        189     3           27        18            104
    ## 57        1136        101     0           21        15            106
    ## 58         703        117     0           21        15            106
    ## 59         878        193     0           21        15            106
    ## 60        1387        354     0           21        15            106
    ## 61        1300        513     0           21        15            106
    ## 62        1138        666     0           21        15            106
    ## 63        1262         62     0           30        16            104
    ## 64        1551        203     0           30        16            104
    ## 65        1463        733     0           30        16            104
    ## 66         632        243     0           30        16            104
    ## 67        1467        916     0           30        16            104
    ## 68        1498        640     0           30        16            104
    ## 69        2139        249     0           25        17            102
    ## 70        2260        393     0           25        17            102
    ## 71        2571        630     0           25        17            102
    ## 72        2361        538     0           25        17            102
    ## 73        2668        932     0           25        17            102
    ## 74        1959        864     0           25        17            102
    ## 75        1625        105     0           23        17             92
    ## 76        1982        297     0           23        17             92
    ## 77        2328        368     0           23        17             92
    ## 78        1984        557     0           23        17             92
    ## 79        1864        755     0           23        17             92
    ## 80        1802        793     0           23        17             92
    ## 81        1643         99     0           24        15             86
    ## 82        1819        159     0           24        15             86
    ## 83        1743        252     0           24        15             86
    ## 84        1517        495     0           24        15             86
    ## 85        1426        160     0           24        15             86
    ## 86        1395        410     0           24        15             86
    ## 87        2585        254     0           29        26            102
    ## 88        2303        733     0           29        26            102
    ## 89        2675        825     0           29        26            102
    ## 90        3077        793     0           29        26            102
    ## 91        2762        583     0           29        26            102
    ## 92        2264        710     0           29        26            102
    ## 93        1418         37    14           25        11             74
    ## 94         999         82    14           25        11             74
    ## 95        1145         58    14           25        11             74
    ## 96        2088          9    14           25        11             74
    ## 97        1454         37    14           25        11             74
    ## 98        2555        406     0           29        33            106
    ## 99        2687        738     0           29        33            106
    ## 100       2479        940     0           29        33            106
    ## 101       2965       1092     0           29        33            106
    ## 102       2881        769     0           29        33            106
    ## 103       2576       1079     0           29        33            106
    ## 104       1019        227    11           28        20             88
    ## 105       1316        335    11           28        20             88
    ## 106       1159        330    11           28        20             88
    ## 107        855        455    11           28        20             88
    ## 108       1142        197    11           28        20             88
    ## 109        995        274    11           28        20             88
    ## 110       1787        214     9           26        14             86
    ## 111       1822        362     9           26        14             86
    ## 112       2092        503     9           26        14             86
    ## 113       2485        717     9           26        14             86
    ## 114       2504        792     9           26        14             86
    ## 115       2389        590     9           26        14             86
    ## 116       2826        122    21           22         8             72
    ## 117       2524         78    21           22         8             72
    ## 118       2764         96    21           22         8             72
    ## 119       2283         93    21           22         8             72
    ## 120       1963          5    21           22         8             72
    ## 121       2077          2    21           22         8             72
    ## 122       1698        118     0           21        19            106
    ## 123       1843        414     0           21        19            106
    ## 124       1811        305     0           21        19            106
    ## 125       2256        592     0           21        19            106
    ## 126       1965        686     0           21        19            106
    ## 127       2044        847     0           21        19            106
    ## 128       2138        103    14           25        19             65
    ## 129       1990        157    14           25        19             65
    ## 130       1332        148    14           25        19             65
    ## 131       1054        162    14           25        19             65
    ## 132       1518        197    14           25        19             65
    ## 133       1748        236    14           25        19             65
    ## 134        654         26    20           13        11             67
    ## 135       1995         32    20           13        11             67
    ## 136       1393         40    20           13        11             67
    ## 137       1482        143    20           13        11             67
    ## 138       1278         34    20           13        11             67
    ## 139       1349        143    20           13        11             67
    ## 140       2740        433     0           29        22             90
    ## 141       2562        623     0           29        22             90
    ## 142       2589        826     0           29        22             90
    ## 143       2978       1225     0           29        22             90
    ## 144       2940       1145     0           29        22             90
    ## 145       2895       1010     0           29        22             90
    ## 146       1450        483    11           32        33            100
    ## 147       1668       1293    11           32        33            100
    ## 148       2518        714    11           32        33            100
    ## 149       2410       1154    11           32        33            100
    ## 150       3076       1249    11           32        33            100
    ## 151       1361         95     0           26        18             96
    ## 152       1663        236     0           26        18             96
    ## 153       1706        299     0           26        18             96
    ## 154       1754        384     0           26        18             96
    ## 155       1385        318     0           26        18             96
    ## 156       1788        530     0           26        18             96
    ## 157        584        111     0           20        16            102
    ## 158       1139        258     0           20        16            102
    ## 159       1006        403     0           20        16            102
    ## 160       1470        502     0           20        16            102
    ## 161       1098        622     0           20        16            102
    ## 162       1889        521     0           20        16            102
    ## 163       1643        117    17           26        14             72
    ## 164       2448        177    17           26        14             72
    ## 165       2417         39    17           26        14             72
    ## 166       2214         39    17           26        14             72
    ## 167       2560        179    17           26        14             72
    ## 168       2391        204    17           26        14             72
    ## 169       2054         21    12           31        13             70
    ## 170       2211        116    12           31        13             70
    ## 171       2278        151    12           31        13             70
    ## 172       1863         79    12           31        13             70
    ## 173       2360        193    12           31        13             70
    ## 174       2219        195    12           31        13             70
    ## 175       1870        319     0           27        22            100
    ## 176       2219        542     0           27        22            100
    ## 177       1872       1246     0           27        22            100
    ## 178       2233        750     0           27        22            100
    ## 179       2729        690     0           27        22            100
    ## 180       2363        418     0           27        22            100
    ## 181        927          3    21           21         9             69
    ## 182       1856          7    21           21         9             69
    ## 183       1590          1    21           21         9             69
    ## 184       2054         14    21           21         9             69
    ## 185       1388         33    21           21         9             69
    ## 186       2202        100    21           21         9             69
    ## 187        788         35    14           25        11             76
    ## 188       1393         10    14           25        11             76
    ## 189       1504         26    14           25        11             76
    ## 190       1726        142    14           25        11             76
    ## 191       1917         42    14           25        11             76
    ## 192       1852         79    14           25        11             76
    ## 193        893        180    10           27        22             82
    ## 194        748        368    10           27        22             82
    ## 195       1350        365    10           27        22             82
    ## 196       1224        438    10           27        22             82
    ## 197       1223        537    10           27        22             82
    ## 198       1540        605    10           27        22             82
    ## 199       1344        260     1           23        21            102
    ## 200       1784        879     1           23        21            102
    ## 201       2328        702     1           23        21            102
    ## 202       2145         74     1           23        21            102
    ## 203       2279        736     1           23        21            102
    ## 204       2438        611     1           23        21            102
    ## 205       1631         16     3           19        13             98
    ## 206       1843        107     3           19        13             98
    ## 207       1504        218     3           19        13             98
    ## 208       1715        477     3           19        13             98
    ## 209       1979        459     3           19        13             98
    ## 210       1978        568     3           19        13             98
    ## 211       1988        337     7           33        26             70
    ## 212       2340        433     7           33        26             70
    ## 213       1570        694     7           33        26             70
    ## 214       2252        510     7           33        26             70
    ## 215       2314        815     7           33        26             70
    ## 216       2510        897     7           33        26             70
    ## 217       2035        398     1           29        28            104
    ## 218       2290        439     1           29        28            104
    ## 219       2146        825     1           29        28            104
    ## 220       2482        576     1           29        28            104
    ## 221       1999        800     1           29        28            104
    ## 222       1537        109    11           26        19             88
    ## 223       1481        126    11           26        19             88
    ## 224       1709        232    11           26        19             88
    ## 225       1675        135    11           26        19             88
    ## 226       1951        340    11           26        19             88
    ## 227       1987        110    11           26        19             88
    ## 228       1827         58    13           27        13             86
    ## 229       1882        148    13           27        13             86
    ## 230       2148        404    13           27        13             86
    ## 231       2377        493    13           27        13             86
    ## 232       2508        571    13           27        13             86
    ## 233       1547        235     0           25        17            102
    ## 234       1847        264     0           25        17            102
    ## 235       1426        352     0           25        17            102
    ## 236       1689        434     0           25        17            102
    ## 237       1936        651     0           25        17            102
    ## 238       1741        702     0           25        17            102
    ## 239       2334        176     0           24        19             96
    ## 240       2201        423     0           24        19             96
    ## 241       2124        604     0           24        19             96
    ## 242       1726        820     0           24        19             96
    ## 243       1600        875     0           24        19             96
    ## 244       1764        719     0           24        19             96
    ## 245       1591        450    14           42        27             65
    ## 246       2031        536    14           42        27             65
    ## 247       2141       1023    14           42        27             65
    ## 248       2158        822    14           42        27             65
    ## 249       2326       1054    14           42        27             65
    ## 250       2227        921    14           42        27             65
    ## 251       1417         68     3           30        20             94
    ## 252       1515        120     3           30        20             94
    ## 253       1740        528     3           30        20             94
    ## 254       1556        378     3           30        20             94
    ## 255       1207        433     3           30        20             94
    ## 256       1188         91     5           24        20            113
    ## 257       1375        165     5           24        20            113
    ## 258       1912        354     5           24        20            113
    ## 259       1909        433     5           24        20            113
    ## 260       1791        502     5           24        20            113
    ## 261       2144         63    20           21         9             75
    ## 262       1973         38    20           21         9             75
    ## 263       1018          3    20           21         9             75
    ## 264        861         55    20           21         9             75
    ## 265       1609         32    20           21         9             75
    ## 266       1918        137    20           21         9             75
    ## 267       1347        212     0           27        20            102
    ## 268       1653        571     0           27        20            102
    ## 269       1568        486     0           27        20            102
    ## 270       1559        973     0           27        20            102
    ## 271       1224        304     0           27        20            102
    ## 272       1371         61     0           27        20            102
    ## 273       2147         40    17           28        10             82
    ## 274       1344          4    17           28        10             82
    ## 275       1482         45    17           28        10             82
    ## 276       1871        255    17           28        10             82
    ## 277       1565        306    17           28        10             82
    ## 278       1452        286     0           27        27            108
    ## 279       1867        356     0           27        27            108
    ## 280       1609        671     0           27        27            108
    ## 281       1452        684     0           27        27            108
    ## 282       1525        586     0           27        27            108
    ## 283       1701        646     0           27        27            108
    ## 284       1215         43    19           17        10             64
    ## 285       1851        147    19           17        10             64
    ## 286       1423         77    19           17        10             64
    ## 287       1145         70    19           17        10             64
    ## 288       1447          0    19           17        10             64
    ## 289       1396          8    19           17        10             64
    ## 290       1503         38     1           22        14            102
    ## 291       1483         97     1           22        14            102
    ## 292       1562        122     1           22        14            102
    ## 293       1829        348     1           22        14            102
    ## 294       1986        238     1           22        14            102
    ## 295       1866        395     1           22        14            102
    ## 296       2267        319     0           29        22             94
    ## 297       1845        298     0           29        22             94
    ## 298       2142        897     0           29        22             94
    ## 299       2585        642     0           29        22             94
    ## 300       2772        723     0           29        22             94
    ## 301       2887        436     0           29        22             94
    ## 302       1170        473     0           30        30             98
    ## 303        686        449     0           30        30             98
    ## 304       1020        660     0           30        30             98
    ## 305        798        978     0           30        30             98
    ## 306       1067        814     0           30        30             98
    ## 307       1024        358     0           30        30             98
    ## 308       1118        109     1           24        22            115
    ## 309       1905        287     1           24        22            115
    ## 310       1418        261     1           24        22            115
    ## 311       1699        694     1           24        22            115
    ## 312       1977        725     1           24        22            115
    ## 313       2347        517     1           24        22            115
    ## 314       1144        154     0           26        17             96
    ## 315       1058        244     0           26        17             96
    ## 316       1470        672     0           26        17             96
    ## 317       1524       1051     0           26        17             96
    ## 318       1731       1294     0           26        17             96
    ## 319        955         47    14           27        11             77
    ## 320       1417         19    14           27        11             77
    ## 321       1393         14    14           27        11             77
    ## 322       1390         36    14           27        11             77
    ## 323       2613          0    15           28        10             66
    ## 324       2274         44    15           28        10             66
    ## 325       2717         41    15           28        10             66
    ## 326       2196          1    15           28        10             66
    ## 327       2070         30    15           28        10             66
    ## 328       2450         64    15           28        10             66
    ## 329       2084        233    15           30        24             88
    ## 330       1511        260    15           30        24             88
    ## 331       1891        393    15           30        24             88
    ## 332       2109        574    15           30        24             88
    ## 333       2276        531    15           30        24             88
    ## 334       2221        618    15           30        24             88
    ## 335       1805        244     4           29        22            108
    ## 336       1959        401     4           29        22            108
    ## 337       1610        424     4           29        22            108
    ## 338       1974        637     4           29        22            108
    ## 339       1916        636     4           29        22            108
    ## 340       1460        659     4           29        22            108
    ## 341       2587        469    13           30        30             87
    ## 342       2534        670    13           30        30             87
    ## 343       2841        698    13           30        30             87
    ## 344       3163        693    13           30        30             87
    ## 345       3090        357    13           30        30             87
    ## 346       3072        713    13           30        30             87
    ## 347       1579        166    15           27        16             79
    ## 348       1205        356    15           27        16             79
    ## 349       1618        254    15           27        16             79
    ## 350       1644        334    15           27        16             79
    ## 351       1527        511    15           27        16             79
    ## 352       1481        210    15           27        16             79
    ##     mean(tokens_CHI)
    ## 1           389.8011
    ## 2           389.8011
    ## 3           389.8011
    ## 4           389.8011
    ## 5           389.8011
    ## 6           389.8011
    ## 7           389.8011
    ## 8           389.8011
    ## 9           389.8011
    ## 10          389.8011
    ## 11          389.8011
    ## 12          389.8011
    ## 13          389.8011
    ## 14          389.8011
    ## 15          389.8011
    ## 16          389.8011
    ## 17          389.8011
    ## 18          389.8011
    ## 19          389.8011
    ## 20          389.8011
    ## 21          389.8011
    ## 22          389.8011
    ## 23          389.8011
    ## 24          389.8011
    ## 25          389.8011
    ## 26          389.8011
    ## 27          389.8011
    ## 28          389.8011
    ## 29          389.8011
    ## 30          389.8011
    ## 31          389.8011
    ## 32          389.8011
    ## 33          389.8011
    ## 34          389.8011
    ## 35          389.8011
    ## 36          389.8011
    ## 37          389.8011
    ## 38          389.8011
    ## 39          389.8011
    ## 40          389.8011
    ## 41          389.8011
    ## 42          389.8011
    ## 43          389.8011
    ## 44          389.8011
    ## 45          389.8011
    ## 46          389.8011
    ## 47          389.8011
    ## 48          389.8011
    ## 49          389.8011
    ## 50          389.8011
    ## 51          389.8011
    ## 52          389.8011
    ## 53          389.8011
    ## 54          389.8011
    ## 55          389.8011
    ## 56          389.8011
    ## 57          389.8011
    ## 58          389.8011
    ## 59          389.8011
    ## 60          389.8011
    ## 61          389.8011
    ## 62          389.8011
    ## 63          389.8011
    ## 64          389.8011
    ## 65          389.8011
    ## 66          389.8011
    ## 67          389.8011
    ## 68          389.8011
    ## 69          389.8011
    ## 70          389.8011
    ## 71          389.8011
    ## 72          389.8011
    ## 73          389.8011
    ## 74          389.8011
    ## 75          389.8011
    ## 76          389.8011
    ## 77          389.8011
    ## 78          389.8011
    ## 79          389.8011
    ## 80          389.8011
    ## 81          389.8011
    ## 82          389.8011
    ## 83          389.8011
    ## 84          389.8011
    ## 85          389.8011
    ## 86          389.8011
    ## 87          389.8011
    ## 88          389.8011
    ## 89          389.8011
    ## 90          389.8011
    ## 91          389.8011
    ## 92          389.8011
    ## 93          389.8011
    ## 94          389.8011
    ## 95          389.8011
    ## 96          389.8011
    ## 97          389.8011
    ## 98          389.8011
    ## 99          389.8011
    ## 100         389.8011
    ## 101         389.8011
    ## 102         389.8011
    ## 103         389.8011
    ## 104         389.8011
    ## 105         389.8011
    ## 106         389.8011
    ## 107         389.8011
    ## 108         389.8011
    ## 109         389.8011
    ## 110         389.8011
    ## 111         389.8011
    ## 112         389.8011
    ## 113         389.8011
    ## 114         389.8011
    ## 115         389.8011
    ## 116         389.8011
    ## 117         389.8011
    ## 118         389.8011
    ## 119         389.8011
    ## 120         389.8011
    ## 121         389.8011
    ## 122         389.8011
    ## 123         389.8011
    ## 124         389.8011
    ## 125         389.8011
    ## 126         389.8011
    ## 127         389.8011
    ## 128         389.8011
    ## 129         389.8011
    ## 130         389.8011
    ## 131         389.8011
    ## 132         389.8011
    ## 133         389.8011
    ## 134         389.8011
    ## 135         389.8011
    ## 136         389.8011
    ## 137         389.8011
    ## 138         389.8011
    ## 139         389.8011
    ## 140         389.8011
    ## 141         389.8011
    ## 142         389.8011
    ## 143         389.8011
    ## 144         389.8011
    ## 145         389.8011
    ## 146         389.8011
    ## 147         389.8011
    ## 148         389.8011
    ## 149         389.8011
    ## 150         389.8011
    ## 151         389.8011
    ## 152         389.8011
    ## 153         389.8011
    ## 154         389.8011
    ## 155         389.8011
    ## 156         389.8011
    ## 157         389.8011
    ## 158         389.8011
    ## 159         389.8011
    ## 160         389.8011
    ## 161         389.8011
    ## 162         389.8011
    ## 163         389.8011
    ## 164         389.8011
    ## 165         389.8011
    ## 166         389.8011
    ## 167         389.8011
    ## 168         389.8011
    ## 169         389.8011
    ## 170         389.8011
    ## 171         389.8011
    ## 172         389.8011
    ## 173         389.8011
    ## 174         389.8011
    ## 175         389.8011
    ## 176         389.8011
    ## 177         389.8011
    ## 178         389.8011
    ## 179         389.8011
    ## 180         389.8011
    ## 181         389.8011
    ## 182         389.8011
    ## 183         389.8011
    ## 184         389.8011
    ## 185         389.8011
    ## 186         389.8011
    ## 187         389.8011
    ## 188         389.8011
    ## 189         389.8011
    ## 190         389.8011
    ## 191         389.8011
    ## 192         389.8011
    ## 193         389.8011
    ## 194         389.8011
    ## 195         389.8011
    ## 196         389.8011
    ## 197         389.8011
    ## 198         389.8011
    ## 199         389.8011
    ## 200         389.8011
    ## 201         389.8011
    ## 202         389.8011
    ## 203         389.8011
    ## 204         389.8011
    ## 205         389.8011
    ## 206         389.8011
    ## 207         389.8011
    ## 208         389.8011
    ## 209         389.8011
    ## 210         389.8011
    ## 211         389.8011
    ## 212         389.8011
    ## 213         389.8011
    ## 214         389.8011
    ## 215         389.8011
    ## 216         389.8011
    ## 217         389.8011
    ## 218         389.8011
    ## 219         389.8011
    ## 220         389.8011
    ## 221         389.8011
    ## 222         389.8011
    ## 223         389.8011
    ## 224         389.8011
    ## 225         389.8011
    ## 226         389.8011
    ## 227         389.8011
    ## 228         389.8011
    ## 229         389.8011
    ## 230         389.8011
    ## 231         389.8011
    ## 232         389.8011
    ## 233         389.8011
    ## 234         389.8011
    ## 235         389.8011
    ## 236         389.8011
    ## 237         389.8011
    ## 238         389.8011
    ## 239         389.8011
    ## 240         389.8011
    ## 241         389.8011
    ## 242         389.8011
    ## 243         389.8011
    ## 244         389.8011
    ## 245         389.8011
    ## 246         389.8011
    ## 247         389.8011
    ## 248         389.8011
    ## 249         389.8011
    ## 250         389.8011
    ## 251         389.8011
    ## 252         389.8011
    ## 253         389.8011
    ## 254         389.8011
    ## 255         389.8011
    ## 256         389.8011
    ## 257         389.8011
    ## 258         389.8011
    ## 259         389.8011
    ## 260         389.8011
    ## 261         389.8011
    ## 262         389.8011
    ## 263         389.8011
    ## 264         389.8011
    ## 265         389.8011
    ## 266         389.8011
    ## 267         389.8011
    ## 268         389.8011
    ## 269         389.8011
    ## 270         389.8011
    ## 271         389.8011
    ## 272         389.8011
    ## 273         389.8011
    ## 274         389.8011
    ## 275         389.8011
    ## 276         389.8011
    ## 277         389.8011
    ## 278         389.8011
    ## 279         389.8011
    ## 280         389.8011
    ## 281         389.8011
    ## 282         389.8011
    ## 283         389.8011
    ## 284         389.8011
    ## 285         389.8011
    ## 286         389.8011
    ## 287         389.8011
    ## 288         389.8011
    ## 289         389.8011
    ## 290         389.8011
    ## 291         389.8011
    ## 292         389.8011
    ## 293         389.8011
    ## 294         389.8011
    ## 295         389.8011
    ## 296         389.8011
    ## 297         389.8011
    ## 298         389.8011
    ## 299         389.8011
    ## 300         389.8011
    ## 301         389.8011
    ## 302         389.8011
    ## 303         389.8011
    ## 304         389.8011
    ## 305         389.8011
    ## 306         389.8011
    ## 307         389.8011
    ## 308         389.8011
    ## 309         389.8011
    ## 310         389.8011
    ## 311         389.8011
    ## 312         389.8011
    ## 313         389.8011
    ## 314         389.8011
    ## 315         389.8011
    ## 316         389.8011
    ## 317         389.8011
    ## 318         389.8011
    ## 319         389.8011
    ## 320         389.8011
    ## 321         389.8011
    ## 322         389.8011
    ## 323         389.8011
    ## 324         389.8011
    ## 325         389.8011
    ## 326         389.8011
    ## 327         389.8011
    ## 328         389.8011
    ## 329         389.8011
    ## 330         389.8011
    ## 331         389.8011
    ## 332         389.8011
    ## 333         389.8011
    ## 334         389.8011
    ## 335         389.8011
    ## 336         389.8011
    ## 337         389.8011
    ## 338         389.8011
    ## 339         389.8011
    ## 340         389.8011
    ## 341         389.8011
    ## 342         389.8011
    ## 343         389.8011
    ## 344         389.8011
    ## 345         389.8011
    ## 346         389.8011
    ## 347         389.8011
    ## 348         389.8011
    ## 349         389.8011
    ## 350         389.8011
    ## 351         389.8011
    ## 352         389.8011

``` r
df_merged <- group_by(df_merged,SUBJ) %>% mutate(mean(tokens_CHI+tokens_MOT))
df_merged <- mutate(df_merged,mean(tokens_CHI))

#other solution
mutate1 <- df_merged %>% mutate(meanallvisit = mean(tokens_CHI))

#2
mutate2 <- df_merged %>% group_by(SUBJ) %>% mutate(meanallvisit = mean(tokens_CHI))

#3
#The development of the children is probably important, i.e. how they change, and not just how many words they can speak on average. That would only be a very simplified measure. 
```
