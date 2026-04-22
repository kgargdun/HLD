### Capacity → Design Decision Cheat Sheet

| Signal / Metric        | Threshold (rough) | What to do |
|-----------------------|------------------|------------|
| Read vs Write ratio   | ≥ 10x reads      | Add **cache (Redis)**, optimize read path, denormalize if needed |
| Write QPS             | < 100–200 QPS    | Use **SQL + transactions + strong consistency** |
| Write QPS             | > 1K QPS         | Consider **sharding, async processing, eventual consistency** |
| Total data size       | < 1–2 TB         | Single **DB cluster + replicas**, no sharding needed |
| Total data size       | > 10 TB          | Plan **sharding / distributed storage** |
| Peak read QPS         | > 1K QPS         | Add **cache + read replicas** |
| Peak read QPS         | > 10K QPS        | Add **CDN + aggressive caching + partitioning** |
| Bandwidth (outgoing)  | > 10–50 MB/sec   | Reduce payload size, **cache responses**, consider CDN |
| Latency requirement   | < 200 ms         | Avoid heavy joins, use **indexes + caching** |
| Consistency need      | No oversell      | Use **strong consistency (transactions / locks)** |
| Availability target   | ≥ 99.9%          | Add **replication + failover** |