---
layout: post
title: "Using R in Sublime Text 3"
date: 2014-02-10
---

This post will get you up and running using R in Sublime Text 3.  If you're not familiar with Sublime Text I recommend watching [these videos](https://tutsplus.com/course/improve-workflow-in-sublime-text-2/).  It focuses on Macs and it's a bit outdated, but it's a really good introduction to what you can do with Sublime Text.

<!--break-->

###Installation

The first thing you need to do is head over to [Sublime Text 3](http://www.sublimetext.com/3) and get yourself a copy.  There is a trial version that never expires, but it will bug you to buy it every 100 saves or so.  You'll also need a copy of [R](http://cran.r-project.org/bin/windows/base/).  This tutorial will be focused on Windows, but I've run the same setup on Linux before and the steps are very similar.

###Setup

Sublime Text is great for writing any kind of code, but without being able to send your R code directly to the console it can be cumbersome.  As usual, there is a package for Sublime Text that fixes this problem.

First, we need to install Package Control.  [Go here](https://sublime.wbond.net/installation) and follow the instructions to install it.  To install a package, press `Ctrl+Shift+P`, type `install` and click on the command to install packages.  Search for `SublimeREPL` and install it.  [SublimeREPL](https://github.com/wuub/SublimeREPL) let's you run an interpreter inside of a Sublime Text window.  We will use this to run an R console from within Sublime Text.

SublimeREPL comes with R support built in, but we need to tell it where to find R.  In Sublime Text, go to `Preferences > Package Settings > SublimeREPL > Settings-User`.  You should see a blank document.  We're going to add the R executable to the PATH as shown below.

{% highlight json %}
{
    "default_extend_env": {"PATH": "{PATH};C:\\Program Files\\R\\R3.0.2\\bin\\x64"},
    "show_transferred_text": true
}
{% endhighlight %}

That's what your `Settings-User` file should look like.  Make sure to change the path to your R installation.  If you're on Mac or Linux then it will most likely work out of the box without needing to add the above settings.  I also specify the `show_transferred_text` option because I like to be able to see what is going into the R console.

###Usage

Open up an R console by pressing `Ctrl+Shift+P` and typing `REPL R`.  Once you do this a few times you'll be able to just type `R` and it will come up.  I recommend changing your view to have two windows (`Alt+Shift+2`) so you can have your R code on the left and your R console on the right.  You can also separate them into two different windows and have them each on their own monitor.

There are three options for running your code.  To run the current line or your selected lines, press `Ctrl+Shift+,,l`.  To run just the selected text, press `Ctrl+Shift+,,s`.  To run the entire file, press `Ctrl+Shift+,,f`.  The R console in Sublime Text behaves exactly like your typical R console, but now it has all of the features of Sublime Text (autocomplete, syntax highlighting, etc.).

###Tips and Tricks

Sublime Text is highly customizable, so I'll give you some of my personal preferences here.  I use themes from the `Tomorrow Color Schemes` package.  It can be installed through package manager and more information can be found [here](https://github.com/chriskempson/tomorrow-theme).  It comes with light and dark themes, and I usually switch between them depending on the time of day and my mood.  The font I use is [DejaVu](http://dejavu-fonts.org/wiki/Main_Page).  You can specify different fonts for different syntaxes using `Preferences > Settings-More > Syntax Specific-User`.  I use DejaVu Sans for my R code and DejaVu Mono for my R console.  I should probably use Mono for everything, but I don't care much about fixed width spacing in my R code.

A lot of R coders use periods in their variable names (e.g. `data.table`).  Sublime Text treats this as two different words, which means autocomplete won't work very well and selecting it can be a hassle.  I recommend switching to underscores instead.

A while ago I had an intermittent problem where the keyboard shortcuts for sending code to the R console would randomly stop working.  It took me months to figure it out, but I eventually found the solution [here](https://github.com/wuub/SublimeREPL/issues/271).  Check that out if you run into a similar issue, it involves changing a line in the Python code in the SublimeREPL package.
