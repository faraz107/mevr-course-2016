# Distances walkthrough



First load the libraries you need.


```r
library(ape)
library(phangorn)
library(network)
```

```
## network: Classes for Relational Data
## Version 1.12.0 created on 2015-03-04.
## copyright (c) 2005, Carter T. Butts, University of California-Irvine
##                     Mark S. Handcock, University of California -- Los Angeles
##                     David R. Hunter, Penn State University
##                     Martina Morris, University of Washington
##                     Skye Bender-deMoll, University of Washington
##  For citation information, type citation("network").
##  Type help("network-package") to get started.
```

Load the multiple sequence alignment using ```read.dna``` from the ```ape``` library.


```r
myalignment.filename <- "ray2000_pruned_degapped.fas"
myalignment <- read.dna(myalignment.filename,format="fasta",as.matrix=TRUE)
```

Calculate distances using the TN93 model.


```r
myalignment.dist.tn93 <- dist.dna(myalignment,model="TN93",as.matrix=TRUE)
```

Plot out the distances.


```r
hist(myalignment.dist.tn93[lower.tri(myalignment.dist.tn93)],xlab="Distance",ylab="Frequency",main="",col="grey")
```

![plot of chunk plot_distances](figure/plot_distances-1.png) 

Calculate a neighbour joining tree.


```r
myalignment.tn93.nj <- nj(myalignment.dist.tn93)
```

Plot the tree out and add a scale bar.


```r
plot(myalignment.tn93.nj,"unrooted",cex=0.5)
add.scale.bar(length=0.1)
```

![plot of chunk plot_tree](figure/plot_tree-1.png) 

Bootstrap the alignment; we first define a function to make a tree from the alignment, then call ```boot.phylo```.


```r
maketree <- function(x) nj(dist.dna(x,model="TN93"))
myalignment.tn93.nj.boot <- boot.phylo(myalignment.tn93.nj,myalignment,maketree,B=100,block=3,trees=TRUE,quiet=FALSE,rooted=FALSE)
```

```
## 
## Calculating bootstrap values... done.
```

Now we can add the bootstrap supports to the tree.


```r
plotBS(myalignment.tn93.nj,myalignment.tn93.nj.boot$trees,type="phylogram",cex=0.5)
```

![plot of chunk plot_bootstrap](figure/plot_bootstrap-1.png) 

We can also create a network of 'similar' sequences, by defining a threshold distance.


```r
thresh <- 0.01
myalignment.dist.tn93.net <- network(myalignment.dist.tn93<=thresh)
plot(myalignment.dist.tn93.net)
```

![plot of chunk plot_network](figure/plot_network-1.png) 
