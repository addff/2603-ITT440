# MOHD SYAHRUL AZHAM BIN MOHD SHAFIE
# ADAPTIVE LOAD AND RELIABILITY ANALYSIS OF WEATHER FORECAST API USING DYNAMIC REQUESTS WITH GRAFANA K6

# ITT440: Individual Assignment Report  
**Course:** ITT440 - Network Programming  
**Group:** NBCS2555B   
**Name:** Mohd Syahrul Azham Bin Mohd Shafie   
**Student ID:** 2024358831   
**Lecturer:** Sir Shahadan Bin Saad  


# 1. Introduction<br />
Performance testing is a critical aspect of evaluating the scalability, responsiveness, and stability of modern web services. APIs (Application Programming Interfaces) are widely used to deliver data efficiently, making their performance under concurrent load an important area of study.
This project focuses on analyzing the performance of a public weather API, specifically the Open-Meteo API. Unlike conventional testing approaches that rely on static requests, this study incorporates dynamic request parameters to better simulate real-world user behavior.
The main objectives of this study are:<br />
•	To evaluate API performance under increasing user load <br />
•	To analyze system behavior under sudden traffic spikes <br />
•	To assess long-term stability through endurance testing <br />
<br />
# 2. Methodology
<br />
2.1 Tools Used<br />
    •	Grafana k6 <img width="89" height="73" alt="image" src="https://github.com/user-attachments/assets/6d7a3690-354e-4113-95aa-5d0e5dc21015" />

<br />
2.2 Target API
    •	https://api.open-meteo.com/v1/forecast
<br />
2.3 Testing Approach
<br />
This project implements three types of performance tests:
<br />

| Test | Type	Description |
|---------------------|------------------------------|
| Load Test (Ramp-Up)	| Gradual increase in users |
| Burst Spike Test | Sudden increase in traffic |
| Endurance Soak Test	| Long-duration stability test |
<br />
2.4 Dynamic Request Design
<br />
  To simulate realistic usage, the API requests include randomly generated latitude and longitude values:

    let lat = 3 + Math.random();
    let lon = 101 + Math.random();

  This ensures that each request interacts with different data, mimicking real-world user scenarios.
<br />
<br />
# 3. Test Design & Coding
<br />
   3.1 Load Test (Ramp-Up)
<br />
  •	Start: 0 users 
<br />
  •	Peak: 50 users 
<br />
  •	Duration: 2 minutes 
<br />
  •	Analyze performance as traffic gradually increases 


    import http from 'k6/http';
    import { sleep } from 'k6';
    export const options = {
      stages: [
              { duration: '30s', target: 20 },
              { duration: '1m', target: 50 },
              { duration: '30s', target: 0 },
              ],
    };

    export default function () {
      let lat = 3 + Math.random();
      let lon = 101 + Math.random();
      let url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&hourly=temperature_2m`;
      http.get(url);
      sleep(1);
    }
<br />
<br />
3.2 Burst Spike Test
<br />
•	Sudden jump from 10 to 200 users 
<br />
•	Evaluate system behavior under sudden load 

    import http from 'k6/http';
    import { sleep } from 'k6';
    export const options = {
    stages: [
        { duration: '10s', target: 10 },  
        { duration: '10s', target: 200 },  
        { duration: '10s', target: 10 },   
    ],
    };
    export default function () {
    let lat = 3 + Math.random();
    let lon = 101 + Math.random();
    http.get(`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&hourly=temperature_2m`);
    sleep(1);
    }
<br />
<br />
3.3 Endurance Soak Test
<br />
•	50 users for 5 minutes 
<br />
•	Detect performance degradation over time 

    import http from 'k6/http';
    import { sleep } from 'k6';
    export const options = {
    vus: 50,
    duration: '5m',   
    };
    export default function () {
    let lat = 3 + Math.random();
    let lon = 101 + Math.random();
    http.get(`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&hourly=temperature_2m`);
    sleep(1);
    }
<br />
<br />

#  4. Results

<br />
<br />
4.1 Ramp-Up Load Test Result
<br />
  •	Average Response Time: 401.39 ms 
<br />
  •	Median Response Time: 248.75 ms 
<br />
  •	Max Response Time: 5.83 s 
<br />
  •	95th Percentile: 1.19 s 
<br />
  •	Total Requests: 2228 
<br />
  •	Request Rate: 18.36 req/s 
<br />
  •	Error Rate: 48.51% (1081 failed requests)
<br />
<img width="838" height="603" alt="image" src="https://github.com/user-attachments/assets/9cb22ce6-306f-47a2-b079-b6b7858f2089" />

<br />
<br />
  4.2 Burst Spike Test Result
<br />
  •	Average Response Time: 233.17 ms 
<br />
  •	Median Response Time: 186.45 ms 
<br />
  •	Max Response Time: 4.07 s 
<br />
  •	95th Percentile: 409.11 ms 
<br />
  •	Total Requests: 1754 
<br />
  •	Throughput: 56.42 requests/sec 
<br />
  •	Error Rate: 64.65% (1134 failed requests)
  <br />
<br />
<img width="975" height="686" alt="image" src="https://github.com/user-attachments/assets/419ec2ee-53f1-4ad5-8fa1-d99061117cc8" />
<br />
<br />
4.3 Endurance Soak Test Result
<br />
  •	Average Response Time: 219.41 ms 
<br />
  •	Median Response Time: 183.7 ms 
<br />
  •	Max Response Time: 12.77 s 
<br />
  •	95th Percentile: 263.41 ms 
<br />
  •	Total Requests: 12,253 
<br />
  •	Throughput: 40.68 requests/sec 
<br />
  •	Error Rate: 72.92% (8936 failed requests) 

<img width="975" height="658" alt="image" src="https://github.com/user-attachments/assets/ac839b72-a9b1-4b0d-81f2-a22c4a3e4b0a" />
<br />
<br />

#  5. Discussion

The performance evaluation of the Open-Meteo API under dynamic request conditions reveals several critical insights regarding system behavior under varying load scenarios.
During the ramp-up load test, the system demonstrated moderate performance with an average response time of approximately 401 ms. However, the relatively high error rate of 48.51% indicates that even under gradually increasing load, the API begins to experience instability. This suggests that the system has limited scalability and struggles to maintain consistent performance as the number of concurrent users increases.
In the spike test, the system exhibited a more severe degradation in reliability. Although the average response time appeared lower at 233 ms, this metric alone does not reflect the true system performance. The error rate significantly increased to 64.65%, indicating that the majority of requests were unsuccessful during the sudden surge in traffic. This highlights the system’s inability to handle abrupt increases in load, likely due to server-side limitations such as rate limiting, request throttling, or insufficient resource allocation.
The soak test further exposed the system’s limitations under prolonged usage. While the average response time remained relatively stable at 219 ms, the maximum response time reached 12.77 seconds, indicating severe latency spikes over time. More importantly, the error rate escalated to 72.92%, demonstrating a critical failure in maintaining reliability under sustained load conditions. This behavior suggests possible resource exhaustion, connection saturation, or backend processing inefficiencies.
A key strength of this study is the implementation of dynamic request parameters. By varying latitude and longitude values in each request, the test effectively simulates real-world user behavior. However, this also increases processing complexity on the server side, which may contribute to the high failure rates observed across all test scenarios.
Overall, the results indicate that while the API is capable of handling low to moderate traffic, it fails to scale effectively under both sudden spikes and prolonged load. The consistently high error rates across all test scenarios highlight a significant performance bottleneck, particularly in terms of reliability and scalability.
