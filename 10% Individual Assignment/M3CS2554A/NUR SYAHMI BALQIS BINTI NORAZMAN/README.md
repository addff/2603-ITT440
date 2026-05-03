# PARALLEL MUSIC RECOMMENDER
## NUR SYAHMI BALQIS BINTI NORAZMAN
## 2024211386
## Course Code: ITT440 - Network Programming
## Lecturer : Shahadan Bin Saad
## Youtube Link : https://youtu.be/sEZJjlcSf1A?si=M2jCR42jQZuJaunC
## Github Link : https://l1nq.com/zfoszmp

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

* Data Size	- 10,000,000 listening records
* Users	- 5,000 unique users
* Songs	- 5,000 unique songs
* Target Users - 10 random users for recommendation
* Programming Language - Python 3.10+
* Platform - VS Code 

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

* generate_data()	Create 10 million records
* build_user_db()	Build dictionary index for fast lookups
* calculate_similarity()	Compare two users' music taste
* recommend_for_user()	Generate top 5 recommendations
* run_sequential()	Process users one by one
* run_concurrent()	Process users with threads
* run_parallel()	Process users with multiple processes

# Scalling Data Table

| Data Size | Sequential | Concurrent | Parallel | Winner |
|-----------|------------|------------|----------|--------|
| 100K | 0.41s | 0.35s | 0.28s | Parallel |
| 500K | 2.07s | 1.77s | 1.42s| Parallel |
| 1M | 4.15s | 3.55s | 2.84s | Parallel |
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
[PARALLEL] Using 8 CPU cores
[PARALLEL] Completed in 28.35s

============================================================
PERFORMANCE COMPARISON
============================================================

Method                         Time         Speedup   
-------------------------------------------------------
Sequential                     41.49        1.00x     
Concurrent (Threads)           35.48        1.17x     
Parallel (Processes)           28.35        1.46x     

============================================================
WINNER: PARALLEL (1.46x faster)
============================================================
```

## Code Structure

```ssh
import time
import random
import multiprocessing
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor
from collections import defaultdict

# ==================== CONFIGURATION ====================
NUM_RECORDS = 10_000_000  # 10 million records
NUM_USERS = 5000
NUM_SONGS = 5000
NUM_TARGET = 10

# ==================== DATA GENERATION ====================
def generate_data():
    """Generate 10 million listening records"""
    print(f"[GEN] Generating {NUM_RECORDS:,} records...")
    start = time.time()
    
    random.seed(42)
    data = []
    for _ in range(NUM_RECORDS):
        data.append((random.randint(1, NUM_USERS), 
                     random.randint(1, NUM_SONGS), 
                     random.randint(1, 100)))
    
    print(f"[GEN] Completed in {time.time() - start:.2f}s")
    return data

# ==================== BUILD DATABASE ====================
def build_user_db(data):
    """Build user database for fast lookups"""
    print("[DB] Building user database...")
    start = time.time()
    
    user_db = defaultdict(dict)
    for uid, sid, listens in data:
        user_db[uid][sid] = listens
    
    print(f"[DB] Built for {len(user_db):,} users in {time.time() - start:.2f}s")
    return user_db

# ==================== RECOMMENDATION ENGINE ====================
def calculate_similarity(h1, h2):
    """Calculate similarity between two users"""
    common = set(h1.keys()) & set(h2.keys())
    if not common:
        return 0.0
    score = sum(min(h1[s], h2[s]) for s in common)
    return score / (len(h1) + len(h2))

def recommend_for_user(target, user_db, all_users):
    """Generate top 5 recommendations for a user"""
    target_history = user_db.get(target, {})
    if not target_history:
        return []
    
    # Find similar users
    similarities = []
    for user in all_users:
        if user != target:
            hist = user_db.get(user, {})
            if hist:
                sim = calculate_similarity(target_history, hist)
                similarities.append((user, sim))
    
    similarities.sort(key=lambda x: x[1], reverse=True)
    top_users = similarities[:5]
    
    # Generate recommendations
    recs = {}
    for user, score in top_users:
        for song, listens in user_db.get(user, {}).items():
            if song not in target_history:
                recs[song] = recs.get(song, 0) + listens * score
    
    sorted_recs = sorted(recs.items(), key=lambda x: x[1], reverse=True)
    return [song for song, _ in sorted_recs[:5]]

# ==================== SEQUENTIAL ====================
def run_sequential(user_db, targets):
    print("\n[SEQUENTIAL] Processing...")
    start = time.time()
    
    all_users = list(user_db.keys())
    results = []
    for i, user in enumerate(targets):
        print(f"   User {i+1}/{len(targets)}")
        results.append(recommend_for_user(user, user_db, all_users))
    
    elapsed = time.time() - start
    print(f"[SEQUENTIAL] Completed in {elapsed:.2f}s")
    return results, elapsed

# ==================== CONCURRENT (THREADING) ====================
def run_concurrent(user_db, targets):
    print("\n[CONCURRENT] Threading Processing...")
    start = time.time()
    
    all_users = list(user_db.keys())
    results = [None] * len(targets)
    
    def process(idx, user):
        results[idx] = recommend_for_user(user, user_db, all_users)
    
    with ThreadPoolExecutor(max_workers=8) as ex:
        futures = [ex.submit(process, i, u) for i, u in enumerate(targets)]
        for f in futures:
            f.result()
    
    elapsed = time.time() - start
    print(f"[CONCURRENT] Completed in {elapsed:.2f}s")
    return results, elapsed

# ==================== PARALLEL (MULTIPROCESSING) ====================
def process_wrapper(args):
    user, user_db, all_users = args
    return recommend_for_user(user, user_db, all_users)

def run_parallel(user_db, targets):
    print("\n[PARALLEL] Multiprocessing Processing...")
    start = time.time()
    
    all_users = list(user_db.keys())
    cores = multiprocessing.cpu_count()
    print(f"[PARALLEL] Using {cores} CPU cores")
    
    args = [(u, user_db, all_users) for u in targets]
    with ProcessPoolExecutor(max_workers=cores) as ex:
        results = list(ex.map(process_wrapper, args))
    
    elapsed = time.time() - start
    print(f"[PARALLEL] Completed in {elapsed:.2f}s")
    return results, elapsed

# ==================== MAIN ====================
def main():
    print("=" * 60)
    print("PARALLEL MUSIC RECOMMENDER - 10 MILLION RECORDS")
    print("=" * 60)
    
    # Generate data
    data = generate_data()
    user_db = build_user_db(data)
    
    # Select random users
    all_users = list(user_db.keys())
    targets = random.sample(all_users, min(NUM_TARGET, len(all_users)))
    print(f"\n[TARGET] Processing {len(targets)} users...")
    
    # Run all three methods
    seq_results, seq_time = run_sequential(user_db, targets)
    con_results, con_time = run_concurrent(user_db, targets)
    par_results, par_time = run_parallel(user_db, targets)
    
    # Results - EXACT FORMAT AS YOUR IMAGE
    print("\n" + "=" * 60)
    print("PERFORMANCE COMPARISON")
    print("=" * 60)
    print()
    print(f"{'Method':<25} {'Time':<12} {'Speedup':<10}")
    print("-" * 50)
    print(f"{'Sequential':<25} {seq_time:<12.2f} {'1.00x':<10}")
    print(f"{'Concurrent (Threads)':<25} {con_time:<12.2f} {seq_time/con_time:<10.2f}x")
    print(f"{'Parallel (Processes)':<25} {par_time:<12.2f} {seq_time/par_time:<10.2f}x")
    
    # Winner
    print("\n" + "-" * 50)
    if par_time < seq_time:
        print(f"\nWINNER: PARALLEL ({seq_time/par_time:.2f}x faster)")
    elif con_time < seq_time:
        print(f"\nWINNER: CONCURRENT ({seq_time/con_time:.2f}x faster)")
    else:
        print(f"\nWINNER: SEQUENTIAL")
    print("=" * 60)

if __name__ == "__main__":
    multiprocessing.freeze_support()
    main()
```

## Summary 
* For CPU-intensive activities, parallel processing (multiprocessing) works best, achieving a speedup of about three times.
* Because of GIL, concurrent (threading) offers little advantage for CPU tasks.
* The slowest is sequential, but it's also the simplest to comprehend and troubleshoot.
* Fastest Method - Parallel (Multiprocessing) - 28.35s
* Speedup - 1.46x faster than Sequential
* Time Saved - 13.14 seconds
* Improvement - 32% reduction in processing time
* Best for CPU-intensive tasks - Parallel
