---
layout: post
title: "Children Without Siblings are 62% More Likely to be Unhappy, Sad, or Depressed"
---

###Data
Starting in 2003, the CDC began conducting the [National Survey of Children's Health (NSCH)](http://www.cdc.gov/nchs/slaits/nsch.htm).  In 2011 (the most recent year available), they completed a total of 95,677 child-level interviews.  The survey is a cross-sectional telephone survey of households with at least one child age 0-17.  They stratify the samples by state and type (landline vs. cell phone) and cluster them by household (multiple children in the same house are clustered together).  In English, they do a bunch of fancy statistics that allow us to use this dataset as a nationally representative sample.

<!--break-->

A complete list of available variables can be found [here](ftp://ftp.cdc.gov/pub/Health_Statistics/NCHS/slaits/nsch_2011_2012/04_List_of_variables_and_frequency_counts/create_formatted_frequencies.pdf).  There are two variables related to the position of a child relative to his/her siblings (`AGEPOS4` and `TOTKIDS4`).  I created a new variable based on these two variables that indicates whether a child is an only child, youngest, middle, or oldest.  I used the [survey package](http://cran.r-project.org/web/packages/survey/index.html) provided by Dr. Thomas Lumley to analyze the data in R.

###Methods
The effect of confounding variables cannot be ignored in this analysis.  For example, let's say I find that children without siblings are 50% more likely to be covered by health insurance.  What if low income families tend to have more children than high income families?  That would mean that part of that 50% difference could be explained by the the income of the child's family, rather than whether or not they have siblings.

I use a logistic regression to control for these confounding variables:

* Age (in years)
* Sex
* Race (black, white, and other)
* Poverty Ratio (the ratio of a family's income to the poverty line for that family)
* Parents' Education (less than high school, high school, and more than high school)

In short, when I say:

>Middle children are 189% more likely to eat ice cream.

what I actually mean is:

>Holding age, sex, race, poverty ratio, and parents' education constant, the odds of a middle child eating ice cream divided by the odds of an oldest, youngest, or only child eating ice cream is 2.89.

###Results

####Health

Only children are 31% less likely to have health insurance, while oldest children are 34% more likely to have health insurance.  Only children are 32% more likely to be clinically obese.  Only children are 17% more likely to have asthma, while oldest children are 14% less likely to have asthma.  Only children are 50% less likely to have gone to the dentist in the last year. Oldest, middle, and youngest children are 25%, 68%, and 13% more likely to have gone to the dentist in the last year, respectively.  Only children are 12% more likely to have missed at least one day of school in the last year due to illness or injury.  Oldest children are 36% more likely to have been breast fed, while youngest children are 16% less likely to have been breast fed.

####Behavior

Oldest children are 21% less likely to be affectionate and tender with their parents, while youngest children 17% more likely to do the same.  Only children are 29% more likely to show interest and curiosity in learning new things, while middle children are 32% less likely to do the same.  Middle children are 39% more likely to argue with their parents too much, while only children are 31% less likely to do the same.  Oldest children are 30% more likely to bully or be cruel/mean to others, while youngest children are 18% less likely to do the same.  Only children are 62% more likely to be unhappy, sad, or depressed, while middle and youngest children are 37% and 15% less likely to by unhappy, sad, or depressed, respectively.  Oldest children are 23% more likely to always do their homework, while middle and only children are 17% and 9% less likely to do the same.

####Activities

Only children and oldest children are 18% and 45% more likely to have their parents read to them every day, respectively.  Youngest children are 24% less likely to have their parents read to them every day.  Only children are 13% less likely to have played with other children on 4 or more days in the last week, while middle children are 39% more likely to do the same.  Youngest children were 20% less likely to have been on a sports team in the last year, while oldest and middle children are 9% and 16% more likely to do the same, respectively.  Only children are 10% less likely to have exercised on at least 5 days in the last week.  Oldest children are 12% more likely to spend at least 1 hour per day reading for pleasure, while middle children are 15% less likely to do the same.  Only children and youngest children are 29% and 12% more likely to spend at least 2 hours per day in front of a TV, respectively. Oldest children are 21% less likely to do the same.  