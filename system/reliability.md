# Validation, resilience and fraud

| Concern | What we do | Owner |
|---|---|---|
| Input validation | Validate request bodies at the gateway and in each service, and reject unknown fields | Everyone |
| Capacity is real | The agent benchmarks CPU, RAM, disk and network on join. Only measured CU counts, not claimed CU | B |
| Fraud | Random spot-check jobs with known answers, heartbeat consistency checks, one machine ID per hardware fingerprint, a trust score, quarantine with payouts on hold | B, D |
| Overcommit | Reserve CU before scheduling, and never place more than the verified CU | B, C |
| Fault tolerance | Open pool deployments always run 2 or more replicas on different providers. Health checks run every few seconds, and lost replicas are rescheduled within a minute | C |
| Calls between services | Timeouts, retries with backoff only on idempotent calls, idempotency keys on holds and charges, an outbox for events | Everyone, D for charges |
| Capacity planning | Available, reserved and used CU per pool. Alert under 20% headroom, and reject deployments that don't fit | B, D |
| Monitoring | Grafana dashboards per service, alerts on errors, machine churn and low credits | D |
| Logs | Service logs and deployment logs in one place, searchable by request ID and by deployment | D, C |

Next: [Dashboard](dashboard.md)
