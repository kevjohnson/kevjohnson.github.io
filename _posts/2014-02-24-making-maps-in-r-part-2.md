---
layout: post
title: "Making Maps in R (Part 2)"
description: "Part 2 of tutorial on making maps in R.  This part goes over how to plot your data on top of Google Maps."
---

This is a continuation of my [previous post]({{ site.url }}/making-maps-in-r/) on making maps in R with ggplot2.

<!--break-->

![]({{ site.url }}/images/map6.png)

The above map looks nice and all, but what if we want to zoom in on a more populated area like Atlanta?  We could just use `xlim()` and `ylim()` to zoom this map into a specified latitude and longitude, but I'd like to be able to see where exactly in the city I am.  This is where the [ggmap](http://cran.r-project.org/web/packages/ggmap/index.html) package comes in handy.

The first step is to use the `get_map()` function to retrieve a map from the Google Maps API.  The zoom parameter specificies how far you want the map to be zoomed in.  In general, 3 is a continent, 10 is a city, and 21 is a building.  Let's get a quick map of the metro Atlanta area.

{% highlight r %}
map <- get_map("Atlanta", zoom=10)
p <- ggmap(map)
ggsave(p, file = "map7.png", width = 5, height = 5, type = "cairo-png")
{% endhighlight %}

![]({{ site.url }}/images/map7.png)

I don't really like this particular look, but `get_map()` comes with a variety of options for the desired style of map (terrain, satellite, roadmap, hybrid).  Let's see what the roadmap style looks like.

{% highlight r %}
map <- get_map("Atlanta", zoom = 10, maptype = "roadmap")
p <- ggmap(map)
ggsave(p, file = "map8.png", width = 5, height = 5, type = "cairo-png")
{% endhighlight %}

![]({{ site.url }}/images/map8.png)

Well that looks familiar.  I'll provide further examples later on, but for now let's stick with this style.  So, how do we get data on this map?  The best feature of `ggmap()` is that it returns a ggplot object, which means we can apply everything we learned in the previous tutorial (with a few modifications).

{% highlight r %}
p <- ggmap(map) +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent), colour = NA, alpha = 0.5) +
    scale_fill_distiller(palette = "YlOrRd", breaks = pretty_breaks(n = 10), 
        labels = percent) +
    labs(fill = "") +
    theme_nothing(legend = TRUE) +
    guides(fill = guide_legend(reverse = TRUE, override.aes = 
        list(alpha = 1)))
ggsave(p, file = "map9.png", width = 5, height = 4, type = "cairo-png")
{% endhighlight %}

![]({{ site.url }}/images/map9.png)

We have colors on our map now, but something is clearly off at the edges of our image.  When the boundaries of a census tract intersect with the boundaries of the map, bad things happen.  We need to somehow subset our data to only include census tracts within the boundaries of the map, but we also want the census tracts on the edges to still be visible. We can accomplish this with the following code.

{% highlight r %}
library(raster)
library(rgeos)
box <- as(extent(as.numeric(attr(map, 'bb'))[c(2,4,1,3)] + 
    c(.001,-.001,.001,-.001)), "SpatialPolygons")
proj4string(box) <- CRS(summary(tract)[[4]])
tract <- readOGR(dsn = ".", layer = "gz_2010_13_140_00_500k")
tractSub <- gIntersection(tract, box, byid = TRUE, 
    id = as.character(tract$GEO_ID))
tractSub <- fortify(tractSub, region = "GEO_ID")
plotData <- left_join(tractSub, data, by = "id")
{% endhighlight %}
    
The first line creates a bounding box that we use with the `gIntersection()` function in order to properly subset the census tracts.  The bounding box is based on the size of the map (with an adjustment of .001), and we calculate the intersection between the box and the census tract shapefile.  In the end, we end up with the same dataframe as before except with a modified shapefile.

{% highlight r %}
p <- ggmap(map) +
    geom_polygon(data = plotData, aes(x = long, y = lat, group = group, 
        fill = percent), colour = NA, alpha = 0.5) +
    scale_fill_distiller(palette = "YlOrRd", breaks = pretty_breaks(n = 10), 
        labels = percent) +
    labs(fill = "") +
    theme_nothing(legend = TRUE) +
    guides(fill = guide_legend(reverse = TRUE, override.aes = 
        list(alpha = 1)))
ggsave(p, file = "map10.png", width = 5, height = 4, type = "cairo-png")
{% endhighlight %}

![]({{ site.url }}/images/map10.png)

We fixed the problem with the borders of our map, but I'm still not completely satisfied.  The jagged edges of the census tract boundaries aren't very pleasing to the eye and they make it slightly more difficult to see what's going on.  Let's smooth this out a bit.

{% highlight r %}
data <- data[data$id %in% unique(tractSub$id),]
centroids <- data.frame(coordinates(tract), tract$GEO_ID)
colnames(centroids) <- c("long", "lat", "id")
d <- left_join(data, centroids)
d <- d[complete.cases(d),]
{% endhighlight %}

First, we subset the data according to the census tract dataframe that we just created.  We're going to be interpolating between every data point, so the less data we have the better.  Next, we use the `coordinates()` function to extract the centroids and join that with our dataframe.  `d` now contains the centroid latitude, centroid longitude, and percent value for each census tract in metro Atlanta.

We will use the `interp()` function from the `akima` package to fill in the missing area using the values at the centroids.

{% highlight r %}
akima_li <- interp(x = d$long,
    y = d$lat,
    z = d$percent,
    yo = seq(min(d$lat), max(d$lat), length = 250),
    xo = seq(min(d$long), max(d$long), length = 250),
    linear = FALSE,
    extrap = TRUE)
dInterp <- data.frame(expand.grid(x = akima_li$x, y = akima_li$y), z = c(akima_li$z))
dInterp$z <- ifelse(dInterp$z < 0, 0, dInterp$z)
{% endhighlight %}

This isn't a statistics post so I'm going to treat this like magic for purposes of this tutorial.  The most important value here is the `length` parameter which basically controls the resolution of our heatmap.  The default value is 40, and I would recommend setting it to around 20 for testing purposes.  I set it to 250 when I know everything works and I'm ready for my final output because it can take quite a while to run.

Finally, we'll use the `geom_tile()` function to map the data we have in `dInterp`.  Nothing too crazy here, check the ggplot docs if you're confused about this part.

{% highlight r %}
p <- ggmap(map) +
    geom_tile(data = dInterp, aes(x, y, fill = z), alpha = 0.5, 
        color = NA) +
    scale_fill_distiller(palette = "YlOrRd", breaks = pretty_breaks(n = 10), 
        labels = percent) +
    labs(fill = "") +
    theme_nothing(legend = TRUE) +
    guides(fill = guide_legend(reverse = TRUE, override.aes = 
        list(alpha = 1)))
ggsave(p, file = "map11.png", width = 5, height = 4, type = "cairo-png")
{% endhighlight %}

![]({{ site.url }}/images/map11.png)

We're done!  We now have a nice looking heatmap of the percentage of people in metro Atlanta who do not have health insurance.