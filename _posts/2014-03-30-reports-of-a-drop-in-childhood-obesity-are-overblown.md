---
layout: post
title: "Reports of a Drop in Childhood Obesity are Overblown"
description: "FiveThirtyEight recently posted an article with that title.  They're not wrong, but I have a few issues with their analysis."
---

I ripped this headline from a [recent post at fivethirtyeight.com](http://fivethirtyeight.com/features/reports-of-a-drop-in-childhood-obesity-are-overblown) where they examine a flood of recent headlines about the recent paper published by scientists at the CDC.  The mainstream news mostly talked about the "significant" drop in obesity prevalence among kids age 2-5.  Emily Oster makes the case that these headlines are greatly overblown, and she is absolutely correct about that.  I'm certain the authors of the [original paper](https://jama.jamanetwork.com/article.aspx?articleid=1832542) would agree with her.

However, I do have a few issues with the case made by Ms. Oster.

<!--break-->

>...since the authors are slicing the data and doing multiple tests — they consider at least six different age groups — the statistics need to be adjusted to account for that.

This is where I have a small problem.  There is no real consensus in the statistical community about what exactly constitutes a "family of hypotheses", and saying that the statistics "need to be adjusted" is more of an opinion than a fact.

Let's back up a bit.  When you perform a bunch of hypothesis tests simulatenously on a set of data, the probability that one of your tests comes back significant just due to chance is remarkably high.  If you have 15 hypotheses to test, and your significance level is the standard 0.05, then what is the probability that one of your tests will be significant just by chance?

$$\begin{align}
\Pr(\text{at least one significant result}) &= 1-\Pr(\text{no significant results}) \\
&= 1-(1-0.05)^{15} \\
&= 0.54
\end{align}$$

More often than not, at least one out of 15 simultaneous hypothesis tests will come back significant even if none of them are actually significant.  The Bonferroni Correction that Ms. Oster mentioned in her post attempts to reduce this probability by adjusting the significance level ($\alpha$) to be lower.

The problem with this is that it is not agreed upon what exactly constitutes a family of hypothesis tests.  At the moment it is a subjective process.  In a paper like this, adjusting for the number of tests you are performing would mean that a significant result would almost never appear.  

This is all related to the age old balance between Type I and Type II error.  If your only goal is to minimize false positives, then the Bonferroni Correction makes perfect sense (but then again, so does setting $\alpha=0.000001$).  Lowering your significance level will reduce your false positives, but it will also increase your false negatives.  The authors of the paper decided against these adjustments because they do not want to significantly increase their false negatives.

Here's the most relevant quote from the paper:

>In subgroup analyses, the prevalence of obesity among children aged 2 to 5 years decreased from 14% in 2003-2004 to just over 8% in 2011-2012, and the prevalence increased in women aged 60 years and older, from 31.5% to more than 38%. **Because these age subgroup analyses and tests for significance did not adjust for multiple comparisons, these results should be interpreted with caution**.

The final paragraph of the paper:

>In the current analysis, trend tests were conducted on different age groups.  When multiple statistical tests are undertaken, by chance some tests will be statistically significant (eg, 5% of the time using $\alpha$ of .05).  In some cases, adjustments are made to account for these multiple comparisons, and a P values lower than .05 is used to determine statistical significance.  In the current analysis, adjustments were not made for multiple comparisons, but the P value is presented.

My main issue is that, intentional or not, Ms. Oster seemed to imply that the authors of this paper were not aware of the fact that they needed to adjust their significance tests.  On the contrary, they made a conscious decision not to adjust their statistics, and personally I agree with that decision.  Whether or not you agree with that is a matter of personal opinion, not objective fact (at least for now).

With that said, when you look at the paper as a whole you cannot say that there is any significant change in obesity levels in the US.  Obesity rates have been plateauing for a while now, so in the end this paper is a non-story.  We're not getting any worse, but we're also not getting any better.  One significant decrease out of dozens of tests is not something we should be celebrating.