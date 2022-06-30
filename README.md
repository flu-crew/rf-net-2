#### OUP Bioinformatics Supplemental
A supplemental reassortment network for the associated Bioinformatics manuscript is located in the `Bioinformatics_Supplemental` folder

Markin, A., Wagle, S., Anderson, T.K. and Eulenstein, O., *2022. RF-Net 2: fast inference of virus reassortment and hybridization networks*. Bioinformatics, 38(8), pp.2144-2152.

## RF-Net 2
The RF-Net executables, supplemental scripts, and a test dataset are located in `RF-Net-2.zip`

### Overview
RF-Net is a method for estimation of entangled evolutionary histories of viruses or species. It reconstructs reassortment or hybridization networks from real, error-prone biological data. RF-Net requires rooted gene/locus phylogenetic trees in the Newick format as an input.

### RF-Net execution
RF-Net requires Java 8 or higher for execution.
To run RF-Net, unpack RF-Net-2.zip in a desired location and execute it as

``java -jar RF-Net-2.0.jar -i <path/to/input/newick> -o <path/to/output/newick> [options]``

Note that it has to be executed from the same location, where you stored the .jar with dependencies.

#### Options
For a full list of RF-Net options run `java -jar RF-Net-2.0.jar -h`.
* ``-r <#num>``: [Default: 5] Maximum number of reticulations (reassortments or hybridizations) in an output network.
* ``-f`` or ``--fast``: Dramatically advances the scalability of RF-Net. Should be used when many reassortment/hybridization events are anticipated (e.g., more that 10).
* ``-e`` or ``--embed``: Enables RF-Net to output error-corrected gene trees in addition to reticulation networks.
* ``-t <#percentage>``: Specifies an optional threshold for adding new reticulations (see more information on that option with `-h`). Example: `-t 5`.

**Usage example**:

``java -jar RF-Net-2.0.jar -i sample-dataset/IAV-delta1A-HANA.tre -o delta1A-network.newick -r 2 -e``

#### RF-Net output
RF-Net writes the computed phylogenetic networks in the extended newick format to the specified output file. This output contains networks with 1, 2, ...  reticulations (up to the maximum number specified with `-r`, unless an additional threshold was specified with `-t`).

The output networks can be visualized with [IcyTree](https://icytree.org) (just drag and drop the output file into the IcyTree tab in browser).

Additionally, if `-e` option was specified, RF-Net writes the *embeddings* of the gene trees (i.e., error-corrected gene trees) to `<output-file>.embeddings.tre`.

In the example above, RF-Net will write the two resulting networks with 1 and 2 reticulations, respectively, to `delta1A-network.newick`. The gene tree embeddings into the network with 2 reticulations will be written to `delta1A-network.newick.embeddings.tre`.

### Choosing the best network by visualizing the embedding cost
RF-Net uses the *embedding cost* as an optimization function. As RF-Net computes multiple networks (for different numbers of reticulations), we provide a pyhton script to visualize the dynamic of the embedding cost as the number of reticulations grows. This visualization can then help select the network with the proper number of reticulations.

The script requires Python 3 and `matplotlib` and takes the RF-Net output file as an input.

For example, to find the proper number of reticulations for the IAV-delta1A-HANA.tre dataset, we can attempt the following:
1. ``java -jar RF-Net-2.0.jar -i sample-dataset/IAV-delta1A-HANA.tre -o delta1A-network.newick -r 5`` computes networks with up to 5 reticulations;
1. ``python3 plot_embedding_costs.py delta1A-network.newick`` then plots the embedding cost dynamic:

<center>
<img src="embedding-cost-plot.png" width="400px">
</center>

As the embedding cost only decreases by one after the first two reticulations, the plot suggests that 2 is the optimal number of reticulations, whereas a larger number of reticulations is likely to cause error-fitting.

### Potential issues with computing the starting tree with RFS
RF-Net uses [RFS](https://almob.biomedcentral.com/articles/10.1186/1748-7188-5-18?optIn=true) as an algorithm for computing a starting *supertree* for the input trees.
As RFS is distributed as binaries, there could appear difficulties with running it on different architectures/operating systems. Therefore, if you run into an error indicating that the RF Supertree method cannot be executed, please try to do the following:
1. For Linux/MacOS users: try running `chmod +x dependencies/RFS.*` from the location where you placed RF-Net and dependencies,
to make sure that RFS binaries have the execution access permissions.
1. If running on a 32-bit Linux distribution, please, make sure to specify the `--lin32` flag in the option list when running RF-Net.
1. If none of these work, run RF-Net using the `-s` option (i.e., providing a starting tree yourself rather than relying on RFS) and report the issue you encountered to Alexey Markin (alexey.markin@usda.gov).
