# BANK TRANSACTION PROCESSING SYSTEM
### Student Information: Ainaa Sofeeya Binti Amran
### Date: April 24, 2026

## SYSTEM ENVIRONMENT
* OS: Ubuntu
* Python Script: bank.py
* Link Youtube Video: https://youtu.be/76C3l08Awew?si=_JWPcfp4PhBoFq4Q

---
# 1. INTRODUCTION
In modern banking systems, large volumes of transactions such as deposits, withdrawals, and transfers are processed continuously. Efficient processing is crucial to ensure fast and reliable services.
This project simulates a bank transaction system and evaluates the performance of three processing techniques:

* Sequential Processing
* Concurrent Processing (Multithreading)
* Parallel Processing (Multiprocessing)

The comparison focuses on execution time and system efficiency when handling a large number of transactions.

---
# 2. PROBLEM STATEMENT
As transaction volume increases, traditional sequential processing becomes inefficient due to its inability to utilize system resources effectively.
The problem addressed in this project is:
```ssh
How do sequential, concurrent, and parallel processing techniques affect the performance
of a banking system when handling millions of transactions?
```
This project investigates which method provides the best performance and why.

---
# 3. OBJECTIVES
* To simulate real-world bank transactions.
* To implement sequential, concurrent, and parallel processing.
* To compare execution time across different methods.
* To analyze the impact of computational workload on performance.

---
# 4. SYSTEM DESIGN
## 4.1 Transaction Types
The system includes three transaction types:

* Deposit – adds money to the account
* Withdrawal – subtracts money from the account
* Transfer – simulates fund transfer

## 4.2 Computational Design
To simulate realistic workload:

* Each transaction performs a base computation (compute)
* Sequential processing includes:
   * Multiple class layers (Layer1 → Layer2 → Layer3)
   * Additional heavy computation (extra_heavy)

 ## 4.3 Processing Methods
 
 #### a) Sequential Processing
   * Executes transactions one by one
   * Includes additional computation and layered class calls
   * Designed to be the slowest method


#### b) Concurrent Processing (Threading)
   * Uses ThreadPoolExecutor
   * Divides tasks into smaller chunks
   * Limited by Python’s Global Interpreter Lock (GIL)


#### c) Parallel Processing (Multiprocessing)
   * Uses multiprocessing.Pool
   * Executes tasks across multiple CPU cores
   * Enables true parallel execution

---
# 5. IMPLEMENTATION
## 5.1 Source Code 
```ssh
import random
import time
from concurrent.futures import ThreadPoolExecutor
from multiprocessing import Pool, cpu_count
 
# =========================
# LIGHT COMPUTE
# =========================
def compute(x):
    for _ in range(20):
        x = (x * 3 + 7) % 1000003
    return x
 
# =========================
# EXTRA HEAVY
# =========================
def extra_heavy(x):
    for _ in range(100):
        x = (x * 5 + 11) % 1000003
    return x
 
# =========================
# EXTRA LAYERS
# =========================
class Layer1:
    @staticmethod
    def process(x):
        return Layer2.process(x)
 
class Layer2:
    @staticmethod
    def process(x):
        return Layer3.process(x)
 
class Layer3:
    @staticmethod
    def process(x):
        x = compute(x)
        x = extra_heavy(x)
        return x
 
# =========================
# CLASSES
# =========================
class Deposit:
    @staticmethod
    def execute_seq(bal, amt):
        Layer1.process(amt)   
        return bal + amt
 
    @staticmethod
    def execute_fast(bal, amt):
        compute(amt)       
        return bal + amt
 
class Withdraw:
    @staticmethod
    def execute_seq(bal, amt):
        Layer1.process(amt)
        return bal - amt
 
    @staticmethod
    def execute_fast(bal, amt):
        compute(amt)
        return bal - amt
 
class Transfer:
    @staticmethod
    def execute_seq(bal, amt):
        Layer1.process(amt)
        return bal - amt
 
    @staticmethod
    def execute_fast(bal, amt):
        compute(amt)
        return bal - amt
 
# =========================
# PROCESS TRANSACTION
# =========================
def process_transaction(bal, act, amt, mode="fast"):
    if act == "deposit":
        if mode == "seq":
            return Deposit.execute_seq(bal, amt), 1, 0, 0
        else:
            return Deposit.execute_fast(bal, amt), 1, 0, 0
 
    elif act == "withdraw":
        if mode == "seq":
            return Withdraw.execute_seq(bal, amt), 0, 1, 0
        else:
            return Withdraw.execute_fast(bal, amt), 0, 1, 0
 
    else:
        if mode == "seq":
            return Transfer.execute_seq(bal, amt), 0, 0, 1
        else:
            return Transfer.execute_fast(bal, amt), 0, 0, 1
 
# =========================
# WORKER
# =========================
def worker(n, mode="fast"):
    bal, d, w, t = 10000, 0, 0, 0
 
    for _ in range(n):
        act = random.choice(["deposit","withdraw","transfer"])
        amt = random.randint(1,1000)
 
        bal, dd, ww, tt = process_transaction(bal, act, amt, mode)
        d += dd
        w += ww
        t += tt
 
    return bal, d, w, t
 
# =========================
# COMBINE RESULTS
# =========================
def combine(results, workers):
    total_bal = sum(r[0] for r in results) - (10000 * (workers - 1))
    total_d = sum(r[1] for r in results)
    total_w = sum(r[2] for r in results)
    total_t = sum(r[3] for r in results)
    return total_bal, total_d, total_w, total_t
 
# =========================
# SEQUENTIAL
# =========================
def run_sequential(n):
    start = time.time()
    result = worker(n, mode="seq")   
    return result, time.time() - start
 
# =========================
# CONCURRENT
# =========================
def run_concurrent(n, workers=4):
    start = time.time()
    chunk = n // workers
 
    with ThreadPoolExecutor(max_workers=workers) as ex:
        results = list(ex.map(lambda x: worker(x, "fast"), [chunk]*workers))
 
    combined = combine(results, workers)
    return combined, time.time() - start
 
# =========================
# PARALLEL
# =========================
def run_parallel(n):
    workers = cpu_count()
    start = time.time()
    chunk = n // workers
 
    with Pool(workers) as p:
        results = p.starmap(worker, [(chunk, "fast")] * workers)
 
    combined = combine(results, workers)
    return combined, time.time() - start
 
# =========================
# MAIN
# =========================
if __name__ == "__main__":
    NUM = 30_000_000
 
    print(f"\nPROCESSING {NUM:,} TRANSACTIONS...\n")
 
    print("SEQUENTIAL...")
    (bal, d, w, t), t1 = run_sequential(NUM)
    print(f"Deposits: {d}")
    print(f"Withdrawals: {w}")
    print(f"Transfers: {t}")
    print(f"Balance: RM{bal}\n")
 
    print("CONCURRENT...")
    (_, _, _, _), t2 = run_concurrent(NUM)
 
    print("PARALLEL...")
    (_, _, _, _), t3 = run_parallel(NUM)
 
    print("\n=== PERFORMANCE ===")
    print(f"Sequential : {t1:.4f}s")
    print(f"Concurrent : {t2:.4f}s")
    print(f"Parallel   : {t3:.4f}s")
```
    

## 5.2 Implementation Description

| Component | Description |
|-----------|-------------|
| `compute(x)` | Performs basic computation for each transaction to simulate processing workload |
| `extra_heavy(x)` | Adds additional computational load specifically for sequential execution to increase processing time |
| `Layer1`, `Layer3`, `Layer3` | Multi-layer class structure used in sequential mode to introduce function call overhead |
| `Deposit`, `Withdraw`, `Transfer` | Class that represents deposit transaction type and executes balance update |
| `process_transaction()` | Determines transaction type and routes execution based on mode (sequential or fast) |
| `worker()` | Processes a batch of transactions and tracks balance and transaction counts |
| `combine()` | Aggregates results from multiple workers into a final result |
| `run_sequential()` | Executes transactions sequentially using heavy processing mode |
| `run_concurrent()` | Executes transactions using multithreading (ThreadPoolExecutor) |
| `run_parallel()` | Executes transactions using multiprocessing (Pool) across CPU cores |
| `main block` | Controls execution flow, runs all methods, and prints results |

---
# 6. OUTPUT RESULT
<img width="566" height="450" alt="image" src="https://github.com/user-attachments/assets/c19d860b-dea0-4322-acf5-a40484ecb275" />

---
# 7. PERFORMANCE ANALYSIS

## 7.1 Results Summary
| Method | Execution Time (seconds) | Speedup |
|--------|-------------------------|---------|
| Sequential | 551.0373 | 1.00× |
| Concurrent | 145.3051 | 3.79× |
| Parallel | 29.3132 | 18.80× |

---

## 7.2 Transaction Statistics

| Transaction Type | Count(RM) |
|-----------------|-------|
| Deposits | 9,998,542 |
| Withdrawals | 10,003,900 |
| Transfers | 9,997,558 |
| **Total** | **30,000,000** |

## 7.3 Analysis
Sequential (551.04s): Slowest method. Execution time is significantly higher due to additional computational overhead, including multiple class layers and extra heavy processing. Each transaction goes through several function calls, making it inefficient for large-scale workloads.

Concurrent (145.31s): Approximately 3.79× faster than sequential. Performance improved by dividing tasks into multiple threads; however, the improvement is limited by Python’s Global Interpreter Lock (GIL), which prevents true parallel execution for CPU-bound tasks.

Parallel (29.31s): Fastest method. Achieved approximately 18.80× speedup compared to sequential. This method utilizes multiple CPU cores, allowing true parallel execution and significantly reducing overall processing time.


## 7.4 Key Observations
* Sequential processing is significantly slower due to added computational complexity.

* Multithreading improves performance but is limited for CPU-intensive workloads.

* Multiprocessing provides the highest performance gain due to parallelism.

---
# 8. CONCLUSION
In conclusion, this project successfully demonstrates the performance differences between sequential, concurrent, and parallel processing in handling large-scale bank transactions. Sequential processing was the slowest due to its single-threaded nature and additional computational complexity introduced in the system design. Concurrent processing provided moderate improvement but was limited by Python’s Global Interpreter Lock, making it less effective for CPU-bound operations.

On the other hand, parallel processing significantly outperformed the other methods by utilizing multiple CPU cores, resulting in the fastest execution time. This clearly shows that multiprocessing is the most suitable approach for improving performance in systems that require handling large volumes of computational tasks.
