Project 5 - An Exploration of SQLite & NumPy

Anudeep Kaur Chawla, Ankita Budhraja & Paul Kenniston
(Writeup by Paul Kenniston)

Systems Evaluated & Motivation for Comparison:

For this project, we chose to explore the query performance of SQLite in comparison to NumPy. We chose these two systems because, despite serving different purposes for data storage and serving different utility in industry, they both support the same simple data manipulation and query operations. As a result, we can compare the efficacy of these operations in these two systems for small-scale databases, and gain some insight as to when one system would be preferred over another. Specifically, we will be evaluating the memory usage and time spent when executing each query. In the future, it may be worth also including a qualitative ‘ease of use’ analysis in our comparison; but, given the simplicity of these small-scale queries, we chose to omit ‘ease-of-use’ from our comparison in this project.

Background for the Systems in Scope:

Providing a high-level background for these systems may be beneficial in understanding the output of our queries. Regarding use cases, typically NumPy is primarily used for numerical computations and array operations, whereas SQLite is a relational database management system (RDBMS) designed for storing and querying structured data.

NumPy and SQLite also have different underlying architectures and optimizations. NumPy operates mainly in memory and is optimized for numerical operations on large arrays, while SQLite stores data on disk and is optimized for querying structured data using SQL.

This difference in architecture also affects scalability, although this difference won’t be made manifest in the smaller databases that we are testing for this project. NumPy's performance may degrade when handling extremely large datasets that exceed available memory, whereas SQLite's performance may degrade as the size of the database grows due to disk I/O constraints.

How we Set Up this Comparison:

In order to set up these two systems and compare them as directly as possible (while also collaborating with each other remotely in a shared environment), we chose to create a new Google Colab instance to house our data and support the execution of our queries. We initiated our Colab instance using Python for its native support with NumPy; to do this we initialized our notebook by importing ‘pandas’. We then enabled querying with SQL code by importing the package ‘sqlite3’.

Of course, we not only needed to write the code for queries, we had to evaluate their performance as well. To do this, we imported the ‘time’ package while initializing the notebook in order to capture the time spent on each query execution. We also used ‘process.memory_info’ to measure memory usage.


Figure 1: Code used to calculate performance


Dataset Used:

In choosing a dataset to be used for evaluation, we had several criteria in mind:

Dataset should include a variety of data types (more than 8)
Dataset should have a sufficient number of rows to evaluate performance (more than 1,000)
Dataset should include numeric and string values
Dataset should contain some null values (nice to have)

These criteria ensured that the queries that we ran, and the data manipulation that we did prior to queries, were complex enough so that when we conducted a performance analysis we would receive non-zero values.

After reviewing some options on Kaggle, we found that the oft-used ‘Wine Quality’ dataset uploaded to Kaggle by M Yasser H met our criteria. We decided to move forward with this dataset, and loaded it to our Colab instance.


Query Workload:

We ran several queries to evaluate the performance of these two systems. A quick description of each task is given below, as well as the results of their performance analysis.

Generally, when there is a difference in performance between these two systems, the delta is minimal at best. This is to be expected when querying a relatively small database. However, it is not difficult to imagine how even small costs like the ones shown in our results can quickly become cumbersome with larger datasets or more frequent queries.

Task 1: Loading Data

The first step of any database management operation. While this task wouldn’t be performed often in vivo, it is still worth evaluating how effective these systems are at spinning up a new database from scratch. 

This task had one of the biggest differences in both time and memory between the two systems. NumPy took approximately ¼ of a second more than SQLite to create this database, but utilized 2.5 MB less of memory. We generally expected similar results, given that NumPy is designed around numerical calculations and array operations that provide more efficient memory structures, whereas SQLite has better caching and disk-related management used for spinning up structured data.



NumPy
SQLite
Temporal Cost
0.307 seconds
0.071 seconds
Memory Cost
1.805 MB
4.246 MB



Task 2: Querying Length of Database

Next, we tried a very simple single-dimensional query - querying the total length of the database. Our results here show that NumPy performed this instantaneously, but SQLite took a minimal (but observable) amount of time at 0.004 seconds. This generally makes sense, as it is unlikely for SQLite to have any 0.0 second operations given the on-disk nature of its calculations.



NumPy
SQLite
Temporal Cost
0.0 seconds
0.004 seconds
Memory Cost
0.0 MB
0.0 MB



Task 3: Filtering Data


Here we began to put these systems to its paces. We filtered the data along several datatypes using numerical thresholds. Below is a summary of the datatypes we used, and the results that we intended to receive:

Volatile_acidity < 0.7
Alcohol > 10
Quality >= 5

As expected, the memory cost was 0MB for both as we are not actually writing any changes. This task was clearly slightly more strenuous on both systems given that NumPy registered as a non-zero amount of execution time.



NumPy
SQLite
Temporal Cost
0.002 seconds
0.005 seconds
Memory Cost
0.0 MB
0.0 MB



Task 4: Creating a Dataset

Next, we tasked these systems with creating a new dataset using existing data that was already loaded (size of new database: 1000, 1). Here, we see a substantial difference between these two systems in both temporal cost and memory cost. Here, we can start to pull apart some of the nuances between these two systems. Since NumPy operates by creating data structures for efficient array processing, it boasts a much quicker execution time. However, creating arrays in this way comes at a significant cost to memory. SQLite, on the other hand, was able to achieve the same operation without incurring any memory cost given that it is writing these data structures directly to disk. 



Figure 2: Code used to spin off a new dataset




NumPy
SQLite
Temporal Cost
0.048 seconds
3.986 seconds
Memory Cost
113.43 MB
0.0 MB




Task 5: Sorting

Next, we conducted a sorting task on multiple parameters. One parameter is taken directly from the database, the other is a derived datatype. Below is a summary of this task:

Select: ‘quality’, AVG(‘alcohol’)
Group by: ‘quality’



NumPy
SQLite
Temporal Cost
0.002 seconds
0.005 seconds
Memory Cost
0.0 MB
0.0 MB




Task 6: Data Aggregation

Finally, we queried an aggregation of multiple datatypes. Each datatype that was returned was a derived datatype that we did some manipulation to before returning.

AVG(alcohol)
MAX(alcohol)
MIN(alcohol)
AVG(density)
MAX(total sulfur dioxide)
MIN(pH)




NumPy
SQLite
Temporal Cost
0.005 seconds
0.003 seconds
Memory Cost
0.0 MB
0.0 MB





Analysis of Usability and Performance & Ideal Future Use Cases:

Based on our prior understanding of these two systems, and the results shown in our performance analysis, we arrived at a few general conclusions about the usability and efficacy when querying these two systems.

NumPy is typically better suited for scenarios requiring high-performance numerical computations and array operations. It seems like it would be best for operations in which speed and efficiency are the most critical concerns. This includes machine learning tasks, ad-hoc data analysis tasks, and perhaps also data visualization tasks. Whereas, it may not be as suited for longer-term scenarios in which data storage, relational querying, and memory play bigger roles. To that end, we would recommend that NumPy be used for smaller ad-hoc tasks that involve a lot of numeric manipulation - ideally tasks that don’t involve any concurrent operations.
SQLite, however, is better suited for scenarios for self-contained, medium-to-large databases where data persistence and reliability are important. It seems to perform well with read-heavy workloads, and although not evaluated in this project, scenarios in which multiple activities need to be executed concurrently. To that end, we would recommend that SQLite be used for larger databases in which memory is a bigger concern and there are a lot of tasks that require read-operations.

How do these Systems Compare to Document-based Data Storage?

Of course, these two systems don’t represent the entirety of solutions available for data storage. One model not discussed yet, which we feel is worth acknowledging for the sake of comparison here, are document-based storage solutions such as MongoDB. MongoDB is a NoSQL database that uses a document-oriented data model that keeps data in a JSON-like format, allowing for flexible querying.

The document-based model allows for some affordances that NumPy and SQLite cannot match as effectively. Unlike the two previously mentioned systems, MongoDB excels at both semi-structured and unstructured data processing - this enables it to be used for multi-modal platforms that collect various different kinds of data. For example, a MongoDB instance might be a great fit for a web application that receives all different kinds of data structures (e.g. user profiles, ecommerce, advertising). MongoDB is also perhaps the most scalable of the three systems referenced, as it is effective at replicating data for multiple users and ‘sharding’ different instances to enable concurrent usage. This makes it a great choice for very high volume datasets.


References:

https://www.kaggle.com/datasets/yasserh/wine-quality-dataset
https://en.wikipedia.org/wiki/MongoDB
https://www.techtarget.com/searchdatamanagement/definition/MongoDB
