# ontology-visualize-spark
Course project for CS786 Special Topics: Parallel Computing for Multicore Systems

The goal of this project is to utilize Spark’s[3] resilient distributed dataset (RDD)[2] feature to store ontology class-restriction topology information, and employ the build in batch operations to facilitate graph computations, such as multiple route identification, subgraph, group coloring. In addition, the data wrapper/holder -- RDDs can be used as input to graph plot tool to draw 3D graphs for better ontology visualization.

readlink -f /usr/bin/java | sed "s:bin/java::"

conda develop 'directory_path'


On Ubuntu 16.04, packages build-essential and python-dev should already be installed, but some other packages are missing, lxml2 and lz. Install them like so:
sudo apt install libxml2-dev libz-dev
That should be all. We can now install igraph for Python 3:
sudo pip3 install python-igraph



conda env export -n name > name.yml
conda env upload my-environment
