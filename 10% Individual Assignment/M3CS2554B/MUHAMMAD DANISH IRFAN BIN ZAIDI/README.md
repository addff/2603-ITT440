# Monte Carlo Pi Simulation: A Real-Time Comparison of Sequential, Concurrent, and Parallel Execution Models”

## **Name:** Muhammad Danish Irfan Bin Zaidi
## **Subject:** ITT440
## **Group:** CS2554B
## **Date:** May 2026
---

## 📌 Project Overview

This project implements a **Monte Carlo simulation** to estimate the value of π (Pi) using three different execution models:

* Sequential Programming
* Concurrent Programming (Threading)
* Parallel Programming (Multiprocessing)

The goal is to demonstrate how different programming approaches affect **execution time and performance** when processing a large number of computational tasks.

---

## 🎯 Problem Statement

Estimating π using the Monte Carlo method requires generating a very large number of random points and checking whether they fall inside a unit circle. When executed sequentially, this process is computationally expensive and slow.

This project addresses the following problem:

> How can concurrent and parallel programming techniques improve the performance of large-scale Monte Carlo simulations compared to sequential execution?

---

## ⚙️ Features

* Real-time Monte Carlo visualization using GUI
* Comparison of three execution models:

  * Sequential
  * Thread-based concurrency
  * Process-based parallelism
* Performance metrics:

  * Execution time
  * Speedup comparison
  * Accuracy (difference from actual π)
* Live progress tracking using progress bars
* Multi-core CPU utilization

---

## 🧠 Concepts Used

* Monte Carlo Simulation
* Multithreading (`threading`)
* Multiprocessing (`multiprocessing`)
* Inter-process communication (Queue)
* GUI development using `tkinter`

---

## 💻 System Requirements
| Parameter | Details |
|---|---|
| OS| Windows / Linux / macOS  |
| Python Version | Python 3.8 or higher |
| IDE | Visual Studio Code |
| GUI Framework | Tkinter (built-in) |
| Multi-core CPU | Auto-detected via multiprocessing.cpu_count() |
| Execution Modes | Sequential, Threading, Multiprocessing |

---

## 📦 Installation

1.Install Python:
https://www.python.org/downloads/windows/
<img width="1474" height="852" alt="image" src="https://github.com/user-attachments/assets/64a1999e-d4f8-45a6-b6ce-d51b1026d082" />

* Make sure you download the latest version available
2. Install Visual Studio Code:
https://code.visualstudio.com/download

<img width="1797" height="832" alt="image" src="https://github.com/user-attachments/assets/eca64401-c649-441e-a866-9a9c1c69b618" />


Note: If you having trouble to code in visual studio code click [here](https://www.youtube.com/watch?v=6i3e-j3wSf0)

---

## 📂 Source Code

```bash
import random
import multiprocessing
import threading
import time
import tkinter as tk
from tkinter import ttk
import math

# --- SHARED MATH FUNCTION ---
def estimate_portion(num_samples, worker_id, queue):
    hits = 0
    report_at = max(1, num_samples // 40)  # Frequent updates for smooth visuals
    
    for i in range(1, num_samples + 1):
        x, y = random.random(), random.random()
        is_hit = x**2 + y**2 <= 1.0
        if is_hit:
            hits += 1
        
        if i % report_at == 0:
            progress = (i / num_samples) * 100
            # Send progress + a sample point for the visualizer
            queue.put(('PROGRESS', worker_id, progress, (x, y, is_hit)))
            
    return hits

class PiGui:
    def __init__(self, root):
        self.root = root
        self.root.title("Monte Carlo Pi Architectures")
        self.root.geometry("850x600")
        
        self.total_samples = _10_000_000 # Adjust input to see the difference better
        self.cores = multiprocessing.cpu_count()
        self.queue = multiprocessing.Manager().Queue()
        self.results = {}
        self.actual_pi = math.pi

        # --- UI SETUP ---
        left_frame = ttk.Frame(root, padding="20")
        left_frame.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)

        right_frame = ttk.Frame(root, padding="20")
        right_frame.pack(side=tk.RIGHT, fill=tk.Y)

        # Left side: Metrics
        ttk.Label(left_frame, text="Execution Metrics", font=('Helvetica', 14, 'bold')).pack(pady=10)
        
        self.modes = ["Sequential", "Concurrent", "Parallel"]
        self.widgets = {}

        for mode in self.modes:
            frame = ttk.LabelFrame(left_frame, text=mode, padding="10")
            frame.pack(fill=tk.X, pady=5)
            pb = ttk.Progressbar(frame, orient='horizontal', length=300, mode='determinate')
            pb.pack(fill=tk.X)
            lbl = ttk.Label(frame, text="Status: Waiting...", font=("Consolas", 9))
            lbl.pack(anchor=tk.W)
            self.widgets[mode] = {'bar': pb, 'label': lbl}

        self.btn_start = ttk.Button(left_frame, text="Start Comparison", command=self.start_test)
        self.btn_start.pack(pady=10)

        self.result_text = tk.Text(left_frame, height=10, font=('Consolas', 9))
        self.result_text.pack(fill=tk.BOTH, expand=True)

        # Right side: Visualizer
        ttk.Label(right_frame, text="Monte Carlo Visualizer", font=('Helvetica', 12, 'bold')).pack(pady=5)
        self.canvas_size = 300
        self.canvas = tk.Canvas(right_frame, width=self.canvas_size, height=self.canvas_size, bg="white", highlightthickness=2, highlightbackground="#333")
        self.canvas.pack()
        
        # Draw static quarter circle
        self.canvas.create_arc(-self.canvas_size, 0, self.canvas_size, self.canvas_size*2, start=0, extent=90, outline="blue", width=2)
        
        self.live_stat = ttk.Label(right_frame, text="Actual Pi: 3.141592", font=("Consolas", 10))
        self.live_stat.pack(pady=10)

    def update_gui(self):
        """ Checks the queue for progress and points to draw """
        while not self.queue.empty():
            msg = self.queue.get_nowait()
            if msg[0] == 'PROGRESS':
                _, mode, val, point = msg
                # Update Bars
                self.widgets[mode]['bar']['value'] = val
                self.widgets[mode]['label'].config(text=f"Running... {val:.0f}%")
                
                # Draw point (x, y, is_hit)
                px, py, is_hit = point
                cx = px * self.canvas_size
                cy = self.canvas_size - (py * self.canvas_size)
                color = "#e74c3c" if is_hit else "#bdc3c7"
                self.canvas.create_oval(cx-1, cy-1, cx+1, cy+1, fill=color, outline=color, tags="pt")
        
        if self.running:
            self.root.after(30, self.update_gui)

    def log(self, text):
        self.result_text.insert(tk.END, text + "\n")
        self.result_text.see(tk.END)

    def start_test(self):
        self.running = True
        self.btn_start.config(state=tk.DISABLED)
        self.result_text.delete('1.0', tk.END)
        self.canvas.delete("pt")
        self.update_gui()
        threading.Thread(target=self.run_logic, daemon=True).start()

    def run_logic(self):
        samples_per_core = self.total_samples // self.cores
        
        # Helper to summarize
        def finalize(mode, duration, pi):
            diff = abs(self.actual_pi - pi)
            if mode == "Sequential":
                self.seq_time = duration
                speedup = 1.0
            else:
                speedup = self.seq_time / duration if duration > 0 else 0
            
            self.widgets[mode]['label'].config(text=f"Done! {duration:.2f}s | Speedup: {speedup:.2f}x")
            self.log(f"{mode:<12} | {duration:<7.3f}s | {pi:<8.5f} | {diff:<10.7f} | {speedup:.2f}x")
            time.sleep(0.5)

        self.log(f"{'MODE':<12} | {'TIME':<8} | {'PI':<8} | {'DIFF':<10} | {'SPEEDUP'}")
        self.log("-" * 65)

        # 1. SEQUENTIAL
        start = time.time()
        h = estimate_portion(self.total_samples, "Sequential", self.queue)
        finalize("Sequential", time.time() - start, (4 * h) / self.total_samples)

        # 2. CONCURRENT
        start = time.time()
        c_hits = []
        def t_wrap(): c_hits.append(estimate_portion(samples_per_core, "Concurrent", self.queue))
        threads = [threading.Thread(target=t_wrap) for _ in range(self.cores)]
        for t in threads: t.start()
        for t in threads: t.join()
        finalize("Concurrent", time.time() - start, (4 * sum(c_hits)) / self.total_samples)

        # 3. PARALLEL
        start = time.time()
        with multiprocessing.Pool(processes=self.cores) as pool:
            args = [(samples_per_core, "Parallel", self.queue) for _ in range(self.cores)]
            p_hits = pool.starmap(estimate_portion, args)
        finalize("Parallel", time.time() - start, (4 * sum(p_hits)) / self.total_samples)

        self.running = False
        self.btn_start.config(state=tk.NORMAL)

if __name__ == "__main__":
    multiprocessing.freeze_support()
    root = tk.Tk()
    app = PiGui(root)
    root.mainloop()
```

---

## 🖥️ How to run the program

1. Launch Visual Studio Code
2. Create new file 
3. Copy the source code from this post [here](#-source-code) and paste it on the new file
4. Run the program
5. Click **"Start Comparison"**
6. Observe:
   * Progress bars updating in real time
   * Visualization of random points
   * Execution time for each method
7. Results will be displayed in the output panel

---

## 📊 Sample Output
<img width="1050" height="776" alt="Screenshot 2026-05-01 150849" src="https://github.com/user-attachments/assets/6e9359b9-a36f-4a5a-b026-0e43c5492961" />

```
MODE         | TIME     | PI       | DIFF       | SPEEDUP
-----------------------------------------------------------------
Sequential   | 24.176 s | 3.14156  | 0.0000349  | 1.00x
Concurrent   | 25.313 s | 3.14175  | 0.0001533  | 0.96x
Parallel     | 4.114  s | 3.14159  | 0.0000042  | 5.88x

```

---

## 📈 Performance Analysis

| Method     | Description                                                     |
| ---------- | --------------------------------------------------------------- |
| Sequential | Executes tasks one by one, slowest performance                  |
| Concurrent | Uses threads, improves responsiveness but limited by GIL        |
| Parallel   | Uses multiple processes, best performance on multi-core systems |

**Conclusion:**
Parallel programming significantly reduces execution time by distributing tasks across multiple CPU cores.

---

## 🔗 Video Demonstration

Upload your demo video to YouTube and paste the link here:

```
https://youtube.com/your-video-link
```

---

## 📖 Conclusion

This project demonstrates that:

* Sequential execution is inefficient for large-scale computations
* Threading provides moderate improvement
* Multiprocessing offers the best performance due to true parallel execution

The Monte Carlo method serves as an effective example to showcase the benefits of parallel programming in Python.

---

## ⚠️ Notes

* Ensure the program is run on a system with multiple CPU cores to observe clear performance differences
* Large sample sizes may take significant time to complete

---
