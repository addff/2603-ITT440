
# 🚀 ADAPTIVE HYBRID PARALLEL SMART CITY TRAFFIC SIMULATOR
# NAME: MOHAMMAD ALIF DANIAL BIN MOHAMAD ASRI
# STUDENT ID: 2024237606
# CLASS: CS2554A
# COURSE: ITT440 - PARALLEL PROGRAMMING
# LECTURER: SHAHADAN BIN SAAD


📌 INTRODUCTION

In today’s digital environment, urban infrastructure systems are required to 
handle large-scale data efficiently. Traditional sequential programming executes 
tasks one at a time, resulting in significant delays when processing vehicle 
physics updates. This project demonstrates optimization via parallel programming.

📊 PROJECT OVERVIEW

A high-performance traffic monitoring system checking physics logic for 5,000 
vehicle agents. Features classification logic and performance benchmarking.

🎯 OBJECTIVES
* Implement sequential, concurrent, and parallel techniques.
* Process large-scale vehicle datasets efficiently.
* Demonstrate workload distribution across multiple CPU cores.

🏗️ SYSTEM DESIGN
* Vehicle Loader: Generates 5,000 vehicle agent IDs.
* Physics Engine: Performs 10^5 mathematical iterations per agent.
* Flow Classifier: Categorizes results (OPTIMAL, HEAVY, CONGESTED).

▶️ HOW TO RUN
1. Save this code as 'main.py'.
2. Open terminal/command prompt.
3. Run the command: python main.py
4. Select options from the menu to see performance differences.

🖥️ PROGRAM MENU
1. Sequential Execution
2. Threading Execution (Concurrent)
3. Multiprocessing Execution (Parallel)
4. Run All & Compare Performance
5. Exit System

🧪 SAMPLE INPUT
Dataset: 5,000 Vehicle Agents (Internal ID List: 1 to 5,000).
Complexity: 100,000 math operations (sqrt, tan) per ID.

📝 SAMPLE OUTPUT

Vehicle ID: 1     | Status: SUCCESS  | Latency: 0.045s | Flow: OPTIMAL FLOW

Vehicle ID: 2     | Status: SUCCESS  | Latency: 0.125s | Flow: HEAVY TRAFFIC


[MULTIPROCESSING] Total Time: 12.30s

⚡ PERFORMANCE ANALYSIS
Method          | Time (seconds) | Performance
----------------|----------------|------------
Sequential      | ~45.00s        | Very Slow
Threading       | ~47.00s        | Slow
Multiprocessing | ~12.00s        | Fast (Winner)

🎥 DEMONSTRATION VIDEO
Link: (https://youtu.be/nVDNVNwAD1U?si=rkaFErSSRf0NRJQo)

🧩 CHALLENGES FACED
* GIL Limitations: Overcoming the Global Interpreter Lock that slows threading.
* Data Scale: Managing physics updates for 5,000 agents in real-time.
* System Overhead: Balancing the number of processes with available CPU cores.

💡 CONCLUSION

Multiprocessing is the superior choice for CPU-bound simulations, bypasses 
the GIL, and achieves maximum hardware utilization.
