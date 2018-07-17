<a href = "https://www.dataquest.io/home">DATAQUEST</a>'s Become a Data Engineer: Handling Large Data Sets In Python
------
Here's how to get DataQuest's Data Engineering Track missions' content to work on your localhost.
Using data from my <a href = "https://github.com/nmolivo/valencia-data-projects/tree/master/valenbisi">Valenbisi ARIMA modeling project</a>, I will walk through steps using PostgreSQL, Postico, and the Command Line to get our DataQuest exercises running out of a Jupyter Notebook. 

This will not be a complete repitition of the many resources I used, so be sure to look out for any links I include if it seems I've skipped a few steps.

Important notes: 
1. In DataQuest, each exercise re-initiates the connection and cursor class of `psycopg2` when interacting with the Postgres DB, with no deliberate closing of the connection. When we productionize our scripts, it will be more efficient and correct to use a `with` statement, which will close the connection once the operations are complete. For the sake of the exercises, I will follow DataQuest's format. I will switch to the `with` statement as we approach production.<br>
2. If there is something that I do not wish to perform on my database, I still complete the exercises, but just leave the code as markdown, rather than executing it. DataQuest does a great job of forshadowing future concepts in earlier lessons, so in cases like these, I may conquer the task in the earlier lesson rather than the later. For example, in mission 2, about creating tables, I use SQLAlchemy to write to a table using a Pandas DataFrame object. Because I've already completed my desired operation in Mission 2, I do not re-execute code which would re-do the task when it is covered in more detail in Mission 4.
3. All projects will contain fully executable code.

This directory will contain the following:
 * Processing Large Datasets in Pandas
    * Optimizing Dataframe Memory Footprint
    * Processing Dataframes in Chunks
    * <b>Guided Project: Practice Optimizing Dataframes and Processing in Chunks</b>
    * Augmenting Pandas with SQLite
    * <b>Guided Project: Analyzing Startup Fundraising Deals from Crunchbase</b>
  * Optimizing Code Performance on Large Datasets
    * CPU Bound Programs
    * I/O Bound Programs
    * Overcoming the Limitations of Threads
    * Quickly Analyzing Data with Parallel Processing
    * <b>Guided Project: Analyzing Wikipedia Pages</b>
  * Algorithms and Data Structures
    * Processing Tasks with Stacks and Queues
    * Effectively Using Arrays and Lists
    * Sorting Arrays and Lists
    * Searching Arrays and Lists
    * Hash Tables
    * <b>Guided Project: Analyzing Stock Prices</b>
  * Recursion and Trees
    * Overview of Recursion
    * Introduction to Binary Trees
    * Implementing a Binary Heap
    * Working with Binary Search Trees
    * Performance Boosts of Using a B-Tree
    * Performance Boosts of Using a B-Tree II
    * <b>Guided Project: Implementing a Key-Value Database</b>
    
# Processing Large Datasets in Pandas

### Optimizing Dataframe Memory Footprint:
------
Pandas groups the columns into blocks of values of the same type.

Use `DataFrame._data` private attribute to return the column and row axes, as well as the individual Block instance for each unique type in the dataframe.

Force pandas to inspect the memory for each linked string value and return the true memory footprint by setting the `memory_usage` parameter to `"deep"` when calling `DataFrame.info()`.



### Processing Dataframes in Chunks:
------

### Guided Project: Practice Optimizing Dataframes and Processing in Chunks:
------

### Augmenting Pandas with SQLite:
------

### Guided Project: Analyzing Startup Fundraising Deals from Crunchbase:
------

# Optimizing Code Performance on Large Datasets

### CPU Bound Programs:
------

### I/O Bound Programs:
------

### Overcoming the Limitations of Threads:
------

### Quickly Analyzing Data with Parallel Processing:
------

### Guided Project: Analyzing Wikipedia Pages:
------

# Algorithms and Data Structures

### Processing Tasks with Stacks and Queues:
------

### Effectively Using Arrays and Lists:
------

### Sorting Arrays and Lists:
------

### Searching Arrays and Lists:
------

### Hash Tables:
------

### Guided Project: Analyzing Stock Prices:
------

# Recursion and Trees

### Overview of Recursion:
------

### Introduction to Binary Trees:
------

### Implementing a Binary Heap:
------

### Working with Binary Search Trees:
------

### Performance Boosts of Using a B-Tree:
------

### Performance Boosts of Using a B-Tree II:
------

### Guided Project: Implementing a Key-Value Database:
------


For Non-Commercial Use Only
------
I highly reccommend participating in this course as a member of DATAQUEST.
