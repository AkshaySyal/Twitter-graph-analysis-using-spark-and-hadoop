# Twitter-graph-analysis-using-spark-and-hadoop
This project involved implementing and comparing Hadoop and Spark programs for calculating exact and approximate number of 2-hop paths and social-triangles (3-hop cycles) on a Twitter graph, utilizing methods like global counters, RS-join, and Rep-join, and analyzing their performance and scalability on local and AWS EMR environments.
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
