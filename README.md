# 2603-ITT440
## Python Parallel Programming
Director Choice

[Parallel Stock Market Price Simulator & Analyzer](https://github.com/addff/2603-ITT440/tree/main/10%25%20Individual%20Assignment/M3CS2554A/AHMAD%20ZARIF%20BIN%20AHMAD%20KHIR) ([Video](https://www.youtube.com/watch?v=NUmkmH9TUqg)) 
[Adaptive Hybrid Parallel Web Health Monitoring System](https://github.com/addff/2603-ITT440/tree/main/10%25%20Individual%20Assignment/M3CS2554A/MASTURA%20BINTI%20RABAEE) ([Video](https://www.youtube.com/watch?v=Pu8BO5MWfwo)]
## Python Docker Container
Why Using Docker for Python is Brilliant
***No More "But it works on my machine!"***
We’ve all been there. Your laptop runs Python 3.12, the production server runs Python 3.8, and your colleague is on Windows whilst you are on a Mac. When you deploy, everything breaks. Docker solves this by packaging your OS, Python version, and dependencies into a single container. If it runs on your machine, it will run exactly the same way anywhere else.

***Keeps Your Local Machine Clean***
If you are juggling an older project that requires Python 3.7 and a brand-new one using Python 3.13, managing your local environment can become a bit of a nightmare. With Docker, you don't need to clog up your hard drive with multiple local versions. You simply change the image tag (e.g., python:3.7-slim vs python:3.13-slim) and your host machine stays completely pristine.

***Seamless Integration with Databases***
If your Python application relies on a database like MySQL, PostgreSQL or Redis, you don’t need to go through the faff of installing them locally. Using docker-compose, you can spin up your Python app alongside its database with a single, straightforward command.

### Easy Way
You can skip docker pull if you want
```
sudo docker pull python:3.14
```
Temporary container
```
sudo docker run --rm -p 8888:8888 -v ./:/kelassir --name sir1 python:3.14 tail -f /dev/null
```
the command tail -f /dev/null is a trick to make your container is always run until you issue the docker stop and docker rm.
```
sudo docker exec -it sir1 bash
```

### Jupyter-Notebook
In Python container you can install Jupyter Notebook
```
pip install jupyterlab
```
Run as server. No more localhost.
```
jupyter lab --allow-root --no-browser --ip=0.0.0.0 --port=8888
```

If you want to skip using Python container and pull existing Jupyterlab from hub.
```
sudo docker pull jupyter/scipy-notebook
```
Now run jupyter-lab container
```
sudo docker run -p 8889:8888 -v "$(pwd)":/kelassir jupyter/scipy-notebook
```
### Video
[![Watch YouTube](https://img.youtube.com/vi/Ui98GXWVk6Q/0.jpg)](https://www.youtube.com/watch?v=Ui98GXWVk6Q)

## Socket Programming using Python
***Simple Python UDP Time Server***
Here is a simple, lightweight UDP time server in Python.

Unlike TCP, UDP is connectionless, meaning the server doesn't need to "accept" a connection. It just sits there, listens for an incoming packet (even an empty one), and immediately replies with the current time.
```
import socket
import datetime

# Define the server IP and port
UDP_IP = "127.0.0.1"  # Localhost
UDP_PORT = 12345

# 1. Create a UDP socket (SOCK_DGRAM specifies UDP)
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# 2. Bind the socket to the IP and port
server_socket.bind((UDP_IP, UDP_PORT))

print(f"UDP Time Server is running on {UDP_IP}:{UDP_PORT}...")

while True:
    # 3. Wait for an incoming message (data, client_address)
    # 1024 is the buffer size in bytes
    data, address = server_socket.recvfrom(1024)
    
    print(f"Received request from {address}")
    
    # 4. Get the current time and format it as a string
    current_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    
    # 5. Send the time back to the client (must be encoded to bytes)
    server_socket.sendto(current_time.encode('utf-8'), address)
```
```
import socket

UDP_IP = "127.0.0.1"
UDP_PORT = 12345

# Create a UDP socket
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

try:
    # Send an empty message to trigger the server
    print("Requesting time from server...")
    client_socket.sendto(b"", (UDP_IP, UDP_PORT))
    
    # Receive the response from the server
    data, server = client_socket.recvfrom(1024)
    
    print(f"Server Time: {data.decode('utf-8')}")

finally:
    # Close the socket
    client_socket.close()
```
## Parallel Programming using Python
In short, sequential processing executes tasks strictly one after the other on a single CPU core, whereas concurrency is about managing multiple tasks at once by rapidly switching between them (ideal for waiting on web requests). Parallelism, on the other hand, is about executing multiple tasks truly simultaneously, which requires a multi-core CPU to handle heavy mathematical or data processing. A thread is simply the smallest unit of execution (like an individual worker) within a program; multiple threads can run concurrently to keep your application fast and responsive without needing extra CPU cores.

### Sequential vs Parallel example
**Sequential Execution**
The program executes the classes strictly one after another Square --> Cube --> Fourth.
The terminal output is clean, ordered, and perfectly predictable. It uses minimal system resources.
It takes longer to complete because the next class must wait for the previous one to finish all 10,000, 000 loops.
```
import math


# --- Fungsi Pembantu untuk Semak Nombor Perdana ---
def is_prime(n):
    if n <= 1:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False  # Elakkan nombor genap lain

    # Semak pembahagi ganjil sehingga punca kuasa dua n
    for i in range(3, int(math.sqrt(n)) + 1, 2):
        if n % i == 0:
            return False
    return True


# --- Class-Class Utama ---
class SquarePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        print("--- Memulakan Kuasa Dua Nombor Perdana ---")
        for x in range(self.limit):
            if is_prime(x):
                print(f"Square Prime: {x}^2 = {x**2}")


class CubePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        print("\n--- Memulakan Kuasa Tiga Nombor Perdana ---")
        for x in range(self.limit):
            if is_prime(x):
                print(f"Cube Prime:   {x}^3 = {x**3}")


class FourthPowerPrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        print("\n--- Memulakan Kuasa Empat Nombor Perdana ---")
        for x in range(self.limit):
            if is_prime(x):
                print(f"Fourth Prime: {x}^4 = {x**4}")


# --- Sequential Execution ---
if __name__ == "__main__":
    # Cetus objek
    squarer = SquarePrinter()
    cuber = CubePrinter()
    fourther = FourthPowerPrinter()

    print("Memulakan pengiraan secara sequential (berperingkat)...\n")

    # Jalankan satu persatu mengikut turutan
    squarer.run()  # Selesai ini baru masuk baris bawah
    cuber.run()  # Selesai ini baru masuk baris bawah
    fourther.run()

    print("\nSemua pengiraan selesai mengikut urutan!")
```

***Multiprocessing - Multi-threading***
It runs all three classes within the same process using multiple threads.
```
import threading
import math


# --- Fungsi Pembantu untuk Semak Nombor Perdana ---
def is_prime(n):
    if n <= 1:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False

    for i in range(3, int(math.sqrt(n)) + 1, 2):
        if n % i == 0:
            return False
    return True


# --- Class-Class Utama ---
class SquarePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        for x in range(self.limit):
            if is_prime(x):
                # Ditambah label [Thread-1] untuk mudahkan anda nampak perbezaan di terminal
                print(f"[Thread-1] Square Prime: {x}^2 = {x**2}")


class CubePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"[Thread-2] Cube Prime:   {x}^3 = {x**3}")


class FourthPowerPrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"[Thread-3] Fourth Prime: {x}^4 = {x**4}")


# --- Multi-threading Execution ---
if __name__ == "__main__":
    # Cetus objek
    squarer = SquarePrinter()
    cuber = CubePrinter()
    fourther = FourthPowerPrinter()

    print(
        "Memulakan pengiraan nombor perdana menggunakan Multi-threading...\n"
    )

    # Cipta 3 thread berbeza untuk setiap class
    thread1 = threading.Thread(target=squarer.run)
    thread2 = threading.Thread(target=cuber.run)
    thread3 = threading.Thread(target=fourther.run)

    # Jalankan ketiga-tiga thread secara serentak
    thread1.start()
    thread2.start()
    thread3.start()

    # Tunggu sehingga kesemua thread selesai barulah program utama ditutup
    thread1.join()
    thread2.join()
    thread3.join()

    print("\nSemua kerja thread telah selesai!")
```

***Multiprocessing - Parallelism***
How it works: It spawns three entirely separate Python processes, running each class on a different CPU core simultaneously

```
import multiprocessing
import math


# --- Fungsi Pembantu untuk Semak Nombor Perdana ---
def is_prime(n):
    if n <= 1:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False  # Elakkan nombor genap lain

    # Semak pembahagi ganjil sehingga punca kuasa dua n
    for i in range(3, int(math.sqrt(n)) + 1, 2):
        if n % i == 0:
            return False
    return True


# --- Class-Class Utama ---
class SquarePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"Square Prime: {x}^2 = {x**2}")


class CubePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"Cube Prime:   {x}^3 = {x**3}")


class FourthPowerPrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"Fourth Prime: {x}^4 = {x**4}")


# --- Parallel Execution ---
if __name__ == "__main__":
    # Cetus objek
    squarer = SquarePrinter()
    cuber = CubePrinter()
    fourther = FourthPowerPrinter()

    print("Memulakan proses kuasakan nombor perdana secara parallel...\n")

    # Sediakan proses
    p1 = multiprocessing.Process(target=squarer.run)
    p2 = multiprocessing.Process(target=cuber.run)
    p3 = multiprocessing.Process(target=fourther.run)

    # Jalan serentak
    p1.start()
    p2.start()
    p3.start()

    # Tunggu selesai
    p1.join()
    p2.join()
    p3.join()

    print("\nSemua pengiraan nombor perdana selesai!")
```
***Asyncio - Concurrency***
How it works: It runs on a single CPU core using an event loop, explicitly yielding control (await) to switch between classes.
```
import asyncio
import math


# --- Fungsi Pembantu untuk Semak Nombor Perdana ---
def is_prime(n):
    if n <= 1:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False

    for i in range(3, int(math.sqrt(n)) + 1, 2):
        if n % i == 0:
            return False
    return True


# --- Class-Class Utama ---
class SquarePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    async def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"[Async-Task 1] Square Prime: {x}^2 = {x**2}")

            # Setiap 50 nombor, kita serah giliran (yield) kepada event loop
            if x % 50 == 0:
                await asyncio.sleep(0)


class CubePrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    async def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"[Async-Task 2] Cube Prime:   {x}^3 = {x**3}")

            if x % 50 == 0:
                await asyncio.sleep(0)


class FourthPowerPrinter:

    def __init__(self, limit=10000001):
        self.limit = limit

    async def run(self):
        for x in range(self.limit):
            if is_prime(x):
                print(f"[Async-Task 3] Fourth Prime: {x}^4 = {x**4}")

            if x % 50 == 0:
                await asyncio.sleep(0)


# --- Pengurusan Event Loop ---
async def main():
    # Cetus objek
    squarer = SquarePrinter()
    cuber = CubePrinter()
    fourther = FourthPowerPrinter()

    print("Memulakan pengiraan nombor perdana menggunakan Asyncio...\n")

    # asyncio.gather akan mendaftarkan ketiga-tiga coroutine ke dalam event loop
    # dan menjalankan mereka secara konfuren (bergilir-gilir dengan sangat pantas)
    await asyncio.gather(squarer.run(), cuber.run(), fourther.run())

    print("\nSemua tugasan Asyncio telah selesai!")

if __name__ == "__main__":
    # Jalankan function main() di dalam persekitaran async
    asyncio.run(main())
```

### Video
[![Watch YouTube Video](https://img.youtube.com/vi/xsHDrERoNRw/0.jpg)](https://www.youtube.com/watch?v=xsHDrERoNRw)
