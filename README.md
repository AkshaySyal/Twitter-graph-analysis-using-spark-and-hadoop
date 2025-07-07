# Twitter-graph-analysis-using-spark-and-hadoop
The first project involved implementing and comparing Hadoop programs for calculating exact and approximate number of 2-hop paths and social-triangles (3-hop cycles) on a Twitter graph, utilizing methods like global counters, RS-join, and Rep-join, and analyzing their performance and scalability on local and AWS EMR environments.
- **Exact Length-2 Path Count**:
  - Used a single MapReduce job.
  - For each node, calculated product of in-degree and out-degree.
  - Aggregated using a global counter.
  - Resulted in **953,138,453,592 paths** on the full Twitter dataset.

- **Approximate Length-2 Path Count**:
  - Used RS-join algorithm with a **MAX filter** to limit dataset size.
  - Approximation trend based on increasing MAX values did **not converge** to the exact count.
  - Insight: MAX filter caused non-linear "extreme step function" behavior due to arbitrary user ID filtering.

- **Triangle Counting**:
  - Two MapReduce join strategies:
    - **RS-join**: Multiple jobs
    - **Rep-join**: Single job using Hadoop’s File Cache to load one dataset in memory
  - Both methods used the MAX filter to control job cost.
  - Performance evaluated on **AWS EMR clusters** of various sizes (running time, speedup ratio, scalability).

- **Conclusion**:
  - Exact cardinality computations are feasible with global counters.
  - Approximate methods may be unreliable depending on filter design.
  - Rep-join is more efficient for triangle counting with memory optimization.

The second project involved implementing and analyzing Spark programs for Twitter follower count and graph triangle counting, investigating aggregation efficiency before shuffling and the impact of MAX filters on performance.<br>
- **Follower Counting**:
  - Developed 5 Spark Scala programs:
    - `RDD-G` (uses `groupByKey`)
    - `RDD-R` (uses `reduceByKey`)
    - `RDD-F` (uses `foldByKey`)
    - `RDD-A` (uses `aggregateByKey`)
    - `DSET` (uses DataFrame `groupBy().count()`)
  - Key Insight:
    - All except `RDD-G` perform **local aggregation within partitions** before shuffling.
    - Mimics **MapReduce Combiner** behavior for optimization.
    - `groupByKey` leads to higher network overhead due to lack of pre-aggregation.

- **Triangle Counting**:
  - Developed 4 programs using both RDD and DataFrame APIs:
    - `RS-R` and `RS-D`: RS-join with RDD and DataFrame
    - `Rep-R` and `Rep-D`: Rep-join with RDD and DataFrame
  - All programs used a consistent **MAX_FILTER = 10,000** to limit dataset size.
  - Output triangle count: **520,296**
  - Evaluated on **AWS EMR clusters** with 4 and 8 worker nodes to compare performance.

- **Conclusion**:
  - Local aggregation via `reduceByKey`, `foldByKey`, `aggregateByKey`, and DataFrames enhances performance by reducing shuffle cost.
  - Triangle counting methods yielded consistent results across APIs.
  - Performance scales with cluster size, providing insight into Spark's parallel efficiency.
