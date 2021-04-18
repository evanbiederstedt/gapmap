---
title: "Example with a TCGA dataset"
date: 8 September 2014
output: 
  rmarkdown::html_vignette:
    toc: true
vignette: >
  %\VignetteIndexEntry{"Example with a TCGA dataset"}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
---

This is an R Markdown document, which demonstrates the use of **gapmap** and **dendsort** packages to generate a gapped cluster heatmap visualization.


Let's start by loading the data file from the package, and creating two color palettes.

```r
library(gapmap)
```

```
## Error in library(gapmap): there is no package called 'gapmap'
```

```r
data("sample_tcga")
```

```
## Warning in data("sample_tcga"): data set 'sample_tcga' not found
```

```r
library(RColorBrewer)
RdBu = rev(brewer.pal(11, name="RdBu"))
RdYlBu = rev(brewer.pal(11, name="RdYlBu"))
```

Now you have the data matrix loaded, let's calculate correlation-based distance and perform hierarchical clustering. In this example, we use the correlation-based dissimilarity (Pearson Correlation) and the complete linkage for hierarchical clustering. 

```r
#transpose
dataTable <- t(sample_tcga)
```

```
## Error in t(sample_tcga): object 'sample_tcga' not found
```

```r
#calculate the correlation based distance
row_dist <- as.dist(1-cor(t(dataTable), method = "pearson"))
```

```
## Error in t(dataTable): object 'dataTable' not found
```

```r
col_dist <- as.dist(1-cor(dataTable, method = "pearson"))
```

```
## Error in is.data.frame(x): object 'dataTable' not found
```

```r
#hierarchical clustering
col_hc <- hclust(col_dist, method = "complete")
```

```
## Error in hclust(col_dist, method = "complete"): object 'col_dist' not found
```

```r
row_hc <- hclust(row_dist, method = "complete")
```

```
## Error in hclust(row_dist, method = "complete"): object 'row_dist' not found
```

```r
col_d <- as.dendrogram(col_hc)
```

```
## Error in as.dendrogram(col_hc): object 'col_hc' not found
```

```r
row_d <- as.dendrogram(row_hc)
```

```
## Error in as.dendrogram(row_hc): object 'row_hc' not found
```


Now you are ready to plot the data. First, we will plot a cluster heatmap without any gaps. 


```r
gapmap(m = as.matrix(dataTable), d_row = rev(row_d), d_col = col_d, ratio = 0, verbose=FALSE, col=RdBu,
       label_size=2, v_ratio= c(0.1,0.8,0.1), h_ratio=c(0.1,0.8,0.1))
```

```
## Error in gapmap(m = as.matrix(dataTable), d_row = rev(row_d), d_col = col_d, : could not find function "gapmap"
```

This `gapmap` package was designed to encode the similarity between two nodes by adjusting the position of each leaves. In the traditional representation, all the information about the similarity two adjacent nodes is in the height of the branch in a dendrogram. By positioning leaves based on the similarity, we introduce gaps in both dendrograms and heat map visualization. In the figure below, we exponentially map a distance (dissimilarity) of two nodes to a scale of gap size.  


```r
gapmap(m = as.matrix(dataTable), d_row = rev(row_d), d_col = col_d, mode = "quantitative", mapping="exponential", col=RdBu,
       ratio = 0.3, verbose=FALSE, scale = 0.5, label_size=2, v_ratio= c(0.1,0.8,0.1), h_ratio=c(0.1,0.8,0.1))
```

```
## Error in gapmap(m = as.matrix(dataTable), d_row = rev(row_d), d_col = col_d, : could not find function "gapmap"
```

Since the background is white, we can use another color scale where the value 0 is encoded in yellow. 


```r
gapmap(m = as.matrix(dataTable), d_row = rev(row_d), d_col = col_d, mode = "quantitative", mapping="exponential", col=RdYlBu,
       ratio = 0.3, verbose=FALSE, scale = 0.5, label_size=2, v_ratio= c(0.1,0.8,0.1), h_ratio=c(0.1,0.8,0.1))
```

```
## Error in gapmap(m = as.matrix(dataTable), d_row = rev(row_d), d_col = col_d, : could not find function "gapmap"
```

## dendsort ##
This package works well with the **dendsort** package to reorder the structure of dendrograms. For further information for the **dendsort**, please see the [paper](http://f1000research.com/articles/3-177/v1).


```r
library(dendsort)
gapmap(m = as.matrix(dataTable), d_row = rev(dendsort(row_d, type = "average")), d_col = dendsort(col_d, type = "average"),  
       mode = "quantitative", mapping="exponential", ratio = 0.3, verbose=FALSE, scale = 0.5, v_ratio= c(0.1,0.8,0.1), 
       h_ratio=c(0.1,0.8,0.1), label_size=2, show_legend=TRUE, col=RdBu)
```

```
## Error in gapmap(m = as.matrix(dataTable), d_row = rev(dendsort(row_d, : could not find function "gapmap"
```

## dendrograms ##
You can also plot gapped dendrogram. First you need to create a `gapdata` class object by calling `gap_data()`.  To bring the text labels closer to the dendrogram, we set a negative value to `axis.tick.margin`. This value should be adjusted depending on your plot size. If anyone has a better solution to adjust the position of the axis labels, please let me know.  

```r
row_data <- gap_data(d= row_d, mode = "quantitative", mapping="exponential", ratio=0.3, scale= 0.5)
```

```
## Error in gap_data(d = row_d, mode = "quantitative", mapping = "exponential", : could not find function "gap_data"
```

```r
dend <- gap_dendrogram(data = row_data, leaf_labels = TRUE, rotate_label = TRUE)
```

```
## Error in gap_dendrogram(data = row_data, leaf_labels = TRUE, rotate_label = TRUE): could not find function "gap_dendrogram"
```

```r
dend + theme(axis.ticks.length= grid::unit(0,"lines") )+ theme(axis.ticks.margin = grid::unit(-0.8, "lines"))
```

```
## Error in eval(expr, envir, enclos): object 'dend' not found
```

Here, we can also apply `dendsort`.

```r
row_data <- gap_data(d= dendsort(row_d, type = "average"), mode = "quantitative", mapping="exponential", ratio=0.3, scale= 0.5)
```

```
## Error in gap_data(d = dendsort(row_d, type = "average"), mode = "quantitative", : could not find function "gap_data"
```

```r
dend <- gap_dendrogram(data = row_data, leaf_labels = TRUE, rotate_label = TRUE)
```

```
## Error in gap_dendrogram(data = row_data, leaf_labels = TRUE, rotate_label = TRUE): could not find function "gap_dendrogram"
```

```r
dend + theme(axis.ticks.length= grid::unit(0,"lines") )+ theme(axis.ticks.margin = grid::unit(-0.8, "lines"))
```

```
## Error in eval(expr, envir, enclos): object 'dend' not found
```





