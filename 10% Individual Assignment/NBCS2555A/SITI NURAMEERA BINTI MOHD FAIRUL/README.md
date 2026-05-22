# SITI NURAMEERA BINTI MOHD FAIRUL
# Comprehensive Performance Testing and Bottleneck Analysis of SpaceX REST API Using Grafana k6

## Introduction

This project focuses on performance testing of the SpaceX REST API using Grafana k6. The purpose of this project is to evaluate the API performance under different traffic conditions including load testing, stress testing, and spike testing.

## Target API

https://api.spacexdata.com/v4/launches/latest

## Testing Tool

Grafana k6

## Test Types

- Load Test
- Stress Test
- Spike Test
  
## Hypothesis

The hypothesis of this project is that the SpaceX REST API can handle moderate user traffic efficiently during normal load conditions. However, under extreme concurrent user requests during stress and spike testing the API may experience increased response time, reduced throughput and possible request failures.

---

## Test Environment

- Operating System: Kali Linux
- Performance Testing Tool: Grafana k6
- Target API: https://api.spacexdata.com/v4/launches/latest
- Internet Connection: Stable broadband connection

---

## Repository Structure

```text
scripts/       -> Contains k6 testing scripts
screenshots/   -> Contains test result screenshots
results/       -> Contains raw output files
```
---

## Load Test

The load test was performed to evaluate the API performance under normal expected traffic conditions.

### Load Test Script

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  vus: 20,
  duration: '30s',
};

export default function () {
  const res = http.get('https://api.spacexdata.com/v4/launches/latest');

  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });

  sleep(1);
}
```

### Load Test Result

<img width="755" height="563" alt="image" src="https://github.com/user-attachments/assets/8794657d-0d1f-4e94-9a58-94b62faed371" />


### Analysis

The load test demonstrated stable API performance under normal traffic conditions. The average response time remained low throughout the testing duration and all requests were successfully processed without significant errors. This indicates that the SpaceX REST API is capable of handling moderate concurrent user traffic efficiently.

---

## Stress Test

The stress test was conducted to determine the breaking point of the API under high user load.

### Stress Test Script

```javascript
import http from 'k6/http';

export const options = {
  stages: [
    { duration: '30s', target: 50 },
    { duration: '30s', target: 100 },
    { duration: '30s', target: 200 },
    { duration: '30s', target: 0 },
  ],
};

export default function () {
  http.get('https://api.spacexdata.com/v4/launches/latest');
}
```

### Stress Test Result

<img width="965" height="386" alt="image" src="https://github.com/user-attachments/assets/1ebc67cc-5970-4621-90be-5ebcbe2ad9c0" />


### Analysis

The stress test revealed a noticeable increase in response time as the number of concurrent virtual users increased. Under heavier traffic conditions the API performance became less stable and some requests experienced delays. This suggests that the application may face scalability limitations when operating under extremely high workloads.

---

## Spike Test

The spike test evaluated the API behavior during sudden traffic increases.

### Spike Test Script

```javascript
import http from 'k6/http';

export const options = {
  stages: [
    { duration: '10s', target: 20 },
    { duration: '10s', target: 300 },
    { duration: '20s', target: 300 },
    { duration: '10s', target: 20 },
  ],
};

export default function () {
  http.get('https://api.spacexdata.com/v4/launches/latest');
}
```

### Spike Test Result

<img width="918" height="391" alt="image" src="https://github.com/user-attachments/assets/c314c483-cfc4-45e9-a48b-51638fa4c216" />


### Analysis

The spike test showed that sudden traffic surges affected the consistency of the API response time. During rapid increases in concurrent users, temporary latency spikes were observed. However, the API was still able to recover after the traffic stabilized indicating moderate resilience against abrupt traffic changes.

---

## Performance Metrics Summary

| Test Type | Virtual Users | Duration | Performance Observation |
|---|---|---|---|
| Load Test | 20 VUs | 30s | Stable performance under moderate traffic |
| Stress Test | Up to 200 VUs | 120s | Increased response time under heavy load |
| Spike Test | Spike to 300 VUs | 50s | Temporary latency spike during sudden traffic increase |

---

## Video Demonstration
(link youtube)

---

## Bottleneck Analysis

Several performance bottlenecks were identified during the testing process. The primary bottleneck observed was increased response latency during high concurrent traffic conditions. This behavior may be caused by server-side resource limitations, network congestion and API rate limiting mechanisms. The stress and spike tests demonstrated that the application performance degrades as user load increases significantly.

---

## Recommendations

Based on the testing results, several improvements are recommended to enhance the API performance and scalability:

- Implement load balancing to distribute incoming traffic efficiently.
- Introduce caching mechanisms to reduce repeated processing requests.
- Optimize backend server resource management.
- Improve scalability configurations to handle sudden traffic spikes more effectively.

---

## Conclusion

This project successfully demonstrated the use of Grafana k6 for conducting performance testing on the SpaceX REST API. The load, stress, and spike tests provided valuable insights into the API performance behavior under different traffic conditions. Overall, the API performed well under moderate load but showed performance degradation during extreme traffic conditions.
