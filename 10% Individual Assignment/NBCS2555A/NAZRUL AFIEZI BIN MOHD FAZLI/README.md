# Reliability and Response Time Analysis of NASA Open API Using Grafana k6

Prepared by: Nazrul Afiezi Bin Mohd Fazli
Student No: 2024533927
Course: ITT440
Tool Used: Grafana k6

---

# 1. Introduction

Application Programming Interface (API) is an important technology used in modern systems and websites to allow communication between applications. API performance is very important because users expect fast response times and stable services.

In this project, performance testing was conducted on the NASA Open API using Grafana k6. The API selected for testing was the Astronomy Picture of the Day (APOD) API provided by NASA.

The purpose of this project is to analyze the reliability and response time of the API under different traffic conditions such as normal traffic, heavy traffic, and sudden traffic spikes. This report presents a performance evaluation of the NASA Open API using Grafana k6.

The selected API endpoint for this project is:

```bash
https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY
```

The objective of this analysis is to evaluate the reliability, response time, and scalability of the NASA Open API under different workload conditions. Grafana k6 was used to simulate virtual users and generate performance metrics through three testing approaches:

* Load Test
* Stress Test
* Spike Test

---

# 2. Objectives

The objectives of this project are:

1. To analyze the response time performance of the NASA Open API.
2. To evaluate the reliability of the API under different traffic conditions.
3. To identify how the API behaves during normal, high, and sudden traffic spikes.
4. To measure request success and failure rates using Grafana k6.

---

# 3. Tools and Technologies

| Component        | Description     |
| ---------------- | --------------- |
| Tool             | Grafana k6      |
| API Tested       | NASA Open API   |
| Endpoint         | /planetary/apod |
| Authentication   | DEMO_KEY        |
| Test Environment | Local Machine   |
| Operating System | Windows         |

Grafana k6 is an open-source performance testing tool widely used for load testing and API reliability analysis. It provides detailed metrics such as response time, throughput, request failure rate, and virtual user performance.

---

# 4. Methodology

Three different testing methods were conducted to evaluate the API performance.

---

## 4.1 Load Test

The load test measures system performance under expected normal traffic conditions.

### Configuration

* 10 Virtual Users (VUs)
* Test duration: 30 seconds
* Continuous looping requests

### Purpose

To determine whether the API can handle regular user traffic while maintaining stable response times and successful request handling.

### Load Test k6 Script

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  vus: 10,
  duration: '30s',
};

export default function () {
  const res = http.get('https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY');

  check(res, {
    'status is 200': (r) => r.status === 200,
  });

  sleep(1);
}
```

### Run Command

```bash
k6 run load-test.js
```

---

## 4.2 Stress Test

The stress test evaluates system behavior when the workload exceeds normal operating conditions.

### Configuration

* Up to 100 Virtual Users (VUs)
* Test duration: 2 minutes
* Gradually increasing load

### Purpose

To identify system limitations, performance degradation, and stability issues under heavy traffic.

### Stress Test k6 Script

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 20 },
    { duration: '30s', target: 50 },
    { duration: '30s', target: 100 },
    { duration: '30s', target: 0 },
  ],
};

export default function () {
  const res = http.get('https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY');

  check(res, {
    'status is 200': (r) => r.status === 200,
  });

  sleep(1);
}
```

### Run Command

```bash
k6 run stress-test.js
```

---

## 4.3 Spike Test

The spike test analyzes system performance during sudden increases in traffic.

### Configuration

* Up to 200 Virtual Users (VUs)
* Test duration: 50 seconds
* Sudden traffic spike

### Purpose

To determine how quickly the API responds and recovers during unexpected traffic surges.

### Spike Test k6 Script

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '10s', target: 200 },
    { duration: '30s', target: 200 },
    { duration: '10s', target: 0 },
  ],
};

export default function () {
  const res = http.get('https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY');

  check(res, {
    'status is 200': (r) => r.status === 200,
  });

  sleep(1);
}
```

### Run Command

```bash
k6 run spike-test.js
```

---

# 5. Test Results and Analysis

---

## 5.1 Load Test Results

### Screenshot of Load Test Result

<img width="601" height="665" alt="image" src="https://github.com/user-attachments/assets/eea57ae6-5d6c-4d69-ae82-866964069acf" />

### Observed Metrics

| Metric                | Result     |
| --------------------- | ---------- |
| Total Requests        | 250        |
| HTTP Requests/sec     | 8.04 req/s |
| Average Response Time | 201.97 ms  |
| Minimum Response Time | 193.94 ms  |
| Maximum Response Time | 369.91 ms  |
| 90th Percentile       | 199.49 ms  |
| 95th Percentile       | 232.44 ms  |
| Failed Requests       | 94.80%     |
| Successful Requests   | 5.20%      |

### Analysis

The load test result shows that the API response time was still stable during normal traffic conditions. The average response time was around 202 ms, which can be considered fast and acceptable for API communication.

However, the request failure rate was significantly high, with 94.80% failed requests. The output indicates that many responses returned HTTP status code 200 checks as failed, which may be caused by rate limiting or restrictions associated with the NASA DEMO_KEY. Despite the stable response time, the reliability of the API under continuous requests was poor.

---

## 5.2 Stress Test Results

### Screenshot of Stress Test Result

<img width="713" height="597" alt="image" src="https://github.com/user-attachments/assets/e7636c24-cf7d-4381-b58c-3816069200e9" />

### Observed Metrics

| Metric                | Result      |
| --------------------- | ----------- |
| Total Requests        | 4247        |
| HTTP Requests/sec     | 35.17 req/s |
| Average Response Time | 197.47 ms   |
| Minimum Response Time | 192.67 ms   |
| Maximum Response Time | 219.3 ms    |
| 90th Percentile       | 200.43 ms   |
| 95th Percentile       | 202.26 ms   |
| Failed Requests       | 100%        |
| Successful Requests   | 0%          |

### Analysis

During the stress test, the API received a much higher number of requests with up to 100 virtual users. The average response time was still consistent at around 197 ms.

Although the response speed was stable, the API failed all requests during the stress test. This indicates that the NASA API endpoint using the DEMO_KEY could not support high traffic levels. The results suggest that rate limiting or server-side access restrictions were triggered under heavy load.

The stress test demonstrates that while the server still responded quickly, reliability significantly decreased when the traffic exceeded normal operating conditions.

---

## 5.3 Spike Test Results

### Screenshot of Spike Test Result

<img width="708" height="613" alt="image" src="https://github.com/user-attachments/assets/d7cd39ab-cd30-4ff5-a84c-2b60759aed95" />

### Observed Metrics

| Metric                | Result       |
| --------------------- | ------------ |
| Total Requests        | 5133         |
| HTTP Requests/sec     | 100.46 req/s |
| Average Response Time | 197.38 ms    |
| Minimum Response Time | 192.08 ms    |
| Maximum Response Time | 218.79 ms    |
| 90th Percentile       | 200.01 ms    |
| 95th Percentile       | 201.25 ms    |
| Failed Requests       | 100%         |
| Successful Requests   | 0%           |

### Analysis

The spike test simulated a sudden increase in traffic with 200 virtual users. Even though the traffic increased suddenly, the API response time remained stable at around 197 ms.

However, similar to the stress test, all requests failed. This result strongly indicates that the NASA DEMO_KEY has strict request limitations that prevent successful processing during rapid traffic increases.

The spike test reveals that the API server can still generate responses quickly, but it lacks the ability to maintain successful request processing under sudden high-load conditions.
---

# 6. Comparative Analysis

| Metric               | Load Test | Stress Test | Spike Test |
| -------------------- | --------- | ----------- | ---------- |
| Max VUs              | 10        | 100         | 200        |
| Total Requests       | 250       | 4247        | 5133       |
| Avg Response Time    | 201.97 ms | 197.47 ms   | 197.38 ms  |
| Max Response Time    | 369.91 ms | 219.3 ms    | 218.79 ms  |
| Request Failure Rate | 94.80%    | 100%        | 100%       |
| Requests/sec         | 8.04      | 35.17       | 100.46     |

## Discussion

The comparative analysis shows that the response time remained relatively stable across all testing scenarios. Even under increased traffic, the API server responded within approximately 200 ms.

However, reliability became a major issue as traffic increased. The failure rate escalated from 94.80% during the load test to 100% during both stress and spike tests.

This behavior strongly suggests that the NASA API DEMO_KEY imposes request limitations that affect successful request processing rather than server responsiveness.

---

# 7. Findings

The following findings were identified from the performance analysis:

1. The NASA Open API provides stable response times under various traffic conditions.
2. Average response times remained below 250 ms in all tests.
3. The API reliability decreased significantly under high traffic.
4. Stress and spike testing resulted in complete request failure.
5. The DEMO_KEY likely includes strict rate-limiting policies.
6. The API infrastructure appears responsive but restricts excessive request volumes.

---

# 8. Limitations

Several limitations affected the testing process:

* The NASA DEMO_KEY has public usage limitations.
* Testing was conducted in a local environment.
* Only a single API endpoint was evaluated.
* Network conditions may influence response time measurements.

Future work may include testing with a private API key, distributed load generation, and monitoring server metrics using Grafana dashboards.

---

# 9. Recommendations

Based on the analysis, the following recommendations are proposed:

1. Use a dedicated NASA API key instead of DEMO_KEY for large-scale testing.
2. Implement retry mechanisms and request throttling in client applications.
3. Monitor API rate limits before conducting stress tests.
4. Use distributed testing environments for more accurate scalability analysis.
5. Integrate Grafana dashboards for real-time visualization and monitoring.

---

# 10. Conclusion

In conclusion, this project successfully analyzed the performance of the NASA Open API using Grafana k6. Three different testing methods were used, which are load test, stress test, and spike test.

Based on the results, the API showed stable response times in all testing conditions. However, the request failure rate increased when the number of users became higher. This happened mainly because the NASA DEMO_KEY has request limitations.

Overall, Grafana k6 is a useful tool for API performance testing because it can measure response time, request handling, and API reliability under different traffic conditions.

The results showed that the API maintained stable response times even during heavy traffic conditions. However, reliability significantly decreased as the workload increased, primarily due to request failures associated with the NASA DEMO_KEY limitations.

Overall, the study demonstrates the importance of performance testing in evaluating API scalability, responsiveness, and reliability. Grafana k6 proved to be an effective tool for identifying API behavior under various workload scenarios.

---
i.nasa.gov/planetary/apod?api_key=DEMO_KEY
```
