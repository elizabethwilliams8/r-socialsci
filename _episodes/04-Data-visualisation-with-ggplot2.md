---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 04-Data-visualisation-with-ggplot2.md in _episodes_rmd/
title: "Data visualisation with ggplot2"
teaching: 0
exercises: 0
questions:
- "What is the grammar of graphics?"
- "How do I create basic scatter, histogram and box plots?"
- "How can I add additional aesthetics to my plots?"
- "How can I use the data in a data frame to produced summary plots?"

objectives:
- "Define the grammar of graphics and its components"
- "Use ggplot2 to create scatter, histograms and box plots"
- "Add aesthetic components to plots"
- "Use facets to produce multiple plots" 

keypoints:
- "First key point."
---




## Plotting with `ggplot2`

`ggplot2` is a plotting package that makes breaks down the plotting process into three distinct parts.

You specify:

* the variables to plot.
* how they should be displayed
* boilerplate type properties of the plot

If you need to change the plot in anyway like adding new data, changing the plot type  or changing labels etc. we can do so with only need minimal changes to the code.

ggplot likes data in the 'long' format: i.e., a column for every dimension,
and a row for every observation. Well-structured data will save you lots of time
when making figures with ggplot.

ggplot graphics are built step by step by adding new elements. Adding layers in
this fashion allows for extensive flexibility and customization of plots.

Before we start we need to load the 'ggplot2' library and some data. For this episode we will be using the SAFI_results data.


~~~
library(ggplot2)
library(readr)
SAFI_results <- read_csv("data/SAFI_results.csv")
~~~
{: .language-r}



~~~
Parsed with column specification:
cols(
  .default = col_character(),
  Column1 = col_integer(),
  A03_quest_no = col_integer(),
  A04_start = col_datetime(format = ""),
  A05_end = col_datetime(format = ""),
  A11_years_farm = col_integer(),
  B16_years_liv = col_integer(),
  B_no_membrs = col_integer(),
  C05_buildings_in_compound = col_integer(),
  C06_rooms = col_integer(),
  D_plots_count = col_integer(),
  E19_period_use = col_integer(),
  E_no_group_count = col_integer(),
  E_yes_group_count = col_integer(),
  F_liv_count = col_integer(),
  G01_no_meals = col_integer(),
  `_members_count` = col_integer(),
  `gps:Accuracy` = col_double(),
  `gps:Altitude` = col_integer(),
  `gps:Latitude` = col_double(),
  `gps:Longitude` = col_double()
)
~~~
{: .output}



~~~
See spec(...) for full column specifications.
~~~
{: .output}

To build a ggplot, we need to:

- use the `ggplot()` function and bind the plot to a specific data frame using the  
      `data` argument


~~~
ggplot(data = SAFI_results)
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" style="display: block; margin: auto;" />

If you run this line of code you will get a blank canvas in the plots pane. There is nothing wrong with the caode sofar, there is just nothing to show.

- define a mapping (using the aesthetic (`aes`) function), by selecting the variables to be plotted and specifying how to present them in the graph, e.g. as x/y positions or characteristics such as size, shape, color, etc.


~~~
ggplot(data = surveys_complete, aes(x = A11_years_farm, y = B16_years_liv))
~~~
{: .language-r}



~~~
Error in ggplot(data = surveys_complete, aes(x = A11_years_farm, y = B16_years_liv)): object 'surveys_complete' not found
~~~
{: .error}

When you run this line of code, you get the labels for the x and y axis, because ggplot knows the variable names and as it has the data it can calculate suitable ranges for the axes.

- add `geoms` -- graphical representation of the data in the plot (points,
      lines, bars). `ggplot2` offers many different geoms; we will use some
      common ones today, including:
      * `geom_point()` for scatter plots, dot plots, etc.
      * `geom_boxplot()` for, well, boxplots!
      * `geom_bar()` for barplots.  

To add a geom to the plot use the `+` operator. Because we have two continuous variables,  
let's use `geom_point()` first:


~~~
# how many years farming in area v living in area
ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B16_years_liv)) +
  geom_point()
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" style="display: block; margin: auto;" />

No we have a complete graph!

The `+` in the `ggplot2` package is particularly useful because it allows you
to modify existing `ggplot` objects. This means you can easily set up plot
templates and conveniently explore different types of plots, so the above
plot can also be generated with code like this:


~~~
# Assign plot to a variable
SAFI_plot <- ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B16_years_liv))

# Draw the plot
SAFI_plot +
    geom_point()
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-6-1.png" title="plot of chunk unnamed-chunk-6" alt="plot of chunk unnamed-chunk-6" style="display: block; margin: auto;" />

**Notes:**

- Anything you put in the `ggplot()` function can be seen by any geom layers
  that you add (i.e., these are universal plot settings). This includes the x- and
  y-axis mapping you set up in `aes()`.
- You can also specify mappings for a given geom independently of the
  mappings defined globally in the `ggplot()` function. So the following works OK.


~~~
ggplot(data = SAFI_results) +
  aes(x = A11_years_farm, y = B16_years_liv) +
  geom_point()
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-7-1.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" style="display: block; margin: auto;" />

- The `+` sign used to add layers must be placed at the end of each line containing
a layer. If, instead, the `+` sign is added in the line before the other layer,
`ggplot2` will not add the new layer and will return an error message.


## Building your plots iteratively

Building plots with ggplot is typically an iterative process. We start by
defining the dataset we'll use, lay out the axes, and choose a geom:


~~~
ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B16_years_liv)) +
  geom_point()
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-8-1.png" title="plot of chunk unnamed-chunk-8" alt="plot of chunk unnamed-chunk-8" style="display: block; margin: auto;" />

Then, we start modifying this plot to extract more information from it. For
instance, we can add transparency (`alpha`) to avoid overplotting:


~~~
ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B16_years_liv)) +
  geom_point(alpha = 0.1)
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-9-1.png" title="plot of chunk unnamed-chunk-9" alt="plot of chunk unnamed-chunk-9" style="display: block; margin: auto;" />

We can also add colors for all the points:


~~~
ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B16_years_liv)) +
    geom_point(alpha = 0.1, color = "blue")
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-10-1.png" title="plot of chunk unnamed-chunk-10" alt="plot of chunk unnamed-chunk-10" style="display: block; margin: auto;" />

Or to color each species in the plot differently:


~~~
ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B16_years_liv)) +
  geom_point(alpha = 1.0, aes(color = B_no_membrs))
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-11-1.png" title="plot of chunk unnamed-chunk-11" alt="plot of chunk unnamed-chunk-11" style="display: block; margin: auto;" />


> ## Exercise
>
> Create a plot of No of years farming (All_years_farm) against the size of the household (B_no_membrs)
> use the 'size' parameter within the 'aes' to vary the point size based on the number of plots (D_plots_count)
>
> > ## Solution
> >
> > 
> > ~~~
> > \# Years farming  v members in HH
> > \# size based on no of plots
> > ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B_no_membrs)) +
> >   geom_point(aes(size = D_plots_count), color = "blue", alpha = 0.5)
> > ~~~
> > {: .language-r}
> > 
> > 
> > 
> > ~~~
> > Error: <text>:1:1: unexpected input
> > 1: \
> >     ^
> > ~~~
> > {: .error}
> >
> {: .solution}
{: .challenge}


## Boxplot

We can use boxplots to visualize the distribution of weight within each species:


~~~
ggplot(data = surveys_complete, aes(x = species_id, y = weight)) +
    geom_boxplot()
~~~
{: .language-r}



~~~
Error in ggplot(data = surveys_complete, aes(x = species_id, y = weight)): object 'surveys_complete' not found
~~~
{: .error}

By adding points to boxplot, we can have a better idea of the number of
measurements and of their distribution:


~~~
ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B_no_membrs)) +
 geom_boxplot(alpha = 0) +
 geom_jitter(alpha = 0.3, color = "tomato")
~~~
{: .language-r}



~~~
Warning: Continuous x aesthetic -- did you forget aes(group=...)?
~~~
{: .error}

<img src="../fig/rmd-04-unnamed-chunk-14-1.png" title="plot of chunk unnamed-chunk-14" alt="plot of chunk unnamed-chunk-14" style="display: block; margin: auto;" />

> ## Exercise
>
> Boxplots are useful summaries, but hide the *shape* of the distribution. For
> example, if the distribution is bimodal, we would not see it in a
> boxplot. An alternative to the boxplot is the violin plot where the shape (of the density of points) is drawn.
>
> Replace the box plot with a violin plot; see `geom_violin()`.
>
> > ## Solution
> >
> > ggplot(data = SAFI_results, aes(x = A11_years_farm, y = B_no_membrs)) +
> >   geom_violin() +
> >   geom_jitter( color = "tomato")
> >
> >
> {: .solution}
{: .challenge}

## Bar plots

Bar plots are useful for comparing categorical data.

We looked at a simple bar chart in the dplyr episode. we can recreate this in ggplot.


~~~
# create a small dataframe of the wall types and their counts

wall_types <- SAFI_results %>%
  select(C02_respondent_wall_type) %>%
  group_by(C02_respondent_wall_type) %>%
  tally()

# create a bar chart of the wall types
ggplot(data = wall_types, aes(x = C02_respondent_wall_type, y = count_of_type)) +
  geom_bar(stat="identity")
~~~
{: .language-r}



~~~
Error in FUN(X[[i]], ...): object 'count_of_type' not found
~~~
{: .error}

<img src="../fig/rmd-04-unnamed-chunk-15-1.png" title="plot of chunk unnamed-chunk-15" alt="plot of chunk unnamed-chunk-15" style="display: block; margin: auto;" />



The barchart can however be constructed direectly from  the SAFI_results data.


~~~
# create bar chaart directly from SAFI_results
ggplot(data=SAFI_results, aes(x=C02_respondent_wall_type)) +
  geom_bar(stat="count")
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-16-1.png" title="plot of chunk unnamed-chunk-16" alt="plot of chunk unnamed-chunk-16" style="display: block; margin: auto;" />

Apart from the `dplyr` work involved before creating the first plot, there are a couple of signifcant differences between the two approaches.

In the second, more direct approach, we do not specify a 'y' value. This is because the default 'stat=count' in the call to 'geom_bar' will atomatically produce counts for the x axis items and is used on the y axis .

Although you might think that the items along the x axis need to be Factors, as this is essentially how we treat them, they don't. The wall, floor and roof types are all string variables.

We can even the 'same' code to produce barcharts fro numeric values  on the x axis.


~~~
ggplot(data=SAFI_results, aes(x=A11_years_farm)) +
  geom_bar(stat="count")
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-17-1.png" title="plot of chunk unnamed-chunk-17" alt="plot of chunk unnamed-chunk-17" style="display: block; margin: auto;" />

By default all of plots so far have had the labels on the axis determined by the variable we have used. As you might expect, these can be easily changed. You can add your own x any axis labels as well as an overall plot title.


~~~
ggplot(data=SAFI_results, aes(x=factor(C02_respondent_wall_type))) +
  geom_bar(stat="count") +
  ylab("count of each wall type") +  
  xlab("wall types") +
  ggtitle("SAFI Building wall types")
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-18-1.png" title="plot of chunk unnamed-chunk-18" alt="plot of chunk unnamed-chunk-18" style="display: block; margin: auto;" />

> ## Exercise
>
> Create a bar chart showing the number and types of the different roof types (C01_respondent_roof_type)
> Create a bar chart showing a count of the different household sizes (B_no_membrs).
> Provide suitable labels and titles.
>
> > ## Solution
> >
> > 
> > ~~~
> > ggplot(data=SAFI_results, aes(x=C01_respondent_roof_type)) +
> >   geom_bar(stat="count")
> > ~~~
> > {: .language-r}
> > 
> > <img src="../fig/rmd-04-unnamed-chunk-19-1.png" title="plot of chunk unnamed-chunk-19" alt="plot of chunk unnamed-chunk-19" style="display: block; margin: auto;" />
> > 
> > ~~~
> > ggplot(data=SAFI_results, aes(x=B_no_membrs)) +
> >   geom_bar(stat="count")
> > ~~~
> > {: .language-r}
> > 
> > <img src="../fig/rmd-04-unnamed-chunk-19-2.png" title="plot of chunk unnamed-chunk-19" alt="plot of chunk unnamed-chunk-19" style="display: block; margin: auto;" />
> >
> {: .solution}
{: .challenge}



## Faceting

ggplot has a special technique called *faceting* that allows the user to split one plot
into multiple plots based on a factor included in the dataset.

Instead of looking at the wall types across all of the data, we can split it up based on the values in some other variable. Here is the wall type split by village.


~~~
# facet wrap by village
ggplot(data=SAFI_results, aes(x=C02_respondent_wall_type)) +
  geom_bar(stat="count") +
  ylab("count of wall types") +  
  xlab("wall types") +
  ggtitle("SAFI Building wall types") +
  facet_wrap(~ A09_village )
~~~
{: .language-r}

<img src="../fig/rmd-04-unnamed-chunk-20-1.png" title="plot of chunk unnamed-chunk-20" alt="plot of chunk unnamed-chunk-20" style="display: block; margin: auto;" />

> ## Exercise
>
> Create a facetted set of plots which show the different the different villages use differnet
> roof types (C01_respondent_roof_type)
>
> > ## Solution
> >
> > 
> > ~~~
> > ggplot(data=SAFI_results, aes(x=A09_village)) +
> >   geom_bar(stat="count") +
> >   ylab("count of roof types") +  
> >   xlab("villages") +
> >   ggtitle("SAFI Building roof types") +
> >   facet_wrap(~ C01_respondent_roof_type ) + coord_flip()
> > ~~~
> > {: .language-r}
> > 
> > <img src="../fig/rmd-04-unnamed-chunk-21-1.png" title="plot of chunk unnamed-chunk-21" alt="plot of chunk unnamed-chunk-21" style="display: block; margin: auto;" />
> >
> {: .solution}
{: .challenge}

