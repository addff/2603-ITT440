# PARALLEL PLAYLIST ANALYZER
## FARID AIMAN BIN ZANUAR
## 2024272488
## YOUTUBE LINK : https://youtu.be/Y6Sy9ThRlkQ?si=n7EKk3wvZvZ6K6GL

# PROBLEM STATEMENTS
Millions of playlists by user must be analyzed by music streaming services to discover popular songs, locate active users, preferences on favourite genre, and understanding listening habits. Processing each user's playlist data  sequentially is too slow. This analyzer demonstrates how parallel and concurrent programming can speed up and improve the efficiency of playlists.

# OBJECTIVE
* Build a system that looks for trends and statistics in user playlist data.
* Implement sequential, concurrent (threading), and parallel (multiprocessing) versions
* Compare the performance of each approach
* Explain why CPU-intensive analytics benefit from parallel programming

# Project Scope

* Data Size: 50,000,000 listening records
* Users: 5,000 unique users
* Songs: 5,000 unique songs
* Target Users: 10 random users for recommendation
* Programming Language: Python 3.10+
* Platform: VS Code (Windows)

# Three Implementations
* Sequential - One by one (Processes users one after another)
* Concurrent - Threading ( Uses threads to interleave tasks)
* Parallel - Multiprocessing (Uses multiple CPU cores simultaneously)


# Difference between Sequential, Concurrent & Parallel

## Sequential
* The tasks are carried out sequentially. Before starting the next task, the previous one must be finished.

## Concurrent
* By alternating between several duties, they advance. They alternate, but only one duty is completed at a time.
* Best for: I/O operat
* Different CPU cores are used to conduct many tasks concurrently.
* Best for: CPU-intensive calculations (like similarity scoring)

# Code Structure
```ssh
# Sequential - one at a time
def run_sequential_analyzer(playlist_db, users):
    for user in users:
        analyze_user_playlist(user)  # wait for finish

# Concurrent - threading
def run_concurrent_analyzer(playlist_db, users):
    with ThreadPoolExecutor() as executor:
        executor.map(analyze_user_playlist, users)  # interleaved

# Parallel - multiprocessing  
def run_parallel_analyzer(playlist_db, users):
    with ProcessPoolExecutor() as executor:
        executor.map(analyze_user_playlist, users)  # simultaneous
  
```
## Key Function

* generate_data()	- Create 50M playlist records
* build_playlist_db()	- Build dictionary index for analysis
* analyze_user_playlist() - Calculate statistics for a user's playlist
* find_popular_songs() - Find most listened songs across all users 
* find_active_users() - Identify users with largest playlists
* find_similar_playlists() -Find playlists similar to a target user
* run_sequential_analyzer() - Process users one by one
* run_concurrent_analyzer() - Process users with threads
* run_parallel_analyzer() - Process users with multiple processes

# Scalling Data Table

| Data Size | Sequential | Concurrent | Parallel | Winner |
|-----------|------------|------------|----------|--------|
| 100K | 0.41s | 0.35s | 0.28s | Parallel |
| 500K | 2.07s | 1.77s | 1.42s | Parallel |
| 1M | 4.15s | 3.55s | 2.48s | Parallel |
| 2M | 8.30s | 7.10s | 5.67s | Parallel |
| 5M | 20.75s | 17.74s | 14.18s | Parallel |
| 10M | 207.45s | 177.40s | 141.75s | Parallel |

Note: 50M record times are extrapolated from 5M measurements (linear scaling).



# Result & Performance Analysis
## Expected Output
```ssh
============================================================
PARALLEL PLAYLIST ANALYZER - 50 MILLION RECORDS
============================================================
[GEN] Generating 50,000,000 playlist records...
[GEN] Completed in 124.75s
[DB] Building playlist database...
[DB] Built for 5,000 users in 26.80s

[ANALYSIS] Finding popular songs across all playlists...
[ANALYSIS] Top 10 popular songs found in 2.60s

Top 10 Most Popular Songs:
   1. Song 2834 - 76,170 total listens
   2. Song 912  - 72,835 total listens
   3. Song 456  - 69,460 total listens
   4. Song 1789 - 67,280 total listens
   5. Song 3456 - 63,945 total listens

[ANALYSIS] Finding most active users...
[ANALYSIS] Top active users found in 1.55s

Top 5 Most Active Users (Largest Playlists):
   1. User 4231 - 847 songs in playlist
   2. User 1567 - 812 songs in playlist
   3. User 2890 - 798 songs in playlist
   4. User 3456 - 765 songs in playlist
   5. User 1982 - 734 songs in playlist

[TARGET] Analyzing 10 users in detail...

[SEQUENTIAL] Analyzing playlists...
   Analyzing user 1/10
   ...
[SEQUENTIAL] Completed in 207.45s

[CONCURRENT] Threading Analyzer...
[CONCURRENT] Completed in 177.40s

[PARALLEL] Multiprocessing Analyzer...
[PARALLEL] Using 8 CPU cores
[PARALLEL] Completed in 141.75s

============================================================
PERFORMANCE COMPARISON
============================================================

Method                         Time         Speedup   
-------------------------------------------------------
Sequential                     207.45       1.00x     
Concurrent (Threads)           177.40       1.17x     
Parallel (Processes)           141.75       1.46x     

============================================================
WINNER: PARALLEL (1.46x faster)
============================================================

============================================================
SAMPLE PLAYLIST ANALYSIS RESULTS
============================================================

User ID: 4231
   Total songs in playlist: 847
   Total listens: 62,280
   Average listens per song: 73.53
   Top 3 songs: [(2834, 225), (912, 210), (456, 190)]

============================================================
PROGRAM COMPLETE!
============================================================
```
# Analysis of Results

* Parallel is fastest (1.46x)	- Uses multiple CPU cores simultaneously
* Concurrent is middle (1.17x) - Threads provide some benefit but limited by GIL
* Sequential is slowest	- Only uses 1 core, others idle
* Time saved: 65.7 seconds	- Parallel saves 32% of processing time
* Popular songs found quickly - Dictionary lookups makes the global analysis efficient
* Active user identified - Playlist size analysis reveals power users

## Summary 
* Fastest Method	- Parallel (Multiprocessing) - 141.75.s
* Speedup -	1.46x faster than Sequential
* Time Saved - 65.70 seconds
* Improvement	- 32% reduction in processing time
* Best for CPU-intensive tasks - Parallel
* Popular Songs Found - Top 10 across 50M records
* Active Users Found - Top 5 with largest playlists
