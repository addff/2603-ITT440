<img width="1882" height="809" alt="LogoUiTM" src="https://github.com/user-attachments/assets/3ba6de3b-4be6-4400-b304-70f445571cdd" />

# 📊 Scalability and Performance Analysis of Web APIs Under Load Using Locust

**Prepared by:** ISYRAQUL WALDAN UMAIRAH BT ABD RAZAK  
**Student ID:** 2025310499  
**Group:** CDCS2555A  
**Prepared for:** SIR SHAHADAN BIN SAAD  

---

## 🖥️ Tools Used
- **Locust** ⚡  
<img width="400" height="96" alt="Locust-logo" src="https://github.com/user-attachments/assets/3b1451ea-9025-427d-b5f7-adb70c6c4dae" />

- **OWASP Juice Shop** 🔒  
<img width="205" height="246" alt="images" src="https://github.com/user-attachments/assets/1072c6d5-c0a1-4961-bec3-0a66f2df35f7" />


Testing Types:  
- Load Test 📌  
- Stress Test 🧪  
- Spike Test ⚡  

Target Application: **OWASP Juice Shop**

---

## 🔗 1.0 Introduction
Performance testing is an important activity in system testing to evaluate how a web application behaves under different traffic conditions. It helps identify system limitations, bottlenecks, response delays, and stability issues before the system is deployed into a production environment.
In this assessment, the performance testing tool used is Locust together with OWASP Juice Shop. Locust is used to simulate user traffic and measure system performance, while OWASP Juice Shop is used to support security-related testing and traffic inspection.

Three types of performance tests were conducted:
- Load Test
- Stress Test
- Spike Test

The purpose of these tests is to evaluate:
- System responsiveness
- Maximum handling capacity
- Stability under heavy traffic
- Recovery capability after sudden traffic spikes

---

## ⚡ 2.0 Tool Selection Justification

### 🖥️ Locust (https://locust.io)
Locust was selected because it is a lightweight and powerful open-source performance testing tool written in Python. It allows testers to simulate thousands of concurrent users with customizable behaviors.

- Lightweight, Python-based, open-source  
- Simulates thousands of concurrent users  
- Real-time monitoring dashboard  
- Distributed load testing support  
- Metrics: RPS, Response Time, Failures, Users  

### 🔒 OWASP Juice Shop (https://owasp.org/www-project-juice-shop/)
OWASP Juice Shop was selected because it is a free and open-source web application security testing tool. It can inspect HTTP traffic and identify security vulnerabilities while monitoring application requests.

- Free, open-source  
- Proxy interception for HTTP traffic  
- Identifies vulnerabilities  
- Integrates with browsers/testing environments  

---

## 📌 3.0 Setup Procedure

### 🐳 Install Juice Shop (Docker)
```bash
sudo apt update
sudo apt install docker.io
sudo systemctl enable --now docker
sudo docker pull bkimminich/juice-shop
sudo docker run -d --name juice -p 3000:3000 bkimminich/juice-shop
```
Access: `http://localhost:3000`

### 🐍 Install Locust
Create a simple test file:
```bash
nano locustfile.py
```
Paste this:
```bash
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 3)

    @task
    def home(self):
        self.client.get("/")
```
Save:

- CTRL + O
- ENTER
- CTRL + X


```bash
sudo apt install python3-locust
locust -f locustfile.py --host=http://localhost:3000
sudo apt install python3-venv -y
```
Create new directory:
```bash
mkdir locust
```
Locate the locust file and activate it:
```bash
cd locust
python3 -m venv venv
source venv/bin/activate
```
Install the locust after activate:
```bash
pip install locust
locust --version
```
Run Locust:  
```bash
locust -H https://192.168.100.22:3000
```
Go to browser and browse: `http://localhost:8089` to see the Dashboard of Locust.

---

## 📊 4.0 Load Test (Normal operating conditions)
Load testing is performed to evaluate how the application behaves under expected normal traffic conditions. The objective is to ensure the system can maintain stable performance during regular usage.

**Test Configuration**
| Parameter	| Value |
|----------|----------|
| Users	| 50 |
| Spawn Rate	| 5 users/sec |
| Duration	| 5 minutes |

**Result/Outcome** 
<img width="902" height="757" alt="Load Test" src="https://github.com/user-attachments/assets/a5f96b8a-30a7-47f7-ad5f-4106b442a2e4" />

Peak RPS:
~25

Avg response time:
~40 ms

95th percentile:
~64 ms

Failures/s:
0

- RPS stabilised quickly at ~25 after ramp-up, with no fluctuation — system handled the load cleanly.
- Average response time settled at ~40 ms and 95th percentile ~64 ms — excellent latency for 50 concurrent users.
- Zero failures throughout the entire test window. The app is stable under normal load.
- A brief spike in 95th percentile (~650 ms) appeared at startup before settling — likely JVM/process warm-up, not a real concern.

✅Pass — application performs well under normal expected load.


---

## 🧪 5.0 Stress Test (Beyond normal capacity)
Stress testing is conducted to determine the maximum limit of the system by gradually increasing traffic beyond normal operating capacity.
The purpose is to observe:
- System degradation
- Performance bottlenecks
- Failure points
- Recovery capability

**Test Configuration**
| Parameter	| Value |
|----------|----------|
| Users	| 500 |
| Spawn Rate	| 50 users/sec |
| Duration	| 10 minutes | 

**Result/Outcome** 
<img width="1090" height="860" alt="Stress Test" src="https://github.com/user-attachments/assets/ba58e7a2-ef3b-4f69-adcd-82428745a23c" />


Peak RPS:
~183

Avg response time:
~3,876 ms

95th percentile:
~18,000 ms

Failures/s:
~0

- Response times degraded severely at startup — 95th percentile hit ~18,000 ms early on, indicating the system was overwhelmed during ramp-up.
- Performance recovered gradually over time — avg response time fell from ~9,000 ms down to ~2,000 ms by the end, showing the system can self-stabilise but takes several minutes.
- No hard failures recorded (Failures/s ≈ 0), meaning the server didn't crash — it just became very slow under sustained 500-user load.
- RPS peaked at ~183 and remained high and variable — the system was processing requests but queuing caused the high latency.

⚠️Warning — system survives but response times are unacceptably high at 500 users. Optimisation or scaling is needed above this threshold.


---

## ⚡ 6.0 Spike Test (Sudden traffic burst & recovery)
Spike testing evaluates how the application reacts to sudden and extreme increases in traffic within a short period.
This test determines whether the system can:
- Handle sudden traffic surges
- Maintain availability
- Recover after traffic returns to normal

**Test Configuration**
| Stage	| Users |
|----------|----------|
| Normal Traffic	| 10 |
| Sudden Spike	| 1000 |
| Recovery | 10 | 

**Result/Outcome** 
<img width="1086" height="856" alt="Spike Test" src="https://github.com/user-attachments/assets/af168e45-2764-4c05-88f8-b3580adb0e5f" />

Peak RPS:
~170

Peak 95th pct:
~480,000 ms

Peak users:
1,000

Failures at peak:
Yes

- At peak load (1,000 users), the 95th percentile response time spiked to ~480,000 ms (~8 minutes) — the system was effectively unresponsive to a large portion of users.
- Failure rate appeared during the spike peak, indicating the system began dropping or timing out requests — a partial failure state.
- During ramp-up to 1,000 users, RPS fluctuated erratically (between ~30–170) suggesting the server was struggling to accept new connections consistently.
- After ramp-down back to ~30 users, RPS stabilised at ~30 and response times returned to near-normal — the application did recover, but slowly.
- The second 95th percentile spike (~480,000 ms) occurred around 15:11 when users were nearly fully ramped down — likely queued requests from the peak finally timing out.

❌Fail — the system cannot handle sudden large spikes. Significant capacity, auto-scaling, or queue management improvements are required.


---


## 📌 8.0 Conclusion & Recommendation

| Test Type   | Result |
|-------------|--------|
| Load Test | Stable ✅ |
| Stress Test | Degradation ⚠️ |
| Spike Test | Instability ❌ |

The performance testing successfully evaluated the application under different traffic conditions using Locust and OWASP ZAP.

1. The Load Test showed that the system can handle normal user traffic with stable response times and minimal failures.
2. During the Stress Test, the application experienced slower response times and performance degradation as the number of users increased significantly.
3. In the Spike Test, sudden traffic surges caused instability and high latency, indicating that the server has limitations when handling abrupt increases in concurrent users.

Overall, the testing demonstrates that the system performs adequately under normal operating conditions but requires optimization for handling very high traffic loads and sudden spikes. Improvements such as server scaling, database optimization, caching mechanisms, and load balancing are recommended to improve system stability and performance under extreme conditions.

## 🎥 9.0 Demonstration Videos
https://youtu.be/n_PpzYxxP8Q
