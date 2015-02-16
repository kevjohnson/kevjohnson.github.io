---
layout: post
title: "Percentage of People with No Health Insurance"
description: "Maps of the percentage of people with no health insurance for the top 15 metro areas in the US."
---

As a followup to my [previous post on making maps with R]({{ site.url }}/making-maps-in-r-part-2/), I decided to go ahead and make a bunch of maps of that data for the top 15 metro areas in the US.  I [posted it on Reddit](http://www.reddit.com/r/dataisbeautiful/comments/201tob/percentage_of_people_with_no_form_of_health/) where you can find a good discussion on my methods and data source.

The following maps represent estimates for the percentage of people without any form of health insurance (public or private).

<!--break-->

###Atlanta
![]({{ site.url }}/images/Atlanta.png)

###Boston
![]({{ site.url }}/images/Boston.png)

###Chicago
![]({{ site.url }}/images/Chicago.png)

###Dallas
![]({{ site.url }}/images/Dallas.png)

###Detroit
![]({{ site.url }}/images/Detroit.png)

###Houston
![]({{ site.url }}/images/Houston.png)

###Los Angeles
![]({{ site.url }}/images/LosAngeles.png)

###Miami (working on fixing this first)
![]({{ site.url }}/images/Miami.png)

###Minneapolis/St. Paul
![]({{ site.url }}/images/Minneapolis.png)

###New York City
![]({{ site.url }}/images/NewYorkCity.png)

###Philadelphia
![]({{ site.url }}/images/Philadelphia.png)

###Phoenix
![]({{ site.url }}/images/Phoenix.png)

###Riverside/San Bernadino
![]({{ site.url }}/images/Riverside.png)

###San Francisco
![]({{ site.url }}/images/SanFrancisco.png)

###Seattle
![]({{ site.url }}/images/Seattle.png)

###St. Louis
![]({{ site.url }}/images/StLouis.png)

###Washington D.C.
![]({{ site.url }}/images/WashingtonDC.png)

###FAQ
####Where did the data come from?
The data came from the [2012 American Community Survey 5-year estimates](http://factfinder2.census.gov/faces/tableservices/jsf/pages/productview.xhtml?pid=ACS_12_5YR_S2701&prodType=table).  It represents the percentage of the civilian, non-institutionalized population that does not have any form of health insurance (public or private).

####People who live in lakes and oceans seem to all have health insurance...
Yeah... the interpolation I used produces funny results when there is no data present, so you just have to ignore anything over a body of water or a national park.  One day I'll take the time to fix it.

####Why not just make a choropleth map?
I didn't like the jagged edges of the census tract boundaries, so I smoothed out the borders a bit.  I think it makes it easier on the eyes without losing any important information.  Look at my previous post linked at the top to see a comparison, it's roughly the same.

####Where's my city, and what's Riverside doing there?
I used [this list](http://en.wikipedia.org/wiki/List_of_Metropolitan_Statistical_Areas#United_States) to determine the cities, plus a few extras requested by people on Reddit.