\markboth{September~2017}
{\MakeLowercase}

\IEEEtitleabstractindextext{%
\begin{abstract}
Large-scale graph analytics has become a quickly growing research area. “Big” graphs, such as social networks, mobile networks and biological networks, can easily reach size of millions of vertices and billions of edges. Given this size of problem, practical graph mining algorithms tend to execute their computations in distributed fashion. Various distributed systems have been adopted for distributing graph processing. Although many of these systems have vastly simplified the implementation of certain graph processing algorithms, a clear guideline and comparison between existing graph processing frameworks is still missing. Also, how to configure and tune these distributed systems to achieve the best outcome and efficiency, in terms of parallelism and resource allocation, has remained unknown. In this report we survey a few most active systems that focus on distributed graph processing. In addition, we experiment a set of carefully constructed benchmark tasks on Apache Spark, a widely used distributed data-flow system, to provide deep analysis and insight understanding of state-of-art distributed graph processing system. We hope our work can further guide application implementations on Apache Spark and uncover some open research areas associated with it. All the work in this report is available at https://github.com/hl395/ontology-visualize-spark.
\end{abstract}

% Note that keywords are not normally used for peerreview papers.
\begin{IEEEkeywords}
graph mining, distributed system, ontology, Spark.
\end{IEEEkeywords}}

\maketitle

\IEEEdisplaynontitleabstractindextext

\IEEEpeerreviewmaketitle

# Introduction

of current social networks data , World Wide Web  graph, and biological
networks \[6\] data has build-in graph nature. For example, Facebook
loads 60 terabytes of new social network data every day . Traditional
graph algorithms assume the input graph fits in the memory or disks of a
single machine. However, the recent growth of the sizes in graphs break
this assumption.

Single machine is no longer capable of handling big graphs problems,
thus we naturally turn to distributed systems. However, graph algorithms
tend to have their computations dictated by the structure of the
underlying graph, making parallelization difficult, exhibit poor
locality of memory access, have a high data access to computation ratio,
and have a varying degree of parallelism during execution  . In
addition, as graphs become distributed across many machines, the task of
processing a large graph inherits many of the classic challenges in
distributed systems: maximizing concurrency, load balancing, designing
efficient patterns for communication between machines, fault tolerance,
and scalability.

Efficiently processing large graphs calls for both distribution and
parallelization, but this require support from both underlying data
structure and framework. In the past several years, many systems have
been developed in an attempt to provide scalable solutions to
large-scale graph processing tasks. Pregel , published in 2010 by
Google, was one of the first distributed processing frameworks built
specifically for handling large graphs. From Pregel, this research area
of large-scale graph processing has seen quickly growing interest.

After accessing many candidates, we choose Spark  as our testing
framework, for its scalability, fault tolerance, and ease of
accessibility. On top of Spark, we run different tasks, including some
common graph mining algorithm, including PageRank , Shortest Path , to
address various aspects of graph processing issues.

To summarize, the main contributions of this paper follows:

1.  A survey of related work focused on motivating graph processing
    systems and uncovering some of the open research areas associated
    with these systems.

2.  An outline of the design and implementation of various tasks in
    order to assess and analyze the performance of different tasks on
    Apache Spark framework.

The rest of the paper is organized as follows. Section 2 describes the
background and related works. In Section 3 we survey some existing graph
processing frameworks and systems, and in Section 4 we describe the
platform setup and test-cases design. In Section 5 we present our
testing results and analysis. After discussing future research
directions in Section 6, we conclude in Section 7.

# Background

Cluster computing frameworks like MapReduce  have been widely adopted
for large-scale data analytics. These systems let users write parallel
computations using a set of high-level operators, without having to
worry about work distribution and fault tolerance.

Although current frameworks provide numerous abstractions for accessing
a cluster’s computational resources, they lack abstractions for
leveraging distributed memory. This makes them inefficient for an
important class of emerging applications: those that reuse intermediate
results across multiple computations. Data reuse is common in graph
algorithms, including PageRank, K-means clustering ans subgraph
identification. Unfortunately, in most current frameworks, the only way
to reuse data between computations (e.g., between two MapReduce jobs) is
to write it to an external stable storage system, e.g., a distributed
file system. Given these limitations, a growing number of distributed
systems have emerged to address these issues.

## Distributed Graph Processing Systems

Before Pregel was developed, there were very few options for users who
wanted to process a large graph in any type of distributed setting.
Users often had to resort to creating custom distributed infrastructure
for different graph algorithms and different graph representations, rely
on using distributed computing platforms like MapReduce which were
poorly suited for graph processing, or use existing parallel graph
systems, which at the time did not handle important issues like fault
tolerance.

From Pregel, this research area of large-scale graph processing has seen
quickly growing interest. In recent years, with systems like Giraph++ ,
NScale , and Arabesque , there has been a paradigm shift towards
subgraph-centric implementations. Many of these implementations achieve
significant performance gains over Pregel. However, there are still
several common challenges in these systems. For instance, providing a
robust approach towards achieving fault tolerance is still a common
issue with these systems with many of them opting for snapshot recovery
instead. Additionally, these systems all use different approaches for
allowing users to run computations: X-Stream  is a system that runs on a
single shared-memory machine that uses an edge-centric graph processing
model with stream-based partitioning. The system uses a synchronous
Scatter-Gather execution model in which scatter and gather functions are
executed in phases on edges to dictate state changes maintained in the
vertices. Another notable system is GraphX , which attempts to address
the limitations of vertex-centric systems by building a graph analytics
framework on top of the existing distributed processing framework,
Spark. Instead of using the Graph-Parallel abstraction of Pregel in
which user-defined vertex-programs are executed concurrently on
vertices, GraphX uses a Gather, Apply, Scatter abstraction that leads to
a pull-based model of message computation.

## Ontology Graph

An ontology is defined as a knowledge structure to enable sharing and
reuse of knowledge by specifying the terms and relationships among them.
Ontologies relate to knowledge sources like dictionaries to literary
work. Ontology can be represented in a graph-based model, where every
node of this graph stands for a "concept." Concepts correspond to words
or short phrases. Typically, concepts correspond to nouns or noun
phrases, such as "man", "car" and "New York". The nodes of the ontology
are connected by different kinds of links. The most important kind of
link is called IS-A link. The nodes and IS-A links together form a
Rooted Directed Acyclic Graph (Rooted DAG). Rooted means that there is
one single "highest node" called the Root. All other nodes are connected
by one IS-A link or a chain of several IS-A links to the Root. IS-A link
is “directed”, which means: If an IS-A link points from a concept X to a
concept Y that means that every thing called X also can be called a Y.
In other words, every X IS-A Y. For instance: A car IS-A vehicle. A dog
IS-A animal.

In fact, most of biomedical knowledge and data are principled via
ontologies in such a way that the knowledge and data are semantically
inter-operable and useful for furthering biomedical science and clinical
care. They are also very large in size. One example will be SNOMED CT
(Systematized Nomenclature of Medicine – Clinical Terms) , which
currently contains more than 327,128 medical concepts, divided into
hierarchies as diverse as body structure, clinical findings, geographic
location and pharmaceutical/biological product. Each concept is
represented by an individual number and several concepts can be used
simultaneously to describe a complex condition. Millions of
relationships(e.g. IS-A link) that connects these concepts to form a big
and complicated graph structure.

# Existing Frameworks

## Spark

Traditional batch systems, such as Hadoop , fall short for graph
processing because they keep state in replicated, on-disk storage
systems between jobs. Instead, Spark use a data structure called
Resilient Distributed Datasets (RDDs) , which keeps data in memory and
can recover it without replication by tracking the lineage graph of
operations that were used to build it. With RDDs, Spark can attain
sub-second end-to-end latencies, which is sufficient for many real-world
big grpah data applications. More details of implementation advantages
on Spark is discussed in Section 4.

## GraphX

GraphX is a new component in Spark for graphs and graph-parallel
computation. At a high level, GraphX extends the Spark RDD by
introducing a new Graph abstraction: a directed multigraph with
properties attached to each vertex and edge. To support graph
computation, GraphX exposes a set of fundamental operators (e.g.,
subgraph, joinVertices, and aggregateMessages) as well as an optimized
variant of the Pregel API. In addition, GraphX includes a growing
collection of graph algorithms and builders to simplify graph analytics
tasks.

GraphX have leveraged Apache Spark to great success, observing that
there is a very close relationship between common graph operators and
the various dataflow operators provided by Spark.

## GraphFrames

GraphFrames  is a package for Apache Spark which provides
DataFrame-based Graphs. It provides high-level APIs in Scala, Java, and
Python. It aims to provide both the functionality of GraphX and extended
functionality taking advantage of Spark DataFrames. This extended
functionality includes motif finding, DataFrame-based serialization, and
highly expressive graph queries.

GraphX is to RDDs as GraphFrames are to DataFrames. GraphFrames
represent graphs: vertices (e.g., users) and edges (e.g., relationships
between users). The key difference is that GraphFrames are based upon
Spark DataFrames, rather than RDDs. GraphFrames also provide powerful
tools for running queries and standard graph algorithms. With
GraphFrames, it is much easier to search for patterns within graphs and
find important vertices.

# Platform Setup and Task Design

One of the most critical design decisions for us was to build all test
cases on top of Apache Spark. We chose Spark based on the following
reasons:

1.  Fault Tolerance. Spark offers fault tolerance of Spark RDD’s by
    recording lineage of operations for each RDD, which enables
    automatic reconstruction upon failures.

2.  Rich Data-flow Operators. Many graph computations can be efficiently
    translated into a series of Spark data-flow operators, which becomes
    especially useful when dealing with distributed memory.

3.  User-Defined Data Partitioning. Spark offers fine user control over
    how data is partitioned. This is useful in the context of our Graph
    Packing implementation, which attempts to find efficient
    configurations for storing different subgraphs across distributed
    memory.

Spark uses a master/slave architecture. To use Spark, developers write a
driver program that connects to a cluster of workers, as shown in Fig.
[\[driver\]](#driver). Spark has one central coordinator (Driver) that
communicates with many distributed workers (executors). The driver and
each of the executors run in their own Java processes.

***Driver*** is the process where the main method runs. First it
converts the user program into tasks and after that it schedules the
tasks on the executors.

***Executors*** are worker nodes’ processes in charge of running
individual tasks in a given Spark job. They are launched at the
beginning of a Spark application and typically run for the entire
lifetime of an application. Once they have run the task they send the
results to the driver. They also provide in-memory storage for RDDs that
are cached by user programs through Block Manager.

***Application execution flow*** is simplified as following: A
standalone application starts and instantiates a SparkContext instance.
Then the driver program ask for resources to the cluster manager to
launch executors. The cluster manager launches executors. The driver
process runs through the user application. Depending on the actions and
transformations over RDDs task are sent to executors. Executors run the
tasks and save the results.

\centering

![Spark Cluster Components<span label="driver"></span>](driver)

The data and process flow of our test-cases design is shown in Fig.
[\[flow\]](#flow). We read Ontology graph data(in CSV format) from
Hadoop input source (HDFS) to Spark RDDs in memory. This is our first
task – (1)Input/Output task. Then we process the raw graph data by
performing batch operations such as "map" and "join", to filter
information associated with each concept and the hierarchic structure of
these concepts in a chosen ontology. This composes our second task – (2)
Batch Operations. Then, to facilitate our graph mining tasks, the Spark
RDDs are further converted into Graphframes. Then we evaluate two graph
mining tasks : (3) Pagerank for all concepts in the graph and (4)
Shortest Path from one concept to each other concept in that ontology.

\centering

![Flow Chart of Test-cases<span label="flow"></span>](flow)

To build the Spark cluster framework and testing Spark’s flexibility on
heterogeneous computers, we select one Dell desktop and one Thinkpad
laptop to form testing cluster. We summarize their hardware
configuration details in Table. [\[tab1\]](#tab1).

|               |         |  |  |
| :-----------: | :-----: | :-: | :-: |
|   **Model**   | **CPU** |  |  |
|  Processors   |  ****   |  |  |
|               |         |  |  |
| OptiPlex-7010 |         |  |  |
|    i7-3770    |         |  |  |
| CPU @ 3.40GHz |    8    |  |  |
| 1600 MHz 16GB |         |  |  |
|               |         |  |  |
|     X220      |         |  |  |
|   i7-2640M    |         |  |  |
| CPU @ 2.80GHz |    4    |  |  |
| 1333 MHz 8GB  |         |  |  |

Hardware Configuration

<span id="tab1" label="tab1">\[tab1\]</span>

We chose the Dell Desktop as our ***master*** with both ***master*** and
***worker*** running on it. On Thinkpad laptop, we have one ***worker***
stand by, meaning that we only wake it up when testing with both workers
is necessary. ***Master*** and external ***worker*** are connected via a
high speed switch supporting up to 1000 Mbps network traffic.

# Results and Analysis

We conducting our test-cases from four categories by varying parameters
such as number of workers, number of executors per work, number of cores
per executor, and amount of memory allocated to each executors. By
permutation and combination of these parameters, we record task duration
by taking average time of ten executions of each task, to guarantee
consistency. Note that duration is denoted in second as unit. We present
some of our testing results and findings as follows:

**One Executor Multi-cores** First we try to employ only one executor
but double the number of cores it can use from 1 core to 8 cores. We can
see from Fig. [\[1EMC\]](#1EMC), duration for all four tasks reduce to
half when using 2 cores instead of 1 core. This improvement does not
hold when double the cores from 2 to 4, leading to about 35 percentage
duration reduction. However, if we double the cores from 4 to 8, we
observe a little improvement for batch operations, and even worse
performance for Pagerank and Shortest Path tasks. We dig into this
problem by scanning through the CPU history (Fig.
[\[cpuhistory\]](#cpuhistory)). We figure out that both Spark’s master
and executor randomly pick up CPU resource available to them. Even
though executor bound its overall CPU occupation to its assigned upper
bound, master consumes more CPU resource as number of executor’s cores
increased. Thus, with one-core executor consume around 20% CPU usage,
double the number of cores to 2 occupies around 45-50% CPU resource.
Increasing the number of cores to 4 already push the CPU usage to almost
85%. When we fully utilize all 8 cores, heavy CPU contention is
inevitable, which downgrades the overall performance.

\centering

![One Executor Multi-Cores (in second).<span label="1EMC"></span>](1EMC)

![1 core.<span label="cpu1core"></span>](CPU_1E1C)

![2 cores.<span label="cpu2core"></span>](CPU_1E2C)

![4 cores.<span label="cpu4core"></span>](CPU_1E4C)

![8 cores.<span label="cpu8core"></span>](CPU_1E8C)

**Multiple One-core Executors** To assess the impact of different number
of executors, we evaluate 1, 2, 4, and 8 one-core executors into 4
test-cases respectively. By doing so, we summarize the result in Fig.
[\[ME1C\]](#ME1C). Increasing the number of executors decrease the task
duration in general. Given the limited input size, 4 executors already
push the hardware capability to its limit. Thus, doubling the number of
cores to 8 does not help much in terms of task duration for all 4
test-cases.

![Multiple One-core Executors (in
second).<span label="ME1C"></span>](ME1C)

**Eight Cores with Different Configuration** To find out the best
combination between number of executors and number of cores per
executor, we assign all 8 cores to 1, 2, 4, and 8 executors
respectively. The result in Fig. [\[8C\]](#8C) shows that 4 executors
with 2 cores each setup actually gives the best performance. This is due
to in 8 executors setting, the extra number of threads spawned by each
executor occupy much more resource in total, which leads to heavier
contention than 4 executors setting.

![Eight Cores with Different Configuration (in
second).<span label="8C"></span>](8C)

**One Worker vs. Two Workers** To extend our experiment from Spark’s
standalone mode to cluster model, we wake up the second worker which
resides on external computer, then repeat the test cases. To interpret
the result in Fig. [\[1W2W\]](#1W2W), we compare 1 worker that has 4
one-core executors (blue) versus 2 workers that each has 2 one-core
executors (red). Due to the slow CPUs on the second worker and data
exchange between master and worker, we observe longer duration for all
tasks. However, in a more aggressive setting which we utilize 8 cores: 1
worker that has 4 two-cores executors (orange) versus 2 workers that
each has 2 two-cores executors (green), we found that the 2 workers
setting triumphs with shorter duration. We pull out the process and
thread history (Fig. [\[threadhistory\]](#threadhistory)) to analyze the
potential causes. It turns out that each executor as a process will
spawn around 50 - 90 threads on the fly. Besides those threads that is
assigned to grasp cores for execution, there are also auxiliary cores
that occupy some CPU resource while stand-by. With 4 executors reside on
the same computer, threads compete for CPU resource leading to more
contention than the 2 workers cluster setting. Given the trade off of
CPU processing speed and data transfer on network in cluster setting,
the contention among threads in 1 worker setting still introduce more
execution time than the distributing 4 executors among two workers.

![One Worker vs. Two Workers (in
second)<span label="1W2W"></span>](1W2W)

![Executor 1.<span label="thread1"></span>](top_4E_1)

![Executor 2.<span label="thread2"></span>](top_4E_2)

![Executor 3.<span label="thread3"></span>](top_4E_3)

![Executor 4.<span label="thread4"></span>](top_4E_4)

**Memory Usage** We use 1 GB memory per executor as baseline of all
test-cases. To exclude any potential memory effect, we boost the memory
to 2GB per executor and re-run all tests. From Fig.
[\[Memory\]](#Memory), we can see that duration for all four tasks do
not vary much if allocate more memory. This is due to the given the
input size of 2432 MB CVS data, the testing data after
preprocessing(batch operation), is around 200 MB. Any executor in our
case can easily handle that amount of data, so we can eliminate the
memory impact to our test result.

![Memory Usage.<span label="Memory"></span>](Memory)

**Task Duration Variance** Though we only present average duration for
each task, we did notice some obvious fluctuation in time in cluster
setting (Fig. [\[Variance\]](#Variance)). Deviation from the average
value could be the result of occasional system CPU utilization or the
network disturbance. Due to unpredictable network loads transmitting on
our switch, there is about 20 seconds gap between light traffic and
heavy traffic cases for Shortest Path test.

![Task Duration Variance (in
second).<span label="Variance"></span>](Variance)

**Best Performance** While performance evaluation is our goal here, we
also experiment different scenario that achieves best outcome in terms
of execution duration and latency. We find out there is no one fixed
configuration that fits all types of tasks. However, given the
experiment result (Fig. [\[best\]](#best)), we are able to argue a
general guideline to follow in program design: If the application
consists intensive batch operations, it should exploit all the resource
that available to it. If graph mining algorithm with moderate IO
operation involved, such as Pagerank and Shortest Path tasks, are the
main focus, then distributing computations among cluster with some trade
off is worth exploring.

![Best Performance.<span label="best"></span>](best)

# Future Work

In order to be certain of the correctness of many of our test-cases, it
will be necessary to conduct a comprehensive experimental analysis
comparing the performance and memory usage of Spark against other
comparable graph processing systems. Testing data from different domain
and knowledge filed should also be considered. By conducting these
experimental analysis, we hope to use the results to generate a thorough
guide of how to tune the implementations of graph algorithms or
applications in distributed fashion.

# Conclusion

In this paper we survey some state-of-art works that focus on motivating
massive, real-world graphs processing in distributed systems. We also
assess and analyze the performance of different tasks on Apache Spark
framework by designing and implementing test-cases from four different
categories. Based on our test results and analysis, we provide a general
guideline for applications implemented on state-of-art graph distributed
processing systems.

# Acknowledgment

The author would like to thank Prof. Jing Li for her guidance and help
in the processing of implementing this project.

\ifCLASSOPTIONcaptionsoff

\newpage

\fi

<span>1</span>

\bibitem{biological}

D. A. Bader and K. Madduri. A graph-theoretic analysis of the human
protein-interaction network using mul-ticore parallel algorithms.
Parallel Comput., 2008.

\bibitem{web}

A. Broder, R. Kumar, F. Maghoul, P. Raghavan, S. Rajagopalan, R. Stata,
A. Tomkins, and J. Wiener. Graph structure in the web. Computer Networks
33, 2000.

\bibitem{graphframe}

Dave, A., Gonzalez, J., Jindal, A., Li, E.L., Xin, R., and Zaharia, M.
(2016). GraphFrames: an integrated API for mixing graph and relational
queries. GRADES.

\bibitem{social}

N. B. Ellison, C. Steinfield, and C. Lampe. The benefits of facebook
friends: social capital and college students use of online social
network sites. Journal of Computer-Mediated Communication,
12(4):1143–1168, 2007.

\bibitem{mapreduce}

J. Dean and S. Ghemawat. Mapreduce: simplified data processing on large
clusters. Commun. ACM, 51(1):107–113, 2008.

\bibitem{hadoop}

Hadoop information. http://hadoop.apache.org/.

\bibitem{challenge}

A. Lumsdaine, D. Gregor, B. Hendrickson, and J. Berry. Challenges in
parallel graph processing. Parallel Processing Letters, 17(01):5–20,
2007.

\bibitem{pregel}

G. Malewicz, M. H. Austern, A. J. Bik, J. C. Dehnert, I. Horn, N.
Leiser, and G. Czajkowski. Pregel: a system for large-scale graph
processing. In Proceedings of the 2010 ACM SIGMOD International
Conference on Management of data, pages 135–146. ACM, 2010.

\bibitem{spark}

Matei Zaharia, etc. 2012. Resilient distributed datasets: a
fault-tolerant abstraction for in-memory cluster computing. In
Proceedings of the 9th USENIX conference on Networked Systems Design and
Implementation (NSDI’12). USENIX Association, Berkeley, CA, USA, 2-2.

\bibitem{pagerank}

L. Page, S. Brin, R. Motwani, and T. Winograd. The PageRank citation
ranking: Bringing order to the web. Technical report, Stanford Digital
Library Technologies Project, 1998.

\bibitem{nscale}

A. Quamar, A. Deshpande, and J. Lin. Nscale: neighborhood-centric
analytics on large graphs. Proceedings of the VLDB Endowment,
7(13):1673–1676, 2014.

\bibitem{shortestpath}

Robert W. Floyd. 1962. Algorithm 97: Shortest path. Commun. ACM 5, 6
(June 1962), 345-. DOI: http://dx.doi.org/10.1145/367766.368168

\bibitem{xstream}

A. Roy, I. Mihailovic, and W. Zwaenepoel. X-stream: Edge-centric graph
processing using streaming partitions. In Proceedings of the
Twenty-Fourth ACM Symposium on Operating Systems Principles, pages
472–488. ACM, 2013.

\bibitem{snomed}

SNOMED CT, http://www.nlm.nih.gov/research/umls/Snomed/snomed\_main.html

\bibitem{Arabesque}

C. H. Teixeira, A. J. Fonseca, M. Serafini, G. Siganos, M. J. Zaki, and
A. Aboulnaga. Arabesque: a system for distributed graph mining. In
Proceedings of the 25th Symposium on Operating Systems Principles, pages
425–440. ACM, 2015.

\bibitem{facebook}

A. Thusoo, Z. Shao, S. Anthony, D. Borthakur, N. Jain, J. S. Sarma, R.
Murthy, and H. Liu. Data warehousing and analytics infrastructure at
facebook. In SIGMOD Conference, pages 1013–1020, 2010.

\bibitem{graph++}

Y. Tian, A. Balmin, S. A. Corsten, S. Tatikonda, and J. McPherson. From
think like a vertex to think like a graph. Proceedings of the VLDB
Endowment, 7(3):193–204, 2013.

\bibitem{graphx}

R. S. Xin, J. E. Gonzalez, M. J. Franklin, and I. Stoica. A resilient
distributed graph system on Spark. In First International Workshop on
Graph Data Management Experiences and Systems, page 2. ACM, 2013.
