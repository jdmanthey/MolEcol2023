## More Species Delimitation! 31 October 2023

Today, we will be working with some mtDNA phylogenetic data from Ethiopian birds, with sampling
localities indicated in the below figure. In the figure, darker shades indicate higher elevation, and the dotted lines show
putative biogeographic barriers (the Great Rift and Blue Nile Valleys). The table 'sampling.xlsx' has information
about the sampling localities for all the individuals.

![map](https://github.com/jdmanthey/MolEcol2019/blob/master/09_species_delimitation1/map.png)

#### Set the working directory to the same directory used on Tuesday.

#### Check that you are in the right directory

    list.files()

You should see all the files from the folder you unzipped last Thursday. If not, you are in the wrong directory.

#### Load packages

    library(ape)
    library(phangorn)
    library(phytools)
    source("bgmyc.r")

#### Read in the trees file and plot

    # read in file
    trees <- read.nexus("eth_mtDNA.trees")

This file contains 101 samples of phylogenetic tree estimation from the same dataset. It contains all the individuals
found in the spreadsheet you downloaded. We can plot one of the trees like so:

    plot(trees[[1]], cex=0.5)
    
## Answer question #5.

Let's also check out the unrooted tree visualization of the same tree:

    plot(unroot(trees[[1]]),type="unrooted",cex=0.6, use.edge.length=FALSE,lab4ut="axial", no.margin=TRUE)

And lastly visualizing all 101 trees simultaneously:

    densiTree(trees, cex=0.5)

#### Prune the trees to only one species

Randomly choose a species to keep (the code below is a random sampler):

    genus_to_keep <- sample(c("Cossypha", "Melaenornis", "Parophasma", "Serinus", "Turdus", "Zosterops"), 1)

Which one did you choose?

    genus_to_keep

## Look up some info about this species (Wiki, etc.). What is one interesting thing (your opinion) about this species' natural history? Share it on the front board.

Prune the trees to this species:

    tips_to_keep <- trees[[1]]$tip.label[grepl(paste(genus_to_keep, "*", sep=""), trees[[1]]$tip.label)]
    pruned_trees<-lapply(trees, keep.tip, tips_to_keep)
    class(pruned_trees)<-"multiPhylo"

Plot one of the pruned trees:

    plot(pruned_trees[[1]])
    
Plot all of the pruned trees:

    densiTree(pruned_trees, cex=0.5)

## Answer question #6
    
#### Set base plotting scheme

Some of the plotting functions in the bGMYC scripts will change the base plotting settings. Here, we will save the original 
settings:

    old_par <- par(no.readonly = TRUE)
    
If you need to reset the plotting functions later, use:

    par(old_par)

#### Species delimitation (full tree)

This first step will run the MCMC for the full tree. It is a short run for time purposes. If you wanted to do a real analysis
for publication, you'd likely run this much longer (1-2 orders of magnitude longer).

    result_multi <- bgmyc.multiphylo(trees, mcmc=5000, burnin=4000, thinning=100, t1=2, t2=46, start=c(1,1,45))

Now, we will check out the results of delimiting the tips of the phylogeny into different assigned groups:

    result_probmat <- spec.probmat(result_multi)
    
And plot:

    plot(result_probmat, trees[[1]])

#### Species delimitation (pruned tree)

Now we will repeat the above steps for the pruned tree of just the subclade that you chose.

    result_multi2 <- bgmyc.multiphylo(pruned_trees, mcmc=5000, burnin=4000, thinning=100, t1=2, t2=length(pruned_trees[[1]]$tip.label), start=c(1,1,3))

Make the probability matrix:

    result_probmat2 <- spec.probmat(result_multi2)
    
You can also look at the raw data that is used for the heatmap by observing the matrix created from the previous step:

    result_probmat2
    
Plot:

    plot(result_probmat2, pruned_trees[[1]])


## Question #9 (write answers on back of sheet in any open space): What is/are the sister species (1 or more species) of (A) _Zosterops poliogastrus_ and of (B) _Turdus abyssinicus_ based on this phylogeny?



