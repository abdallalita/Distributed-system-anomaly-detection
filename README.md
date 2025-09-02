## Introduction
This project analyzes logs from a distributed system to understand its performance, reliability, and workload behavior. The dataset captures fine-grained system activity, including events, errors, and latency metrics, making it a good source for monitoring and diagnosing infrastructure health.

The goal of this project is to:
- **Track key infrastructure metrics (latency, error/fatal logs, log activity).**
- **Detect anomalies in system behavior using machine learning.**
- **Provide insights that can guide scaling decisions, fault diagnosis, and system optimization.**

---
### Dataset Overview
- Size: 100,000 log entries, 9 columns
- Source: Synthetic distributed system logs
- Time Period: November 20, 2023, 08:40:50 – 09:04:19 (~24 minutes)

### Data Quality Checks
No missing values or duplicate rows were found.

### Exploratory Data Analysis & Monitoring
Why Use Seconds as the Time Granularity?

Although the dataset captures timestamps down to microsecond precision, all time-based analyses were resampled per second. This strikes a balance between detail and readability, reducing microsecond-level noise while still capturing meaningful bursts, anomalies, and latency fluctuations.

**1. Log Activity Over Time with Rolling Average**
This plot shows log count per second along with a 20-second rolling average to smooth short-term fluctuations.

The log activity reflects a healthy, stable system under load, with natural bursts that align with expected workload variation. 

Tracking log activity over time provides a baseline of system behavior and it helps detect when the system is busier than usual (workload spikes).

**2. Log Level Distribution & Service Contribution**
This analysis highlights how logs are distributed across severity levels and which services contribute to overall log volume.

WARNING is the most frequent level, showing the system encounters many non-critical issues that still require attention.

Each service produces a similar volume of logs (~25k each), indicating the logging load is balanced across the distributed system.

Service C logs slightly more than the others, suggesting it is either handling more workload or is logging more aggressively.

**3. Error & Fatal Concentration per Service**
This chart shows the percentage distribution of ERROR and FATAL logs across services.
Each service contributes an almost similar ratio of ERROR vs FATAL logs,suggesting that the failures are not isolated to a single service but are instead a system-wide reliability concern.

The consistently high proportion of these logs indicates critical reliability issues that pose risks of service degradation or outages.

While the dataset contains multiple log levels (INFO, WARNING, ERROR, FATAL), this analysis emphasizes Error and Fatal logs because they provide the strongest signal of reliability and stability risks in a distributed system.
- ERROR indicates failed operations, often recoverable but disruptive.
- FATAL represents the most severe failures, typically unrecoverable without intervention.

**4. Average Latency by Service**
This bar-chart compares the average response latency across services under load.
All services show an average latency above 55 ms. The bars are about the same height, indicating latency is balanced across services but not particularly fast.

Howevwer, the consistently high Latency at this level shows the system is already under noticeable load. If workload increases, latency is likely to rise further, signaling that the system is close to its capacity limit, reflecting a system-wide performance issue, rather than a problem isolated to a single service.

Its a clear signal signal for scaling resources, either use larger machines with more CPU/memory if workload is too heavy for current nodes) or optimizing shared infrastructure eg Network layer, before load increases push the system into instability

**5. Latency Trend Over Time**
This chart shows latency fluctuations over time. We see that, latency does not hold steady but shows sharp rises and sudden drops, including two large dips, indicating moments when requests take significantly longer, sometimes shorter.

Overall, the system response time is unstable and inconsistent, rather than following a smooth trend.

**6. All Messages Frequency**
This chart shows the distribution of all log message types across the system.

The high frequency of warnings indicates that while the system continues to operate, it is doing so under stress, this is often a precursor to more severe failures, providing an early signal of system health

**5. Error & Fatal Message Distribution**
This chart shows the frequency of all ERROR and FATAL log categories across the system.

While multiple error categories are present, the dominance of Database and Critical Errors shows where efforts should focus first. Reducing these categories will likely deliver the biggest improvement in system stability.


### Anomaly Detection on Latency & Error/Fatal Logs
After tracking baseline metrics, an Isolation Forest anomaly detection model with a contamination rate of 1% to flag rare abnormal behavior, was applied to latency and error/fatal log counts.

Instead of relying only on visual inspection of plots, anomaly detection provides a systematic, automated way to identify unusual patterns, giving us an automated way to detect potential incidents before they escalate.

Why these metrics?
- Latency: Fluctuations directly affect user experience, and anomalies highlight unusual performance degradation.
- Error/Fatal Logs: Critical signals of reliability issues, anomalies here capture unusual spikes/drops that may represent incidents.

Points marked as anomalies represent moments when the system deviated significantly from expected behavior, corresponding to latency spikes and Error/logs spikes (around 5 second intervals), indicating early warning signals.






