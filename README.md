What is Pyspark?
--> Analytical processing engine
--> Parallel processing (multiple nodes)
--> Powerful data processing and machine learning applicationss
--> Processing large data

Who uses pyspark?
--> Data science and machine learning community

When to use pandas and pyspark?
--> For smaller dataset --> Pandas (single node)
--> For large data --> Spark (distributed processing)

What are the features of pyspark?
--> In-memory computation
--> Distributed processing 
--> Can be used with many cluster managers (Spark, Yarn, Mesos etc)
--> Fault tolerant
--> Immutable (RDDs are immutable)
--> Cache and persistent
--> Lazy evaluation
--> Supports ANSI SQL

What is meant by in-memory computation?
--> Spark brings data into the memory (Ram). As we know it takes less time to fetch and process data from the memory as it is fast.

The data that needs to be processed is very huge and the memory is limited. So how spark is able to process this data in memory?
--> Data partitioning: splits data into chunks and process these chunks in distributed fashion across different machines in a cluster
--> Lazy evaluation: doesn't immediately execute operations. stores execution plan in a DAG.
    DAG contains sequence of transformations. It gets executed once any action method is called.
--> Data pipelining
--> Using serialization: apache parquet
--> Out of memory processing: in case, where data exceeds the memory, spark intelligently spills the data into the disks.

What is DAG?
--> Directed acylic graph
--> Directed: graph is directed. operations are in a specific order/ sequence. Each operation depends upon the previos operation.
--> Acyclic: there are no loops or cycles in the sequence operations

What is meant by serialization?
--> serialization refers converting an object into byte stream.
--> serialized objects occupies less space in the disk/memory.
--> They can be used to transfer data over a network or store in memory.

What is apache parquet?
--> It is a storage file format
--> Columnar data structure: values of each column are stored together
--> Compression: it support compression algorithms like gzip, snappy and LZO.
--> Schema evolution:
    --> Parquet supports schema evaluation
    --> If the schema of data evolves over time, we do not require to process the data again, it adapts to the schema.
--> Predicate pushdown:
    --> Parquet supports predicate pushdown
    --> It pushes filtering operations down to the storage layer.
    --> In other words, it filters out data before processing.
    --> It tries to keep filtering conditions (conditions in WHERE clause) close to the data.
    --> Advantage:
	--> It allows parquet to skip reading rows or columns that do not satisfy the filter conditions
	--> Thus, improving query performance by reduction the amount of data that needs to be read from disk.
--> Not suitable for smaller data.

What is column pruning?
--> Optimization technique
--> Identifies and eliminates unnecessary columns
--> Also called as column elimination/ column projection

What is meant by distributed processing?
--> Spark jobs are executed in a cluster
--> This cluster contains combination of many machines
--> The job is divided into chunks and each of them is executed on sperate machines
--> This kind of processing is called as distributed processing.

What is a job?
--> A job can mean a set of instructions/code (transformations) written in python/scala that needs to be executed.

What is a cluster manager?
--> Responsible for managing available resources in a cluster
--> Manages the execution of tasks in a cluster of machines
--> Resources can be CPU, memory, storage and network
--> Also used to schedule jobs
--> Moniters health and status of the cluster, detects failures of nodes.
--> Reschedules tasks if failed.
--> Example: Mesos, kuberneted, Yarn
--> Apache Spark also has a standalone cluster manager

How spark provides fault tolerance?
--> By storing lineage, Spark can reconstruct lost data partitions by recomputing partitions.
--> Data replication: sparks replicate each partition on multiple nodes.
--> Task failure: if a task gets failed on a node, spark assigns the task to another worker node
--> Driver recovery: driver is the single point of failure in spark
--> Cluster managers: they have built in fault tolerance features.

What is cache and persist?
--> Both are methods used to persist frequently accessed data on memory or disk, providing faster access.
--> Cache is used to persist data in memory. It can also be written as persist(StorageLevel.MEMORY_ONLY)
--> Persist allows to choose between many storage levels:
   --> memory only
   --> memory and disk
   --> memory and disk ser
   --> disk only
--> use persist() to persist the data
--> use unpersist() to unpersist the data

What are the different storage levels?
--> memory only
    --> stores data as deserialized objects
    --> may result in data loss, if partitions are recomputed
--> memory and disk
    --> if data doesn't fit in the memory, it spills it into the disk.
    --> may take long time to fetch data, because it is stored in the disk
--> memory and disk ser
--> disk only
--> on heap
    --> memory allocated by the jvm
    --> managed by garbage collector
    --> provides faster access
    --> no need to manage storage
--> off heap
    --> storage allocated outside the jvm
    --> may be used, if the user want control over how the data is stored
    --> on heap storage can be limited, therefore it is efficient to store large data in off heap storage

What are the advantages of pyspark?
--> in memory, distributed processing
--> lightning fast
--> real time data processing

Explain spark architecture.
--> master-slave architecture
--> master - driver, slave - workers
--> spark context is the entry point to spark application
--> SparkContext resides on driver program
--> Cluster manager
    --> spark can work with various cluster managers:
	--> standalone mode: it's own cluster manager
	--> mesos
	--> yarn
	--> kubernetes
--> Driver:
    --> entry point of spark application
    --> runs on a dedicated node
    --> divides and assigns tasks to worker nodes.
--> Executors:
    --> runs the actual tasks
    --> excutes tasks in parallel, stores data in memory
    --> each executor runs in its jvm
    --> has certain amount of cpu cores allocated to it
--> A worker node can have multiple executors.

What is shuffle and data locality?
--> shuffle redistrivutes data accross partitions
--> occurs during aggregation or grouping
--> involves data movement
--> expensive process
--> shuffle operations can be optimized by using data locality, meaning data is processed on the same node where it is stored.

What are the components of spark eco-system?
--> Spark SQL
--> Spark Streaming
--> Spark MLlib
--> GraphX

Which languages supports spark?
--> python
--> R
--> java
--> scala
--> sql

What is checkpointing?
--> stores/persists intermediate rdds or dataframes to a reliable storage system.
--> fault tolerance
--> helps to recover in case of failure.
--> avoids recomputation of dataframes in case of failures.
--> lineage dependency: spark maintains a lineage, which includes series of transformations
--> the reliable system can be: s3, hdfs etc. 
--> Checkpointing incurs additional I/O overhead and storage requirements, so it should be used judiciously and selectively for RDDs or DataFrames that are expensive to recompute or have long lineages.
--> enable checkpointing: 
   --> (1) sparkContext.setCheckpointDir()
   --> (2) spark.conf.set("spark.sql.streaming.checkpointLocation","<checkpoint_directory>")
   --> (3) checkpointing on RDDs: RDD.checkpoint()
   --> (4) checkpointing on dataframes: DataFrame.writeStream().option("checkpointLocation","<checkpoint_directory>").start()

What is the difference between DAG and lineage?
--> DAG is a logical execution plan that represents the sequence of transformations and actions in a spark application
--> Lineage is the history or record of transformations applied to an RDD or dataframe.

Explain Spark UI.
--> A spark job is divided into something called stages.
--> Each stage contains some set of tasks.
--> Generally, stages are divided where there is shuffling of data.
--> Example:
   (1) Adding data --> stage 1
   (2) Collecting data --> stage 2
--> Spark Architecture
   (1) Driver
   (2) Worker nodes
   (3) Each worker nodes have executors or slots
   (4) A worker node can have multiple executors
   (5) A slot is nothing but a CPU.
   (6) Each of the component runs on a separate JVM.
--> A job represents a high-level unit of work triggered by an action operation.
--> A job is divided into stages based on data dependencies and presence of shuffle operations.
--> Stages are composed of tasks that can be executed in parallel, either without shuffling (narrow stages) or with data shuffling (wide stages).

List challenges in spark.
--> (1) Spill
    --> Spill occurs when data requires more memory than available and when the data exceeds memory, it gets spill on to the disk.
    --> Performance is reduced because I/O operations on disk are slower.
--> (2) Shuffle
    --> Redistributing data across partitions during groupBy, reduceByKey, join.
    --> Shuffle includes transfer of data over the network which can be a performance bottleneck.
--> (3) Storage
    --> Poor storage management can lead to excessive memory usage, spilling to disk, or inefficient utilization of storage resources.
--> (4) Serilization
    --> Process of converting data into suitable format for storage or transmission.
    --> Serialization can improve performance, memory usage, and network transfer efficiency.
--> (5) Skew
    --> Refers to the imbalanced distribution across the partitions.
    --> Occurs during aggregations or joins
    --> Can impact performance
--> Solutions:
    --> Optimizing data partitioning and repartitioning to reduce skewness
    --> Adjusting spark configurations related to memory management, shuffle behavior, serialization, and strage levels.
    --> Using broadcast joins, bucketing etc.
    --> Using techniques like caching, checkpointing etc.

List some of the important spark configurations.
--> (1) Spark driver and executor memory:
    --> spark.driver.memory and spark.executor.memory
    --> Ensure sufficient memory to execute spark jobs.
--> (2) Number of executors and executor cores:
    --> spark.executor.instances
    --> spark.executor.cores
--> (3) Spark driver and executor memory overhead:
    --> spark.driver.memoryOverhead and spark.executor.memoryOverhead
--> (4) Spark task parallelism:
    --> spark.default.parallelism, spark.sql.shuffle.partitions and spark.sql.files.maxPartitionBytes
--> (5) Spark caching and storage levels:
    --> spark.storage.memoryFraction, spark.storage.level
--> (6) Serialization:
    --> spark.serializer
--> (7) Resource Management:
    --> spark.dynamicAllocation.enabled, spark.shuffle.service.enabled

Difference between repartition and coalesce.
--> Repartition shuffles data across the cluster.
--> Repartition can increase or  decrease number of partitions, involves a full shuffle, and ensures a balanced distribution.
--> Coalescing can only decrease the number of partitions, minimizes data movement.

How the number of partitions are decided in spark?
--> (1) Input data source
--> (2) Default parallelism: spark.default.parallelism
--> (3) Transformation operations: repartition and coalesce
--> (4) Custom partitioning: partitionBy

How many partitions?
--> Default properties:
    --> spark.default.parallelism (default: Total no. of CPU cores)
    --> spark.sql.files.maxPartitionBytes (default: 128 MB)
    --> spark.sql.files.openCostInBytes (default: 4 MB)
--> Number of partition calculation:
    --> total_bytes = Sum of sizes of all data files + (No. of files * openCostInBytes)
    --> bytesPerCore = total_bytes / default.parallelism
    --> maxSplitBytes = Minimum(maxPartitionBytes, Maximum(bytesPerCore, openCostInBytes))
    --> num_partitions = total_bytes / maxSplitBytes
--> Example​:
	2 csv files of 62 MB each​
	total_bytes = (2 * 62 * 1024 * 1024) + (2 * 4 * 1024 * 1024) = 138412032​
	bytesPerCore = 138412032 / 7 = 19773147​
	maxSplitBytes = Minimum(134217728, Maximum(19,773,147, 4,194,304)) = 19773147​
	num_partitions = 138412032 / 19773147 = 6.49

Explain broadcast join.
--> Used when size of one dataset is smaller than the other.
--> Used to optimize the join process.
Steps:
--> One dataset, known as broadcast or small dataset is small enought to firt entirely in memory.
--> The other dataset, known as non-broadcast or large dataset is comparatively larger in size.
--> Smaller dataset is broadcasted or replicated to all worker nodes.
--> Each worker node performs the join locally by combining the local copy of small dataset with corresponding partition of the large dataset.
--> Results from each worker node are combined to produce the final result of the join operation.
Advantages:
--> Reduced data shuffling
--> Local join excecution

Difference between bucketing and partitionBy.
--> Partitioning divides data into separate directories or files based on specific column values, while bucketing distributes data into fixed-size buckets based on a hash function applied to specific column(s).
--> Partitioning enables data skipping at the partition level during query execution, reducing the amount of data processed. Bucketing does not provide data skipping but helps in even data distribution for join operations or reducing data skew.
--> Partitioning creates separate directories or files for each partition, while bucketing stores buckets as separate files within a partition directory.

What is dynamic pruning?
--> Dynamic pruning is an optimization technique used in query processing to selectively skip unnecessary data based on runtime information and query predicates. It aims to reduce the amount of data accessed and processed during query execution, leading to improved performance. Apache Spark supports dynamic pruning through various mechanisms, including predicate pushdown and filter pushdown optimizations.

What is adaptive query execution?
--> Adaptive Query Execution (AQE) is a feature introduced in Apache Spark 3.0 to optimize query execution dynamically based on runtime feedback and data statistics. AQE aims to improve the performance and resource utilization of Spark applications by adapting the execution plan and making data-driven decisions during query processing. 
