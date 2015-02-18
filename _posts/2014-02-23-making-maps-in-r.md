---
layout: post
title: "Making Maps in R"
date: 2014-02-23
modified: 2015-02-15
description: "Tutorial on how to make your own maps in R."
---

**Update (2015-02-15):** My workflow has changed significantly in the last year so I have updated this post to reflect how I currently do things.

I make a lot of maps in my line of work. R is not the easiest way to create maps, but it is convenient and it allows for full control of what the map looks like. There are tons of different ways to create maps, even just within R. In this post I'll talk about the method I use most of the time.  I will assume you are proficient in R and have some level of familiarity with the [ggplot2](http://www.ggplot2.org/) package ([click here]({{ site.url }}/pdfs/ggplot2.pdf) for an introduction to ggplot2 that I wrote for my MS Analytics students).

<!--break-->

The American Community Survey provides data on almost any topic imaginable for various geographic levels in the US. For this example I will look at the 2012 5-year estimates of the percent of people without health insurance by census tract in the state of Georgia (obtained from the [US Census FactFinder](http://factfinder2.census.gov/)). Shapefiles were obtained from the [US Census TIGER database](http://www.census.gov/geo/maps-data/data/tiger.html). I generally use the cartographic boundary files since they are simplified representations of the boundaries, which saves a lot of space and processing time.

Here's a list of packages I'll be using:

* [ggplot2](http://www.ggplot2.org/)
* [rgdal](http://cran.r-project.org/web/packages/rgdal/index.html): reads in shape files
* [scales](http://cran.r-project.org/web/packages/scales/index.html): tells ggplot what the proper scale should be
* [ggmap](http://cran.r-project.org/web/packages/ggmap/index.html): comes with a nice `theme_nothing()` function
* [dplyr](http://cran.r-project.org/web/packages/dplyr/index.html): I only use the `left_join()` function but this is a very useful package to add to your toolbox if you haven't already
* [Cairo](http://cran.r-project.org/web/packages/Cairo/index.html): creates high quality vector and bitmap images

The first step is to read in the shapefile that describes the census tract boundaries.

{% highlight r %}
tract <- readOGR(dsn = ".", layer = "gz_2010_13_140_00_500k")
tract <- fortify(tract, region="GEO_ID")
{% endhighlight %}

The `readOGR()` function reads a shapefile and stores it in a `SpatialPolygonsDataFrame` object.  You need to supply the `dsn` (location of the folder where the shapefile is located) and `layer` (name of the shapefile without the extension).  The `fortify()` function from ggplot2 transforms data from shapefiles into a dataframe that ggplot can understand. You need to supply it the region in question, which for cartographic shapefiles will generally be something like TRACT, COUNTY, STATE, etc. The safest option is to use GEO_ID since it retains all information about the area in question.  You can find the available options by using `names(tract)` after reading the shapefile.

Next, read in the data and choose the columns you are interested in.  Here, we are interested in the percent of people with no form of health insurance.

{% highlight r %}
data <- read.csv("ACS_12_5YR_DP03.csv", stringsAsFactors = FALSE)
data <- data[,c("GEO.id2", "HC03_VC131")]
colnames(data) <- c("id", "percent")
data$id <- as.character(data$id)
data$percent <- data$percent/100
{% endhighlight %}

We want to join `data` with `tract` using the `id` variable.  In `data` that variable looks like this: 13001950100. In `tract` it looks like this: 1400000US13001950100.  We need to transform `data$id` so that it will match up with `tract$id`.  We could do it the other way around but then we would have to transform `tract$group` and it gets a little messier.

{% highlight r %}
data$id <- paste("1400000US", data$id, sep = "")
plotData <- left_join(tract, data)
{% endhighlight %}

It's finally time to create a map.  There are several functions in ggplot2 that will create a map, but after plenty of trial and error I now use `geom_polygon()` almost 100 percent of the time.  I have found that it offers the most flexibility while retaining simplicity.  Here is a very simple map.

{% highlight r %}
p <- ggplot() +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent), color = "black", size = 0.25)
ggsave(p, file = "map1.png", width = 6, height = 4.5, type = "cairo-png")
{% endhighlight %}
![map1]({{ site.url }}/images/map1.png)

We have a map! Unfortunately, there are many problems with this map that need to be addressed, most noticeably the severe distortion of the shape of Georgia. The `coord_map()` function in ggplot2 will take care of this for us. This will keep our map in shape no matter what we do to the dimensions of the image.

{% highlight r %}
p <- ggplot() +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent), color = "black", size = 0.25) +
    coord_map()
ggsave(p, file = "map2.png", width = 5, height = 4.5, type = "cairo-png")
{% endhighlight %}
![map2]({{ site.url }}/images/map2.png)

I don't like how the census tract borders make it impossible to see the data in heavily populated areas. I could replace `colour = "black"` with `colour = NA` to get rid of the borders, but I'm a Georgia native so I like to have borders to help me know where I am.

Let's replace the census tract borders with county borders instead. We'll read in the county shapefile just like the old one, and then add a new `geom_polygon()` layer to draw the county borders.

{% highlight r %}
county <- readOGR(dsn = ".", layer = "gz_2010_13_060_00_500k")
county <- fortify(county, region="COUNTY")

p <- ggplot() +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent)) +
    geom_polygon(data = county, aes(x = long, y = lat, group = group), 
        fill = NA, color = "black", size = 0.25) +
    coord_map()
ggsave(p, file = "map3.png", width = 5, height = 4.5, type = "cairo-png")
{% endhighlight %}
![map3]({{ site.url }}/images/map3.png)

It's important to note that the order of the layers matters.  If I switched the order of the `geom_polygon()` functions in the code above then the census tract data would be drawn over the county borders and you wouldn't see anything.  I recently used this concept to automatically replace missing zip code data with county values.

The default colors from ggplot aren't bad, but I'd like to be able to change them to suit my needs. Head on over to the fantastic [Color Brewer](http://colorbrewer2.org/) website and choose your favorite color palette. Green is my favorite color, so I'll use the "Greens" palette in this example.

I used to use the `scale_fill_gradientn()` function and supply the colors manually with the RColorBrewer package, but then I discovered the `scale_fill_distiller()` function that makes everything easier.  Simply choose your color palette and everything else is done for you.

{% highlight r %}
p <- ggplot() +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent)) +
    geom_polygon(data = county, aes(x = long, y = lat, group = group), 
        fill = NA, color = "black", size = 0.25) +
    coord_map() +
    scale_fill_distiller(palette = "Greens")
ggsave(p, file = "map4.png", width = 5, height = 4.5, type = "cairo-png")
{% endhighlight %}
![map4]({{ site.url }}/images/map4.png)

I think it makes more sense for the higher percentages to be on top rather than on the bottom.  I would also like to have percentages in my legend rather than decimals, and the legend could use a few more breaks.  The following code implements those changes with some help from `pretty_breaks()` in the scales package.

{% highlight r %}
p <- ggplot() +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent)) +
    geom_polygon(data = county, aes(x = long, y = lat, group = group), 
        fill = NA, color = "black", size = 0.25) +
    coord_map() +
    scale_fill_distiller(palette = "Greens", labels = percent, 
        breaks = pretty_breaks(n = 10)) +
    guides(fill = guide_legend(reverse = TRUE))
ggsave(p, file = "map5.png", width = 5, height = 4.5, type = "cairo-png")
{% endhighlight %}
![map5]({{ site.url }}/images/map5.png)

Finally it's time to get rid of the gray background and axis labels.  I used to include 10 more lines of options to get rid of everything, but now I use the handy `theme_nothing()` function provided by the ggmap package.  Let's add a title to this plot while we're at it and remove the "percent" on our legend.

{% highlight r %}
p <- ggplot() +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent)) +
    geom_polygon(data = county, aes(x = long, y = lat, group = group), 
        fill = NA, color = "black", size = 0.25) +
    coord_map() +
    scale_fill_distiller(palette = "Greens", labels = percent, 
        breaks = pretty_breaks(n = 10), values = c(1,0)) +
    guides(fill = guide_legend(reverse = TRUE)) +
    theme_nothing(legend = TRUE) +
    labs(title = "Percentage of Population Without\nHealth Insurance",
        fill = "")
ggsave(p, file = "map6.png", width = 5, height = 4.5, type = "cairo-png")
{% endhighlight %}
![map6]({{ site.url }}/images/map6.png)

We're done! We now have a nice looking map of the percentage of people without health insurance for every census tract in Georgia.  This post has only scratched the surface of what you can do with maps in R, but it's a good starting point for learning how to make maps.