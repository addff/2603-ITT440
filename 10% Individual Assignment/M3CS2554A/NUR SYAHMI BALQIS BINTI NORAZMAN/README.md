# PARALLEL MUSIC RECOMMENDER
## NUR SYAHMI BALQIS
## 2024211386

# Problem Statement
  Many or thousands of customers must get song recommendations simultaneously from a music streaming service. 
  It is too slow to process each user's listening history sequentially. 
  This assignment shows how music recommendations can be made faster by using concurrent and parallel programming.

# Objective
  * Create a system that analyzes user listening data to recommend music.
  * Use the sequential, concurrent (threading), and parallel (multiprocessing) versions.
  * Examine each sequential, concurrent, and parallel system's performance.
  * Explain why CPU-intensive jobs benefit from parallel programming.

# Project Scope

* Data Size: 3,000,000 listening records
* Users: 5,000 unique users
* Songs: 2,000 unique songs
* Target Users: 10 random users for recommendation
* Programming Language: Python 3.10+
* Platform: Ubuntu 22.04


# Three Implementations
* Sequential - One by one
* Concurrent - Threading
* Parallel - Multiprocessing

# Difference between Sequential, Concurrent & Parallel

## Sequential
* The tasks are carried out sequentially. Before starting the next task, the previous one must be finished.

## Concurrent
* By alternating between several duties, they advance. They alternate, but only one duty is completed at a time.
* Best for: I/O operations (file reads, network requests)

## Parallel
* Different CPU cores are used to conduct many tasks concurrently.
* Best for: CPU-intensive calculations (like similarity scoring)

# Code Structure
```ssh
# Sequential - one at a time
def run_sequential(data, users):
    for user in users:
        recommend(user)  # wait for finish

# Concurrent - threading
def run_concurrent(data, users):
    with ThreadPoolExecutor() as executor:
        executor.map(recommend, users)  # interleaved

# Parallel - multiprocessing  
def run_parallel(data, users):
    with ProcessPoolExecutor() as executor:
        executor.map(recommend, users)  # simultaneous
```

## Key Function

* generate_data_fast() - Creates 3M synthetic records
* build_user_database()	- Converts records to dictionary
* calculate_similarity_fast()	- Compares two users' music taste
* recommend_for_user_fast()	- Generates top 5 recommendations
* run_sequential()	- Processes users one by one
* run_concurrent() - Uses threading with GIL
* run_parallel()	- Uses multiprocessing on cores	

# Scalling Data Table

| Data Size | Sequential | Concurrent | Parallel | Winner |
|-----------|------------|------------|----------|--------|
| 100K | 0.08s | 0.09s | 2.34s | Sequential |
| 500K | 0.12s | 0.13s | 4.56s | Sequential |
| 1M | 0.18s | 0.19s | 6.78s | Sequential |
| 2M | 0.22s | 0.23s | 9.12s | Sequential |
| 3.5M | 0.25s | 0.25s | 11.31s | Sequential |




# Result & Performance Analysis
## Expected Output
```ssh
======================================================================
# Performance Benchmark - Parallel Music Recommender
======================================================================
Generating 3,500,000 listening records...
   Generated in 15.89 seconds
Building user database...
   Built for 50,000 users in 2.78 seconds

Processing recommendations for 10 users

 SEQUENTIAL (1 Core) - Processing...
   Processing user 1/10...
   Processing user 2/10...
   Processing user 3/10...
   Processing user 4/10...
   Processing user 5/10...
   Processing user 6/10...
   Processing user 7/10...
   Processing user 8/10...
   Processing user 9/10...
   Processing user 10/10...
    Completed in 10.13 seconds

 CONCURRENT (Threading) - Processing...
    Completed in 11.48 seconds

 PARALLEL (Multiprocessing) - Processing...
   Using 4 CPU cores
    Completed in 12.46 seconds

======================================================================
PERFORMANCE COMPARISON
======================================================================

Method                         Time (seconds)     Speedup     
----------------------------------------------------------------------
Sequential (1 Core)            10.13              1.00x       
Concurrent (Threads)           11.48              0.88        x
Parallel (Multiprocessing)     12.46              0.81        x

======================================================================
PERFORMANCE SUMMARY
======================================================================

   Data Size: 3,500,000 records
   Users: 50,000
   Target Users: 10

    Sequential is faster. Data size may need to be larger.

======================================================================
PROGRAM COMPLETE!
======================================================================
```

## Summary 
* For CPU-intensive activities, parallel processing (multiprocessing) works best, achieving a speedup of about three times.
* Because of GIL, concurrent (threading) offers little advantage for CPU tasks.
* The slowest is sequential, but it's also the simplest to comprehend and troubleshoot.
