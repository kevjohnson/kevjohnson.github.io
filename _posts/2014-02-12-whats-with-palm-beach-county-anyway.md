---
layout: post
title: "What's With Palm Beach County Anyway"
description: "Regression analysis on Palm Beach County election results from the 2000 US Presidential Election."
---

###Introduction

Anyone who was alive in 2000 remembers the debacle that was the [2000 US Presidential Election](http://en.wikipedia.org/wiki/United_States_presidential_election,_2000).  The election pitted Republican George W. Bush against Democrat Al Gore.  In the end, Bush became President of the United States by a grand total of 537 votes after the Supreme Court decided to halt a recount of 70,000 ballots rejected by machines.

<!--break-->

Much of the controversy surrounded Palm Beach County with accusations of hanging chads, dimpled chads, and (my personal favorite) pregnant chads.  I would like to focus on the complaints against the so-called [butterfly ballot](http://upload.wikimedia.org/wikipedia/commons/6/66/Butterfly_large.jpg).  Many Democrats claimed that people mistakenly voted for Buchanan instead of voting for Gore

###Analysis

Does the data back these claims up?  To find out, I downloaded the Florida 2000 election results by county.  First, let's take a look at a map of Bush's votes and Gore's votes.

![](/images/bushgoremap.png)

Nothing seems out of the ordinary so far.  The vast majority of the votes are concentrated around the population centers, specifically Miami.  If I was actually trying to make sense of this I would probably use a log scale, but that would make my next map noticeably less fun.

Most people don't (and shouldn't) remember that Pat Buchanan also ran for President in 2000.  He began on the Republican ticket, but dropped out on October 25, 1999 to run as the Reform Party candidate.  Here's a map of Buchana's votes in the 2000 election. 

![](/images/buchananmap.png)

Want to take a wild guess which county is Palm Beach?  It's clear from this graph that Buchanan received way more votes in Palm Beach than one would expect based on his results elsewhere, but exactly how many "extra" votes did he receive?  Buchanan ran on the Reform Party ticket, but really Republicans were the ones voting for him.  The number of votes Bush received could be used as a proxy for the number of Republicans in a particular county, and thus the number of votes Buchanan could expect to receive.

![](/images/bushvsbuch.png)

There appears to be a significant linear relationship between votes for Bush and votes for Buchanan.  The correlation between the two is 0.63 (0.87 if you remove Palm Beach County).  Here's the same data with Palm Beach County removed and a log transformation on both variables.  Why a log transformation?  Because log is a magical function that will solve all of your problems (also the correlation goes to 0.93).

![](/images/bushvsbuchlog.png)

Here's the output of a simple linear regression on the data:

{% highlight r %}
Call:
lm(formula = log(buch) ~ log(bush))

Residuals:
     Min       1Q   Median       3Q      Max 
-0.97136 -0.22384  0.02279  0.26959  1.00652 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) -2.31657    0.35470  -6.531 1.23e-08 ***
log(bush)    0.72960    0.03599  20.271  < 2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.4203 on 64 degrees of freedom
Multiple R-squared:  0.8652,    Adjusted R-squared:  0.8631 
F-statistic: 410.9 on 1 and 64 DF,  p-value: < 2.2e-16
{% endhighlight %}

###Conclusion

So what does all of this mean?  Well, Buchanan and Bush received 3,407 and 152,846 votes in Palm Beach County in 2000, respectively.  Based on the number of votes Bush received, the regression equation above predicts that Buchanan would have received 598 votes.  This means that Buchanan received 2,809 more votes than he should have if Palm Beach County had voted similar to every other county in Florida.  Recall that Bush only won Florida by 537 votes and you'll see why this was such a big deal back in 2000.

Does this prove that Al Gore should have won the 2000 Presidential Election?  Absolutely not, but it does show that the controversy surrounding Palm Beach County was justified by the data.  The complaints about the butterfly ballot weren't just grasping at straws, there is data to back them up.