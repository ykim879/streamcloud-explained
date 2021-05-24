# StreamCloud: An Elastic and Scalable Data Streaming System
This respository explained streamcloud by referencing the paper, StreamCloud: An Elastic and Scalable Data Streaming System.
## SPE (Stream Processing Englines)
SPEs are computing systems designed to process continuous streams of data with minimal delay.
**Property:** Data streams are not stored but are rather processed on-the-fly using continuous queries.
-  continuous query: query that is constantly “standing” over the streaming tuples and results are **continuously outputting**.
**Challenge:** attaining higher scalability and avoiding single-node bottlenecks  in architecting a parallel-distributed SPEwith intraoperator parallelism.
- Problems:
  1.  Parallelization should be syntactically and semantically transparent.
    - **Syntactically transparent**: User's regualary query automatically parallized by the system.
    - **Semantic transparent**: The result of the parallel system should be identical to non-parallelized results.
  2. SPE should be elaxitc and adjust the amount of its resources by combining with dynamic load.
    - The applications often faced dynamic changes causing dramatic underprovisioning and overprovisioning.
 ## Data Stream Processing
Data Stream Processing is a novel computing paradigm for massive amount of data without causing long delay.
- **Tuples**: All tuples have time stamp attribute with well synchronized clocks. However when the clock synchronization is not feasible, it can be done by time stamping at the entry point
- **Queries**: Defined as continuous because it is standing over the streaing data. It processed as a direct acyclic graph where each node is an operator and edge is a data flow. 
  - **Query operators**:
    - *Stateless*(Map, Union, and Filter): do not keep state across tuples and perform their computation **solely based on each input tuple**. 
    - *Stateful*(Aggregate, Join, and Cartesian Product): do keep state across tuples. 
      - perform operations on sequences of tuples by sliding windoes of tuples over a period of time.
 
## Parallel Data Streaming
### Parallelization Strategies depending on granulaity of the parallization unit
![Parallelization Strategies depending on granulaity of the parallization unit](https://user-images.githubusercontent.com/59812671/119214625-220eb680-ba7d-11eb-9bea-7c61aab4b806.png)

**Query-cloud strategy:** The parralization unit = the whole query (minimize the number of hops)
- Semantic transpanency: attained by redistributing tuples just *before each stateful operators*
- The number of hops of each tuple = the number of stateful operators ( since redistributing tuples happened just before the stateful operators)
- The number of fanouts of each node = number of nodes - 1

**Operator-cloud strategy:** The parralization unit = single operator (minimize the number of fanout)
- Semantic transpanency: communication between each node of one subcluster and the next subcluster (each operator is deployed by different subclusters)
- The number of hops of each tuple = the number of operators - 1 ( since redistributing happened each time of the opertors)
- The number of fanouts of each node = size of the following subclusters

**Operator-set-cloud strategy:** Trade of between two strategies (best balance between fanout and the hops)
- Semantic transpanency: attained by redistributing tuples just before each stateful operators
- The number of hops of each tuple = the number of stateful operators
- The number of fanouts of each node = size of the following subclusters

**SC employs third strategy because it has the best balance between the hops and the fanouts**

## Query Parallelization in StreamCloud
### Tuple Distribution on each hop
Each redistribution happens before the stateful operators. The subcluster before the redistribution is called **upstream** and after the redistribution is called **downstream**.


**The process of redistribution**:


![query parallelization](https://user-images.githubusercontent.com/59812671/119295395-99725080-bc0b-11eb-8a2e-1579965acdb7.png)

1. The output of the tuples of each subcluster are assigned to buckets.
  - Tuple-buckets assignment is computed by hashing one or more of tuples' attributes modulus the number of buckets.
2. Buckets are forwared to the same downstream instance by operator, *Load Balancer*
  - Load Balancer takes charge of Tuple-buckets assignment and bucket-instance map (BIM).
  - BIM[b] = A, where b is a bucket and A is an instance, is equals to A owning the bucket b. A is a downstream instance that receives all tuples of bucket b. 
3. On the ingoing edge, *Input Merger(IM)* takes input streams and feed the local subquery with a single merged stream
