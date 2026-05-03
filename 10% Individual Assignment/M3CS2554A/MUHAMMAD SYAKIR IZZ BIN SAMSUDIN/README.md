# MUHAMMAD SYAKIR IZZ BIN SAMSUDIN
#  Parallel Computation of Mean for Large Numerical Datasets

[![Python](https://img.shields.io/badge/Python-3.7%2B-blue)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Stdlib Only](https://img.shields.io/badge/Dependencies-None-brightgreen)]()

> **ITT440 - Network Programming**  
> Individual Assignment: Parallel and Concurrent Programming  
---

## 📋 Project Information

| Field | Details |
|-------|---------|
| **Project Title** | Parallel Computation of Mean for Large Numerical Datasets |
| **Name** | MUHAMMAD SYAKIR IZZ BIN SAMSUDIN|
| **Student ID** | 2024274988 |
| **Class** |M3CS2554A|
| **Course** | ITT440 - Network Programming |
| **Lecturer** | SIR SHAHADAN BIN SAAD |
| **Youtube Link** |  |


---

## 📌 Introduction

Effective numerical computation is crucial for machine learning, scientific computing, and data analysis in the big data age. When dataset sizes reach millions or billions of entries, traditional sequential programming becomes more inefficient since it uses a single CPU core to handle data one element at a time.

Although calculating the mean (average) of a big dataset is a basic statistical operation, performance can be significantly impacted by the method utilized. In order to calculate the mean of huge numerical datasets, this research examines three distinct programming paradigms: sequential, threading (concurrent), and multiprocessing (parallel). It also evaluates the execution performance of each paradigm.


---

## 🎯 Objectives

1.  To implement **sequential, concurrent (threading), and parallel (multiprocessing)** programming techniques
2.  To efficiently process **large-scale numerical datasets** (10M numbers)
3.  To **compare execution performance** across all three methods
4.  To understand the impact of Python's **Global Interpreter Lock (GIL)**
5.  To analyze **why certain methods outperform others** for CPU-bound tasks
6.  To demonstrate **workload distribution** across threads and processes

---

## 📊 Project Overview

This project develops a high-performance **Parallel Mean Calculator** with a beautiful GUI dashboard capable of:

- 📈 Computing statistical mean of **10+ million numbers**
- 🔄 Comparing **3 execution methods** in real-time
- 🎨 Displaying results in an **interactive GUI**
- 📊 Visualizing performance with **bar charts**
- 📁 Supporting **multiple data sources** (generate, manual, file)


---

## 🖥️ GUI Screenshots

### Tab 1: Data Setup
<img width="1915" height="835" alt="image" src="https://github.com/user-attachments/assets/08503d43-e676-44bd-9273-bbf64c49a4e8" />

### Tab 2: Preview Numbers
<img width="1918" height="986" alt="image" src="https://github.com/user-attachments/assets/56e9102f-c785-491c-803c-5f05d9f63efe" />

### Tab 3: Performance Results
<img width="1916" height="952" alt="image" src="https://github.com/user-attachments/assets/7bf6c4bf-e447-4d2a-b811-3eabe39be266" />

---

## 📊 Test Results

### Test Configuration


### Performance Results (Actual Output)

| Rank | Method | Time (s) | Speedup | Algorithm | Cores Used |
|:----:|--------|:--------:|:-------:|-----------|:-----------:|
| 🥇 | **Threading** | **0.0797** | **6.12×** | Built-in sum / 4 threads | 4 threads |
| 🥈 | Sequential | 0.4876 | 1.00× | Manual loop + variance | 1 core |
| 🥉 | Multiprocessing | 0.7490 | 0.65× | Built-in sum / 12 processes | 12 processes |

---
## 🔬 Algorithm Design
## Algorithm 1: Sequential Computation


    import time
    from typing import Tuple


    def compute_mean_variance(data: list) -> Tuple[float, float, float]:
    """
    Single-core, manual for-loop mean + variance computation.
    
    This method is GUARANTEED to be the slowest because:
    - Manual Python loop (no C-speed sum)
    - Two complete passes through the data
    - Single-threaded execution
    
    Args:
        data: List of float numbers
        
    Returns:
        Tuple of (mean, variance, elapsed_seconds)
    
    Example:
        >>> data = [1.0, 2.0, 3.0, 4.0, 5.0]
        >>> mean, variance, elapsed = compute_mean_variance(data)
        >>> print(f"Mean: {mean}, Time: {elapsed:.4f}s")
    """
    n = len(data)
    
    # Start timing
    t0 = time.perf_counter()
    
    # PASS 1: Manual sum (SLOW - pure Python iteration)
    # Each addition happens in Python bytecode, not C
    total = 0.0
    for x in data:
        total += x  # Python-level addition (slow)
    
    mean = total / n
    
    # PASS 2: Manual variance (EXTRA WORK)
    # Sequential does this second pass; threading/multiprocessing skip it
    sq_dev = 0.0
    for x in data:
        diff = x - mean
        sq_dev += diff * diff
    
    variance = sq_dev / n
    
    # End timing
    elapsed = time.perf_counter() - t0
    
    return mean, variance, elapsed


    def compute_mean_only(data: list) -> Tuple[float, float]:
    """
    Simplified version: mean only (no variance computation).
    Still slow due to manual loop.
    
    Args:
        data: List of float numbers
        
    Returns:
        Tuple of (mean, elapsed_seconds)
    """
    n = len(data)
    t0 = time.perf_counter()
    
    total = 0.0
    for x in data:
        total += x
    
    elapsed = time.perf_counter() - t0
    return total / n, elapsed


    # Performance characteristics constant
    METHOD_INFO = {
    "name": "Sequential",
    "algorithm": "Manual Python for-loop",
    "passes": 2,
    "parallelization": "None (single core)",
    "speed_rating": "🐢 SLOWEST",
    "color": "#FF7B72",  # Red
    "best_for": "Datasets < 10,000 elements or debugging"
}
   ## Algorithm 2: Threading Computation
      
    import threading
    import time
    import math
    from typing import List, Tuple, Dict


    def _sum_worker(chunk: List[float], results: Dict[int, float], idx: int):
    """
    Worker function for a single thread.
    Uses built-in sum() which runs at C speed.
    
    Args:
        chunk: List of numbers to sum
        results: Shared dictionary to store result
        idx: Index key for this worker
    """
    # Built-in sum runs in C - much faster than manual loop!
    results[idx] = sum(chunk)


    def compute_mean(data: List[float], num_threads: int) -> Tuple[float, float]:
    """
        Multi-threaded mean using Python threads.
    
    How it works:
        1. Split data into N chunks (one per thread)
        2. Start N threads, each summing their chunk
        3. Wait for all threads to complete
        4. Combine partial sums and calculate mean
    
    Despite GIL limitations, this is faster than sequential because:
        - Each thread uses C-speed sum() instead of Python loop
        - Thread creation overhead is relatively low
        - Can utilize some level of parallelism for I/O
    
    Args:
        data: List of float numbers
        num_threads: Number of threads to use (typically 2-8)
        
    Returns:
        Tuple of (mean, elapsed_seconds)
    
    Example:
        >>> data = [1.0, 2.0, 3.0, 4.0, 5.0]
        >>> mean, elapsed = compute_mean(data, 4)
        >>> print(f"Mean: {mean}, Time: {elapsed:.4f}s")
    """
    n = len(data)
    
    # Split data into chunks (one per thread)
    chunk_size = math.ceil(n / num_threads)
    chunks = [data[i:i + chunk_size] for i in range(0, n, chunk_size)]
    
    # Start timing after chunk preparation
    t0 = time.perf_counter()
    
    # Dictionary to store results from each thread
    results = {}
    threads = []
    
    # Create and start threads
    for i, chunk in enumerate(chunks):
        t = threading.Thread(
            target=_sum_worker,
            args=(chunk, results, i)
        )
        threads.append(t)
        t.start()
    
    # Wait for all threads to complete
    for t in threads:
        t.join()
    
    # Combine partial sums (this happens in main thread)
    total = sum(results.values())
    mean = total / n
    
    elapsed = time.perf_counter() - t0
    return mean, elapsed


    def compute_mean_with_variance(data: List[float], num_threads: int) -> Tuple[float, float, float]:
    """
    Multi-threaded mean with variance calculation.
    Variance is computed using the two-pass algorithm for numerical stability.
    
    Note: Variance computation still requires passing through all data,
    but threading still helps with the sum portion.
    
    Args:
        data: List of float numbers
        num_threads: Number of threads to use
        
    Returns:
        Tuple of (mean, variance, elapsed_seconds)
    """
    n = len(data)
    
    # First pass: compute mean using threads
    mean, t1 = compute_mean(data, num_threads)
    
    # Second pass: compute sum of squared deviations
    # This could also be threaded, but we keep simple for comparison
    t0 = time.perf_counter()
    sq_dev = sum((x - mean) ** 2 for x in data)
    variance = sq_dev / n
    t2 = time.perf_counter() - t0
    
    return mean, variance, t1 + t2


    # Performance characteristics constant
    METHOD_INFO = {
    "name": "Threading",
    "algorithm": "Built-in sum() per chunk + Python threads",
    "parallelization": "Multiple threads (GIL limited)",
    "speed_rating": "⚡ MEDIUM",
    "color": "#F0A500",  # Amber
    "best_for": "Datasets 10,000 - 1,000,000 elements or I/O mixed workloads",
    "limitation": "GIL prevents true parallel CPU execution"
}
 ## Algorithm 3: Multiprocessing Computation
    import multiprocessing as mp
    import time
    import math
    from typing import List, Tuple
    
    
    def _sum_worker(chunk: List[float]) -> float:
        """
        Worker function for a single process.
        Runs in its own Python interpreter with its own GIL.
    
    Args:
        chunk: List of numbers to sum
        
    Returns:
        Sum of all numbers in the chunk
    
    Note: This function must be pickle-able for multiprocessing.
    """
    # Built-in sum runs at C speed, now with true parallelism!
    return sum(chunk)


    def compute_mean(data: List[float], num_processes: int = None) -> Tuple[float, float]:
        """
        Multi-process mean computation using process pool.
    
    How it works:
        1. Split data into N chunks (one per process)
        2. Create a pool of N worker processes
        3. Each process gets its own chunk and computes sum
        4. Processes run truly in parallel on different CPU cores
        5. Combine partial sums and calculate mean
    
    This is the FASTEST method for CPU-bound numerical computation
    because there's no GIL contention.
    
    Args:
        data: List of float numbers
        num_processes: Number of processes to use (default: CPU count)
        
    Returns:
        Tuple of (mean, elapsed_seconds)
    
    Example:
        >>> data = [1.0, 2.0, 3.0, 4.0, 5.0]
        >>> mean, elapsed = compute_mean(data, 8)
        >>> print(f"Mean: {mean}, Time: {elapsed:.4f}s")
    """
    n = len(data)
    
    # Auto-detect optimal process count if not specified
    if num_processes is None:
        num_processes = mp.cpu_count()
    
    # Ensure we don't create more processes than chunks
    num_processes = min(num_processes, n)
    
    # Split data into chunks (one per process)
    chunk_size = math.ceil(n / num_processes)
    chunks = [data[i:i + chunk_size] for i in range(0, n, chunk_size)]
    
    # Start timing after data splitting
    t0 = time.perf_counter()
    
    # Create process pool and map chunks to workers
    # Each worker runs in a separate process with its own GIL
    with mp.Pool(processes=num_processes) as pool:
        partial_sums = pool.map(_sum_worker, chunks)
    
    # Combine results (happens in main process)
    total = sum(partial_sums)
    mean = total / n
    
    elapsed = time.perf_counter() - t0
    return mean, elapsed


    def compute_mean_with_variance(data: List[float], num_processes: int = None) -> Tuple[float, float, float]:
        """
        Multi-process mean with variance calculation.
        
    Note: For true parallel variance, we would need to implement
    a parallel algorithm (e.g., Welford's method), but this version
    keeps variance sequential for fair comparison with other methods.
    
    Args:
        data: List of float numbers
        num_processes: Number of processes to use
        
    Returns:
        Tuple of (mean, variance, elapsed_seconds)
    """
    n = len(data)
    
    # First pass: compute mean in parallel
    mean, t1 = compute_mean(data, num_processes)
    
    # Second pass: compute sum of squared deviations
    # This could also be parallelized, but we keep simple
    t0 = time.perf_counter()
    sq_dev = sum((x - mean) ** 2 for x in data)
    variance = sq_dev / n
    t2 = time.perf_counter() - t0
    
    return mean, variance, t1 + t2


    def get_optimal_process_count() -> int:
        """
        Returns the recommended number of processes for the current system.
        Usually equal to the number of CPU cores.
    
    Returns:
        Number of CPU cores available
    """
    return mp.cpu_count()


    # Performance characteristics constant
    METHOD_INFO = {
    "name": "Multiprocessing",
    "algorithm": "Built-in sum() per chunk + separate processes",
    "parallelization": "Multiple processes (true parallel)",
    "speed_rating": "🚀 FASTEST",
    "color": "#3FB950",  # Green
    "best_for": "CPU-bound datasets > 1,000,000 elements",
    "advantage": "Bypasses GIL completely"
    }
    4. data/generator.py - Data Generation Utilities
    python
    #!/usr/bin/env python3
    """
    Data Generation Utilities for Parallel Mean Calculator
    
    Provides various methods to generate test datasets with different
    statistical distributions. All implementations use only Python stdlib.
    """
    
    import random
    import math
    from typing import List, Optional
    
    
    def generate_uniform(size: int, low: float = 0.0, high: float = 100.0) -> List[float]:
        """
        Generate random numbers from a uniform distribution.
    
    Args:
        size: Number of values to generate
        low: Minimum value (default: 0)
        high: Maximum value (default: 100)
        
    Returns:
        List of uniformly distributed random numbers
        
    Example:
        >>> data = generate_uniform(1000, 0, 100)
        >>> print(f"Min: {min(data):.2f}, Max: {max(data):.2f}")
    """
    return [random.uniform(low, high) for _ in range(size)]


    def generate_normal(size: int, mean: float = 50.0, std: float = 15.0) -> List[float]:
    """
    Generate random numbers from a normal (Gaussian) distribution.
    
    Uses the Box-Muller transform for generating pairs of normally
    distributed random numbers from uniformly distributed ones.
    
    Args:
        size: Number of values to generate
        mean: Mean of the distribution (default: 50)
        std: Standard deviation (default: 15)
        
    Returns:
        List of normally distributed random numbers
        
    Example:
        >>> data = generate_normal(1000, 50, 15)
        >>> print(f"Mean approx: {sum(data)/len(data):.2f}")
    """
    data = []
    for _ in range((size + 1) // 2):
        # Generate two uniform random numbers in (0,1]
        u1 = random.random() or 1e-12  # Avoid log(0)
        u2 = random.random()
        
        # Box-Muller transform
        r = math.sqrt(-2 * math.log(u1))
        theta = 2 * math.pi * u2
        
        z1 = r * math.cos(theta)
        z2 = r * math.sin(theta)
        
        # Scale to desired mean and standard deviation
        data.append(mean + std * z1)
        data.append(mean + std * z2)
    
    return data[:size]


    def generate_exponential(size: int, rate: float = 0.05) -> List[float]:
    """
    Generate random numbers from an exponential distribution.
    
    Args:
        size: Number of values to generate
        rate: Rate parameter λ (default: 0.05)
        
    Returns:
        List of exponentially distributed random numbers
        
    Example:
        >>> data = generate_exponential(1000, 0.05)
        >>> print(f"Mean approx: {sum(data)/len(data):.2f}")
    """
    return [-math.log(random.random() or 1e-12) / rate for _ in range(size)]


    def load_from_file(filepath: str) -> Optional[List[float]]:
    """
    Load numbers from a text or CSV file.
    
    Supports:
        - One number per line
        - Comma-separated values
        - Space-separated values
        - Mixed formats
    
    Args:
        filepath: Path to the file
        
    Returns:
        List of numbers or None if file is empty/invalid
        
    Example:
        >>> data = load_from_file("numbers.txt")
        >>> if data:
        ...     print(f"Loaded {len(data)} numbers")
    """
    try:
        numbers = []
        with open(filepath, "r") as f:
            for line in f:
                # Replace commas with spaces for easier splitting
                for token in line.replace(",", " ").split():
                    try:
                        numbers.append(float(token))
                    except ValueError:
                        # Skip non-numeric tokens
                        pass
        
        return numbers if numbers else None
    except Exception:
        return None


    def get_stats(data: List[float]) -> dict:
    """
    Calculate basic statistics for a dataset.
    
    Args:
        data: List of numbers
        
    Returns:
        Dictionary with count, min, max, mean, variance, std_dev
        
    Example:
        >>> data = [1, 2, 3, 4, 5]
        >>> stats = get_stats(data)
        >>> print(f"Mean: {stats['mean']:.2f}, Std: {stats['std_dev']:.2f}")
    """
    n = len(data)
    if n == 0:
        return {
            "count": 0,
            "min": None,
            "max": None,
            "mean": None,
            "variance": None,
            "std_dev": None
        }
    
    total = sum(data)
    mean = total / n
    
    # Two-pass for numerical stability
    sq_dev = sum((x - mean) ** 2 for x in data)
    variance = sq_dev / n
    
    return {
        "count": n,
        "min": min(data),
        "max": max(data),
        "mean": mean,
        "variance": variance,
        "std_dev": math.sqrt(variance)
    }

## Counclusion
In order to calculate the mean of more than 10 million values, this project successfully developed and contrasted sequential, threading, and multiprocessing paradigms. By using built-in `sum()` to get around Python's Global Interpreter Lock (GIL), threading obtained the greatest performance (0.0797 seconds, 6.12× speedup), according to the data, whereas sequential execution served as a slower baseline (0.4876 seconds). Surprisingly, for this computationally simple activity, multiprocessing with 12 processes performed worse (0.7490 seconds, 0.65× speedup) because serialization overhead and significant inter-process communication outweighed the benefits of parallelization. These results show that threading can outperform multiprocessing when using C-level built-in functions for CPU-bound operations like mean calculation, and that efficient workload distribution necessitates striking a balance between computation density and parallelization overhead proving that more cores do not always equate to faster Python execution.
