# ggplot 2

## Links

1. [Beautiful plotting in R: A ggplot2 cheatsheet](http://zevross.com/blog/2014/08/04/beautiful-plotting-in-r-a-ggplot2-cheatsheet-3/)
2. [Data Visualization with ggplot2 cheat sheet by RStudio](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)
3. [Cookbook for R -> Graphs](http://www.cookbook-r.com/Graphs/#graphs-with-ggplot2)
## 1. start with the the quick setup and a default plot followed by a range of adjustments

1. Quick-setup: The dataset
2. A default plot in ggplot2
3. Working with the title
	1. Add a title (ggtitle() or labs())
	2. Make title bold and add a little space at the baseline (face, margin)
	3. Use a non-traditional font in your title (family)
	4. Change spacing in multi-line text (lineheight)
4. Working with axes
	1. Add x and y axis labels (labs(), xlab())
	2. Get rid of axis tick and labels (theme(), axis.ticks.y)
	3. Change size of and rotate tick text (axis.text.x)
	4. Move the labels away from the plot (and add color) (theme(), axis.title.x)
	5. Limit an axis to a range (ylim, scale_x_continuous(), coord_cartesian())
	6. If you want the axes to be the same (coord_equal())
	7. Use a function to alter labels (label=function(x){})
5. Working with the legend
	1. Turn off the legend title (legend.title)
	2. Change the styling of the legend title (legend.title)
	3. Change the title of the legend (name)
	4. Change the background boxes in the legend (legend.key)
	5. Change the size of the symbols in the legend only (guides(), guide_legend)
	6. Leave a layer off the legend (show_guide)
	7. Manually adding legend items (guides(), override.aes)
6. Working with the background colors
	1. Change the panel color (panel.background)
	2. Change the grid lines (panel.grid.major)
	3. Change the plot background (not the panel) color (plot.background)
	4. Working with margins
	5. Changing the plot margin (plot.margin)
7. Working with multi-panel plots
	1. Create a single row of plots based on one variable (facet_wrap())
	2. Create a matrix of plots based on one variable (facet_wrap())
	3. Allow scales to roam free (scales)
	4. Create a grid of plots using two variables (facet_grid())
	5. Put two (potentially unrelated) plots side by side (pushViewport(), grid.arrange())
8. Working with themes
	1. Use a new theme (theme_XX())
	2. Change the size of all plot text elements (theme_set(), base_size)
	3. Tip on creating a custom theme
9. Working with colors
	1. Categorical variables: manually select the colors (scale_color_manual)
	2. Categorical variables: try a built-in palette (based on colorbrewer2.org) (scale_color_brewer):
	3. Color choice with continuous variables (scale_color_gradient(), scale_color_gradient2())
10. Working with annotation
	1. Add text annotation in the top-right, top-left etc. (annotation_custom() and textGrob())
11. Working with coordinates
	1. Flip a plot on it’s side (coord_flip())
12. Working with plot types
	1. Alternatives to the box plot (geom_jitter() and geom_violin())
	2. Create a ribbon (geom_ribbon())
	3. Create a tiled correlation plot (geom_tile())
13. Working with smooths
	1. Default – adding LOESS or GAM (stat_smooth())
	2. Specifying the formula (stat_smooth(formula=))
	3. Adding a linear fit (stat_smooth(method="lm"))
14. Create interactive, online versions of your plots (easier than you think)