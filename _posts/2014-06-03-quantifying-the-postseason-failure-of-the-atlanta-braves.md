---
layout: post
title: "Quantifying the Postseason Failure of the Atlanta Braves"
---

###Introduction

Every long time fan of Atlanta sports is well acquainted with playoff failures.  Last year, Forbes ranked Atlanta the [second most miserable sports city](http://www.forbes.com/pictures/eddf45lhjf/no-2-atlanta/) in the country behind Seattle.  The Seahawks won the Super Bowl earlier this year, so it's safe to say that we are firmly in the lead for the most miserable sports town.

The Atlanta Braves won an unprecedented 14 straight division titles from 1991 to 2005, but they only have one World Series title to show for it (in 1995).  The Braves have been knocked out in the first round of 8 of their last 9 playoff appearances.  Are they just not good enough?  Are they unlucky?  In this post I try to quantify exactly how bad the Braves are in the playoffs.

<!--break-->

###Methods

The overall goal is to find out what the Braves "should" have done and compare it to what actually happened.  The process involves 4 steps:

1. Compute rankings for every team and every year based on regular season performance.
2. Use rankings to come up with head-to-head win probabilities for every team.
3. Simulate the results of the playoffs for each year to find what "should" have happened
4. Compare the simulation results to the actual results

I obtained data on every MLB game between 1991 and 2013 from [retrosheet.org](http://www.retrosheet.org/gamelogs/index.html).

**WARNING: MATH AHEAD.  If you don't care about the technical details then skip to the Results section.**

####Step 1: Team Rankings

At first, I based my methods on work done by [Sokol et al.](http://www2.isye.gatech.edu/~jsokol/ncaa.pdf) for NCAA college basketball rankings.  They use home field advantage and margin of victory for every game in the regular season as inputs for a ranking model.  Unfortunately, this method does not translate well to baseball.  Home field advantage is relatively minor in the MLB, and margin of victory of a single game is a poor predictor of future success against the same team.

![]({{ site.url }}/images/1991-2009 Rematch Victories.png)

In the figure above, the x-axis represents the margin of victory for any given game (a negative margin is a loss), and the y-axis represents the fraction of future games against the same team that were won.  You can see a slight upward trend as the margin of victory increases, but not nearly enough to gain any useful information  for a model.  See page 7 of Sokol et al. for an example of what this graph looks like for college basketball.

From here, I decided to go back to the basics, specifically a paper by [Callaghan et al.](https://people.maths.ox.ac.uk/porterm/papers/bcsmonthly.pdf) titled *Random Walker Ranking for NCAA Division I-A Football*.  They define a set of "voting robots" (random walkers) who each select a team they think is best.  Each voter randomly selects a game from that team's schedule and decides whether to change allegiance to the winner or not with some probability.  This process is repeated infinitely many times, and the steady state number of votes for a given team is the rank of that team.

In short, this method is analagous to the "Team A beat Team B, so Team A is better than Team B" argument repeated infinitely many times across the entire season.

Sokol et al. point out that the underlying model here is a [Markov chain](http://en.wikipedia.org/wiki/Markov_chain) (MC).  The state transitions of the MC correspond to the behavior of the robot voters, and the current state of the voter corresponds to the team that voter believes is best.  

Let $$W_i$$ and $$L_i$$ be the number of games that team $$i$$ has won and lost, and $$w_{ij}$$ and $$l_{ij}$$ be the number of games that team $$i$$ has won and lost against team $$j$$.  Let $$N_i$$ be the total number of games played by team $$i$$, and let $$p$$ be the probability that a voter moves to the state corresponding to the game's winner.

We can express the transition probabilities of the MC in the following way:

$$
\begin{align}
t_{ij} &= \frac{1}{N_i}\left[w_{ij}(1-p)+l_{ij}p\right] \\
t_{ii} &= \frac{1}{N_i}\left[W_{i}p+L_{i}(1-p)\right]
\end{align}
$$

Letting $$T=[t_{ij}]$$, we can calculate the steady state probabilities of the MC using the following system of equations:

$$
\begin{align}
\pi T &= \pi \\
\sum_i \pi_i &= 1
\end{align}
$$

The steady state probability $$\pi_i$$ represents the long run fraction of time spent in state $i$, or the long run fraction of hypothetical votes that each team receives.  In other words, a higher steady state probability results in a higher ranking.  [Here's a bunch of pictures of the MC for each year](http://imgur.com/a/aoWQR).  These pictures aren't very useful, but they're fun to look at.

####Step 2: Head-to-Head Win Probabilities

One way to simulate the result of a game is to just pick the team with a higher ranking, but we can do better than that.  I calculated the difference between the steady state probabilities for every game from 1997-2013, and examined the relationship between those differences and the result of the game.

![]({{ site.url }}/images/1997-2013 Win Probability.png)

In the figure above you can see a very clear linear relationship between the difference in steady state probability from the ranking model and the probability of victory.  I used a non-parametric regression with a local constant estimator to fit the model, which basically means I used semi-fancy statistics for no real reason.  A simple linear regression would have likely done the trick in this situation.

I now have a model that can give me an estimated probability of victory based on the difference in steady state probability between the home and away team.  This takes into account home field advantage and more accurately models the uncertain nature of a singular game of baseball.

####Step 3/4: Simulate and Compare

These steps do not require as much explanation, but simulating a baseball postseason turned out to be far more annoying than I originally anticipated.  I built a full simulation of every postseason from 1997 to 2011, taking into account the rules for home field advantage throughout the playoffs.  I used the model from step 2 to come up with a probability of victory for each team in every game.  The simulation was repeated 10,000 times for every year, and the final output was the average result for each team in each year.  I then compared the simulated results for each year with the actual results to see which teams underperformed (or overperformed) according to my simulation.

###Results

[Here is an album of the yearly results of the simulation compared to actual results.](http://imgur.com/a/pPwoj)

A value of 2 means they lost in the first round (LDS), 3 means they lost in the second round (LCS), 4 means the lost in the third round (World Series), and 5 means they won the World Series.  The abbreviations for the teams came from [here](http://www.retrosheet.org/CurrentNames.csv), but most of them are straightforward (NYA is the Yankees, NYN is the Mets, CHN is the Cubs, and CHA is the White Sox).

![]({{ site.url }}/images/Final Results.png)

The above figure shows the average difference between the simulated result and the actual result for every team with at least 5 playoff appearances between 1997 and 2011.  A positive value means the actual results were greater than the simulated results.

###Conclusion

The Braves performed better than their simulated result only once during the measurement period (2001), and they are the second worst team in terms of underperforming in the playoffs.  The Oakland Athletics are the only team to experience more postseason misery than the Atlanta Braves, and we can all hate on the St. Louis Cardinals for outperforming the simulation more than any other team.

This analysis doesn't tell us why the Braves are so bad in the playoffs, but it does tell is that it's not because they're not good enough to win.  The Braves consistently perform below expectations compared to their success in the regular season.