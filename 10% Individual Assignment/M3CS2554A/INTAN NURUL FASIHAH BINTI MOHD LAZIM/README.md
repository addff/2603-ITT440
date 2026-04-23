# PARALLEL DICE COMBINATION SIMULATOR
# PARALLEL DICE COMBINATION SIMULATOR

**NAME:** INTAN NURUL FASIHAH BINTI MOHD LAZIM

**STUDENT ID:** 2024442972

**CLASS:** M3CS2554A

--------------------------------------------------------------------------------------------------------------------------------
## 📝 INTRODUCTION
The Parallel Dice Combination Simulator is a Python-based application that simulates rolling two dice a large number of times to analyze the frequency of each possible outcome (sum = 2 to 12).

This project demonstrates three execution approaches:

- Sequential execution (single process)
- Concurrent execution using threading
- Parallel execution using multiprocessing

The goal is to compare performance and efficiency when processing large-scale data.
  
  ------------
 ## 🎯 OBJECTIVE
- Simulate large numbers of dice rolls
- Implement sequential processing as baseline
- Apply threading for concurrent execution
- Apply multiprocessing for parallel execution
- Compare execution time between methods
- Analyze distribution of dice outcomes
-----------
## 💻 SYSTEM REQUIREMENT
Hardware Requirements:
- Minumum 4GB RAM
- Multi-core CPU (recommended for multiprocessing)

Software Requirement
- Operating System: Windows
- Python version : 3.8 and above

Required Libraries (Built-in)
- random - for dice simulation
- time - for performance measurement
- threading - for concurrent execution
- multiprocessing - for parallel execution

  --------------
  ## ⚙️ INSTALLATION GUIDE
Step 1: Start Virtual Machine
1. Open VMware Workstation
2. Start your Kali Linux virtual machine
3. Log in to Kali Linux
 
 Step 2: Open Terminal
- Click Terminal or press:
```bash  
Ctrl + Alt + T
```
Step 3: Check Python Installation

Kali usually has Python pre-installed.

Run:

```bash  
python3 --version
```
If not installed:
```bash  
sudo apt update
sudo apt install python3 -y
```

Step 4: Create Project Folder
```bash  
mkdir parallel-dice-simulator
cd parallel-dice-simulator
```
Step 5: Create Python File
```bash  
nano main.py
```
Step 6: Paste Your Code
Copy your Python code
Paste into main.py
Save:

In nano:
- Press ```bash CTRL + X```
- Press ```bashY```
- Press ```bashEnter```

 ## ▶️ 5. How It Runs in Kali Linux

When you run:
```bash python3 main.py```

The program will:

1.Simulate dice rolls
2. Execute:
 - Sequential
 - Threading
 - Multiprocessing
 - Display execution time
 - Show dice combination results

⚠️ Important Notes for Kali Linux
1. Multiprocessing Requirement

Make sure your code includes:
```bash
if __name__ == "__main__":
    main()
```
✔ This is mandatory in Linux & Windows

2. Performance in VM

Running in VMware may be:
- Slower than real machine
- Limited by allocated CPU/RAM
👉 Recommended:
- Allocate at least 2–4 CPU cores
- Allocate 4GB RAM or more
  
3. If Program is Too Slow

Change:
```bash
TOTAL_ROLLS = 1_000_000
```
instead of:
```bash
1_000_000_000
```

-------------------
# 📊 Analytics






-------------------


