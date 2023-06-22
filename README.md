# DB-Multitenet-Eviction-Challenge-Algorithm

A database is a warehouse where data is organized, stored, and managed by its underlying data structures. Everyone uses some kind of a database.

Some cloud service providers decide to build single instances of database management systems which are shared by multiple customers and isolated among different users. This is the concept of database multi-tenancy. A single database instance is divided into multiple virtual sub-databases, serving different tenants. With the database multi-tenant technology, cloud service providers can efficiently integrate resources and greatly reduce service costs.

To speed up data access, data is split into pages, and some pages are loaded from slow storage, like a hard disk, into a faster memory buffer. This is the concept of a database buffer. When the amount of data in the buffer reaches the capacity, some data pages get evicted by the replacement algorithm so that new ones can be loaded from the disk. This challenge is to implement sharing and isolation mechanisms for database buffers in a multi-tenant environment.

The database can be abstracted as a set of pages with a fixed length, where a page is defined as the basic unit for reading data into the buffer. The access to the database is abstracted as a sequence of 𝑀 operations: 𝑆=𝐴1,𝐴2,…,𝐴𝑀 where 𝑀 is a given integer. Each access 𝐴𝑖 in the sequence is called an operation. In multi-tenant environment, operations may come from different tenants. Therefore, an operation 𝐴𝑖 consists of a tenant ID (subject) 𝑈𝑖 and a page (object) 𝑃𝑖. The tenants are numbered by integers from 1 to a given integer 𝑁. Pages for different tenants are local to these tenants. In other words, page 1 of tenant 𝑥 is not relevant to page 1 of tenant 𝑦.

The core of buffer sharing is the replacement algorithm (also called the cache eviction algorithm). For an operation sequence 𝑆, if the requested page 𝑃𝑖 already exists in the buffer, it is called a page hit. Otherwise, it is called a page fault. When a page fault occurs, the cache eviction algorithm is used to select a memory page 𝐶 in the buffer, evict it and then store the new page 𝑃𝑖 in its slot. Typical cache eviction algorithms include Least Recently Used (LRU), Least Frequently Used (LFU), and Segmented LRU (SLRU).

In single-tenant environment, each tenant 𝑡 independently uses a memory block of a fixed size 𝑄𝑡. While in multi-tenant environment, all tenants share a large memory space with total size 𝑄. The memory size used by each tenant is dynamically adjusted to perform page swap-in and swap-out. During a cache eviction, please ensure that the memory size being used by each tenant is within the specified range, that is, 𝑄min𝑡≤𝑄𝑡≤𝑄max𝑡. Specifically, when tenant 𝑥 has 𝑄min𝑥 or less pages in memory, the pages of other tenants cannot replace pages of tenant 𝑥. Conversely, when tenant 𝑦 has 𝑄max𝑦 in memory, the pages of tenant 𝑦 cannot replace pages of other tenants. For tenant 𝑧, the algorithm can evict an existing page of that tenant and replace it with a new page of the same tenant, but only when that tenant has between 𝑄min𝑧
 and 𝑄max𝑧 pages in memory, inclusive. Contestants need to maximize the overall tenants' access experience with limited memory space.

Initially, each page in the buffer does not belong to any of the tenants. Note that evicting these initial pages still counts as evicting some other tenant.

Design a replacement algorithm that is applicable to a multi-tenant environment. For each operation 𝐴𝑖, output a buffer location 𝐶𝑖 that is either the existing or the newly assigned caching location for page 𝑃𝑖 of tenant 𝑈𝑖.

Input
The first line contains three integers 𝑁, 𝑄, and 𝑀. They respectively represent the number of tenants in the system (1≤𝑁≤10), the total buffer size (1≤𝑄≤1000000), and the length of the operation sequence 𝑆 (1≤𝑀≤1000000).

The second line contains 𝑁 integers 𝐿1,𝐿2,…,𝐿𝑁, where 𝐿𝑡 represents the priority level of tenant 𝑡 (1≤𝐿𝑡≤10).

The third line contains 𝑁 integers 𝐷1,𝐷2,…,𝐷𝑁, where 𝐷𝑡 represents the database size of tenant 𝑡 (1≤𝐷𝑡≤100000).

The fourth line contains 3⋅𝑁 integers, which are 𝑁 triplets (𝑄min𝑡,𝑄base𝑡,𝑄max𝑡). They respectively represent the minimum buffer size, the base buffer size, and the maximum buffer size of tenant 𝑡 (1≤𝑄min𝑡≤𝑄max𝑡≤100000). The values 𝑄base𝑡 are used to calculate the score (1≤𝑄𝑏𝑎𝑠𝑒𝑡≤100000). It is guaranteed that ∑𝑁𝑗=1𝑄min𝑗 is less than or equal to 𝑄.

Interaction
The scheduling algorithm must be real-time. It means the solution must print the answer to 𝑖-th operation before it gets the line with (𝑖+1)-th operation.

So, after your program reads the initial part of the input, it needs to interact with the judger 𝑀 times. 
For each interaction, the judger will provide a single line as an input for your program. This line contains two integers which represent the pair 𝐴𝑖=(𝑈𝑖,𝑃𝑖)
of the operation sequence (1≤𝑈𝑖≤𝑁, 1≤𝑃𝑖≤𝐷𝑈𝑖). For each of the 𝑀 operations 𝐴𝑖, print a line containing a single integer 𝐶𝑖: the buffer location accessed by that operation (1≤𝐶𝑖≤𝑄).

For correct interaction, print the end-of-line after each command and flush the output buffer with the respective functions of your programming language:

cout.flush() or fflush(stdout) for C/C++;
stdout.flush() for Python;
System.out.flush() in Java;
see documentation for other languages.
Otherwise, your solution will get the "Idleness Limit Exceeded" outcome.

Scoring
There are 𝑇 tests in this problem.

For each of them, the online judge checks whether a page fault occurs in each operation 𝐴𝑖
 based on the sequence 𝐶𝑖 provided by the contestant. To ensure that the experiences of tenants with different database sizes are balanced, the concept of SLArate is introduced. Here, SLA stands for Service Level Agreement which is the quality of service expected by the tenants. For tenant 𝑡, the SLArate𝑡 can be expressed as follows:
SLArate𝑡=max(SLAactual𝑡,SLAbase𝑡)−SLAbase𝑡SLAbase𝑡 The value SLAactual𝑡 indicates the actual number of page faults for tenant 𝑡 in this test based on the contestant's replacement algorithm. The value SLAbase𝑡 indicates the number of page faults obtained by using the LRU algorithm when the buffer size of tenant 𝑡 is 𝑄base𝑡. It is guaranteed that SLAbase𝑡 is a positive integer.

The contestant's intermediate score Cost𝑗 in test 𝑗 can be expressed as follows:

Cost𝑗=∑𝑖=1𝑁𝑓1(SLArate𝑖)⋅𝐿𝑖 and 𝑓1 is expressed as follows:

𝑓1(𝑥)=3𝑥2
The total score of a contestant can be expressed as follows:
Score = ∑𝑗=1𝑇𝑓2(Cost𝑗Costbase𝑗)
and 𝑓2 is expressed as follows:

𝑓2(𝑥)=100⋅max(0,5−𝑥)
In order to balance the players' results on different test data, we calculate Costbase
 of each data set in advance, and then score the contestant's cost according to Costbase
 and get the Score
. We have implemented several baseline algorithms and, among them, calculated the minimum non-zero value Costbase
 for each test 𝑗
.

There are two sets of tests in this problem. For the duration of the competition, each submission is tested on the preliminary set of tests. When the competition is finished, for each contestant:

the jury takes the latest submission with non-zero score on preliminary tests;
this submission is tested on the final set of tests;
the score on final tests is the final score for the contestant.
The contestants are then ranked according to their final score.

The final tests are similar, but not identical, to the preliminary tests. The number of final tests may be larger, but their distribution is similar to the distribution of preliminary tests.

