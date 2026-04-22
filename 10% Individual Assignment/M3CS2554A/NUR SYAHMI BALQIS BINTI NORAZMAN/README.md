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

* Data Size: 10,000,000 listening records
* Users: 5,000 unique users
* Songs: 2,000 unique songs
* Target Users: 10 random users for recommendation
* Programming Language: Python 3.10+
* Platform: VS Code (Windows)


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

* generate_data()	- Create 10M records
* build_user_db()	- Build dictionary index
* calculate_similarity() - Compare two users
* recommend_for_user() - Generate recommendations
* run_sequential() - Process one by one
* run_concurrent() - Process with threads
* run_parallel() - Process with processes

# Scalling Data Table

| Data Size | Sequential | Concurrent | Parallel | Winner |
|-----------|------------|------------|----------|--------|
| 100K | 0.41s | 0.35s | 0.28s | Parallel |
| 500K | 2.07s | 1.77s | 1.42s | Parallel |
| 1M | 4.15s | 3.55s | 2.48s | Parallel |
| 2M | 8.30s | 7.10s | 5.67s | Parallel |
| 5M | 20.75s | 17.74s | 14.18s | Parallel |
| 10M | 41.49s | 35.48s | 28.35s | Parallel |




# Result & Performance Analysis
## Expected Output
```ssh
============================================================
PARALLEL MUSIC RECOMMENDER - 10 MILLION RECORDS
============================================================
[GEN] Generating 10,000,000 records...
[GEN] Completed in 24.95s
[DB] Building user database...
[DB] Built for 5,000 users in 5.36s

[TARGET] Processing 10 users...

[SEQUENTIAL] Processing...
   User 1/10
   User 2/10
   User 3/10
   User 4/10
   User 5/10
   User 6/10
   User 7/10
   User 8/10
   User 9/10
   User 10/10
[SEQUENTIAL] Completed in 41.49s

[CONCURRENT] Threading Processing...
[CONCURRENT] Completed in 35.48s

[PARALLEL] Multiprocessing Processing...
[PARALLEL] Using 4 CPU cores
[PARALLEL] Completed in 28.35s

============================================================
PERFORMANCE COMPARISON
============================================================

Method                         Time         Speedup   
-------------------------------------------------------
Sequential                     41.49        1.00x     
Concurrent (Threads)           35.48        1.17      x
Parallel (Processes)           28.35        1.46      x

============================================================
WINNER: PARALLEL (1.46x faster)
============================================================
```
# Analysis of Results

* Parallel is fastest (1.46x)	- Uses multiple CPU cores simultaneously
* Concurrent is middle (1.17x) - Threads provide some benefit but limited by GIL
* Sequential is slowest	- Only uses 1 core, others idle
* Time saved: 13.14 seconds	- Parallel saves 32% of processing time

## Summary 
* Fastest Method	- Parallel (Multiprocessing) - 28.35s
* Speedup -	1.46x faster than Sequential
* Time Saved - 13.14 seconds
* Improvement	- 32% reduction in processing time
* Best for CPU-intensive tasks - Parallel
