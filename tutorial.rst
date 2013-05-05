.. _tutorial-index:

########################
The Berkeley DB Tutorial
########################

This tutorial was adapted from Berkeley DB's Refernce Guide.

The Berkeley DB library is an open source embeded database library which provides a scalable, high-performance, transaction protection services to applictaions.
Berkeley DB is scalable in a number of respects. The database library itself is quite compact (under 300 kilobytes of text space on common architectures), but it can manage databases up to 256 terabytes in size. It also supports high concurrency, with thousands of users operating on the same database at the same time. Berkeley DB is small enough to run in tightly constrained embedded systems, but can take advantage of gigabytes of memory and terabytes of disk on high-end server machines.

Berkeley DB generally outperforms relational and object-oriented database systems in embedded applications for a couple of reasons. First, because the library runs in the same address space, no inter-process communication is required for database operations. The cost of communicating between processes on a single machine, or among machines on a network, is much higher than the cost of making a function call. Second, because Berkeley DB uses a simple function-call interface for all operations, there is no query language to parse, and no execution plan to produce. 

It should be noted that Berkeley is not a relational database, not does it suppport SQL queries. Nor does it implicitly maintain structure or records in the table like other relation databases might.

Data Access Services
""""""""""""""""""""

Berkeley DB supports hash tables, Btrees, simple record-number-based storage, and persistent queues. Programmers can create and mix operations on different kinds of tables in a single application.

Hash tables are generally good for very large databases that need predictable search and update times for random-access records. Hash tables allow users to ask, "Does this key exist?" or to fetch a record with a known key. Hash tables do not allow users to ask for records with keys that are close to a known key.

Btrees are better for range-based searches, as when the application needs to find all records with keys between some starting and ending value. Btrees also do a better job of exploiting locality of reference. If the application is likely to touch keys near each other at the same time, the Btrees work well. The tree structure keeps keys that are close together near one another in storage, so fetching nearby values usually doesn't require a disk access.

Record-number-based storage is natural for applications that need to store and fetch records, but that do not have a simple way to generate keys of their own. In a record number table, the record number is the key for the record. Berkeley DB will generate these record numbers automatically.

Queues are well-suited for applications that create records, and then must deal with those records in creation order. A good example is on-line purchasing systems. Orders can enter the system at any time, but should generally be filled in the order in which they were placed. 

.. toctree::
	about-tut
	basic-concepts
	opening-database
	adding-elements
	retrieve-elements
	deleting-elements
	database-application

