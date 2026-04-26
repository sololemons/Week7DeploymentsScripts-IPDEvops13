# kk-payments SLI and SLO Definition

This document defines what kk-payments measures, what it commits to over a 30-day period, and what is explicitly out of scope.

## Scope and Measurement Rules

- Service in scope: kk-payments transaction processing API.
- SLO evaluation window: rolling 30 days.
- Aggregation cadence: metrics are computed in 1-minute buckets, then rolled up for 30-day reporting.
- Request validity rule: malformed requests and expected client 4xx validation failures are **excluded from reliability and payment error calculations**.

## Service Level Indicators (SLIs)

### 1) Availability SLI

- What is measured: percentage of valid requests served without server-side failure.
- Data source: edge proxy access logs.
- Calculation method: ((total valid requests - HTTP 5xx responses) / total valid requests) * 100.
- Measurement window: 1-minute buckets, evaluated over rolling 30 days.

### 2) Latency SLI (p95)

- What is measured: 95th percentile end-to-end response time for valid payment requests.
- Data source: proxy request_time field from access logs.
- Calculation method: compute p95 latency per 1-minute bucket from valid requests, then assess compliance across rolling 30 days.
- Measurement window: 1-minute buckets, evaluated over rolling 30 days.

### 3) Payment Error Rate SLI

- What is measured: percentage of valid payment attempts that fail due to internal processing errors after request acceptance.
- Data source: application transaction event logs and payment state transitions.
- Calculation method: (failed internal payment attempts / total valid payment attempts) * 100.
- Measurement window: 1-minute buckets, evaluated over rolling 30 days.

## Service Level Objectives (SLOs)

All objectives below use a rolling 30-day measurement window.

| SLI | Minimum target percentage | 30-day objective statement |
| --- | --- | --- |
| Availability | 99.90% | At least 99.90% of valid requests complete without 5xx errors. |
| Latency (p95) | 95.00% | At least 95.00% of 1-minute buckets have p95 latency below 800 ms. |
| Payment Error Rate | 99.95% | At least 99.95% of valid payment attempts complete without internal failure. |

## Automated Rollback Thresholds

Short-window thresholds are intentionally tighter than 30-day SLOs so bad releases are stopped before meaningful error-budget burn.

| SLI | 30-day SLO target | Short-window rollback trigger | Relationship between trigger and SLO |
| --- | --- | --- | --- |
| Availability | >= 99.90% | 3 consecutive health checks returning 5xx during deployment monitoring | Three consecutive failures indicate immediate service instability; rolling back early prevents rapid consumption of the monthly availability budget. |
| Latency (p95) | p95 < 800 ms in at least 95.00% of buckets | 3 responses above 2.0 s within 5 deployment polls | Deployment-time repeated high latency is a leading indicator of sustained p95 degradation; early rollback protects the 30-day latency objective. |
| Payment Error Rate | <= 0.05% (or success >= 99.95%) | 2 internal transaction drops within a 120-second burn-in window | Even a small burst of dropped payments can quickly exceed a strict 0.05% error budget; rollback minimizes financial and trust impact. |

## What We Do Not Commit To

1. **Third-party gateway availability** or downstream bank uptime (for example card processor or mobile money provider outages), because these dependencies are outside direct operational control of kk-payments.
2. **User-caused validation failures** and expected business-rule rejections (HTTP 4xx such as invalid card details or insufficient funds), because rejecting invalid input is correct system behavior, not service unreliability.
3. **End-user network quality** from client device to public internet edge, because client ISP or device connectivity conditions are external to platform operation.

