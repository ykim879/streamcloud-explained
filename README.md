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
 
