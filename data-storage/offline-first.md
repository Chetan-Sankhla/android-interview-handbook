# Offline-First Storage Architecture

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What does offline-first mean?

**Difficulty:** 🟡 Intermediate

### Answer

Offline-first means the application is designed so useful behavior does not depend on a network being available for every interaction. Local persistence is therefore a first-class part of the data architecture.

A common Android approach is API ↔ Repository ↔ Room, with Room acting as the local source of truth for UI reads. Network synchronization updates Room, and the UI observes Room through Flow.

### Common Follow-ups

- What is the source of truth?
- How do writes work offline?
- How do you handle conflicts?

### Senior/Lead Perspective

Offline-first is not simply 'cache the API'. It requires explicit policies for freshness, writes, retries, conflict resolution, and recovery.

### Common Mistake

Calling an app offline-first because it shows the last API response from memory.

## Q2. Why can Room be the local source of truth?

**Difficulty:** 🟡 Intermediate

### Answer

Room provides durable local state, queryability, transactions, and observable queries. The UI can render from Room consistently whether data came from a previous sync or a fresh network response.

The repository can synchronize remote data into Room, making the UI independent of network timing.

### Common Follow-ups

- Does that make the server irrelevant?
- How do you refresh Room?
- What happens on sync failure?

### Senior/Lead Perspective

The server remains the remote authority for server-owned data. 'Local source of truth' means the UI has one consistent local representation, not that the device overrides the server.

### Common Mistake

Treating local Room state as automatically authoritative over server state.

## Q3. How would you implement stale-while-revalidate?

**Difficulty:** 🟡 Intermediate

### Answer

Read and display locally persisted data immediately, then determine whether it is stale. If stale, trigger a background refresh. Successful remote data is written into Room, which automatically updates observers.

The freshness policy can use timestamps, server versions, ETags, cursors, or product-specific rules.

### Common Follow-ups

- Where should freshness metadata live?
- Should every screen trigger refresh?
- How do you prevent duplicate refreshes?

### Senior/Lead Perspective

Refresh policy belongs in the data/repository layer. Screens should express user intent such as pull-to-refresh, not each invent its own cache rules.

### Common Mistake

Making every ViewModel implement its own TTL and refresh logic.

## Q4. How would you handle offline writes?

**Difficulty:** 🔴 Advanced

### Answer

Persist the user's intended operation locally, usually as durable state or an outbox entry, then synchronize it when connectivity is available.

Each operation should be idempotent or carry a stable operation ID so retries do not duplicate remote side effects. The UI should represent states such as pending, synchronized, failed, or requiring conflict resolution.

### Common Follow-ups

- What is an outbox?
- How do you retry safely?
- What if the server rejects the operation?

### Senior/Lead Perspective

Offline writes turn the system into a distributed workflow. You need durable intent, retry policy, idempotency, conflict handling, and observability.

### Common Mistake

Keeping offline writes only in memory or blindly retrying non-idempotent operations.

## Q5. How do you handle conflicts between local and remote data?

**Difficulty:** 🔴 Advanced

### Answer

First define ownership and conflict semantics. Possible strategies include server-wins, client-wins, version-based resolution, field-level merging, or explicit user resolution.

For collaborative or critical data, include server versions/timestamps or revision IDs rather than relying on device clocks alone.

### Common Follow-ups

- When is server-wins acceptable?
- How do you detect stale writes?
- What if two devices edit the same record?

### Senior/Lead Perspective

Conflict resolution is a product rule as much as a technical rule. A lead should get the business owner to define the acceptable outcome before implementing the sync algorithm.

### Common Mistake

Choosing last-write-wins without checking whether overwriting user data is acceptable.

## Q6. How would you synchronize 1 million local records?

**Difficulty:** 🔴 Advanced

### Answer

Avoid full-table replacement on every sync. Use server-provided cursors, versions, delta endpoints, or change tokens when available. Apply changes in batches and transactions while keeping memory usage bounded.

Index the fields used for reconciliation, track sync checkpoints, and make each batch restartable after process death.

### Common Follow-ups

- How do you resume after a crash?
- How do you delete remote records?
- How do you avoid duplicate work?

### Senior/Lead Perspective

At scale, synchronization is a data pipeline. Checkpointing, idempotency, bounded memory, and observability become more important than the individual Room API call.

### Common Mistake

Downloading the complete dataset and replacing the entire local database on every sync.

## Q7. How would you prevent multiple simultaneous syncs?

**Difficulty:** 🔴 Advanced

### Answer

Centralize synchronization and coordinate concurrent triggers. Depending on the use case, use a mutex/single-flight mechanism in-process and WorkManager for durable background work.

The sync operation itself should still be idempotent because process death, retries, and multiple app versions can produce repeated work.

### Common Follow-ups

- When would you use WorkManager?
- Can a mutex solve cross-process duplication?
- How do you persist sync state?

### Senior/Lead Perspective

Coordination should exist at both scheduling and data levels. A lock reduces duplicate work; idempotency protects correctness when duplicate work still happens.

### Common Mistake

Relying only on an in-memory mutex for work that must survive process death.

## Q8. How should API DTOs, Room entities, and domain models interact?

**Difficulty:** 🟡 Intermediate

### Answer

Keep them separate when the boundaries have meaningful differences:

- **DTO** represents the network contract.
- **Entity** represents persistence.
- **Domain model** represents business concepts.
- **UI model** represents presentation needs.

Mappings add code, but they prevent API or database schema changes from propagating through every layer.

### Common Follow-ups

- Is this always necessary?
- Where should mapping happen?
- What about simple CRUD apps?

### Senior/Lead Perspective

Do not create abstractions without a reason. For small applications, fewer models may be reasonable; for large teams/products, explicit boundaries usually reduce long-term coupling.

### Common Mistake

Creating four identical models mechanically without any boundary benefit.

### References

- https://developer.android.com/topic/architecture/data-layer
- https://developer.android.com/topic/architecture
- https://developer.android.com/training/data-storage/room