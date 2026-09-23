# Offline-First Architecture

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What does offline-first mean?

**Difficulty:** Basic

### Answer

An offline-first app can perform all or an important subset of its core functionality without reliable internet. The architecture therefore treats local data as an important source rather than making every screen wait for the network.

### When to use

Use it when users need continuity under poor connectivity.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

Offline-first is not simply adding a cache to a network-first app.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** An offline-first app can perform all or an important subset of its core functionality without reliable internet. The architecture therefore treats local data as an important source rather than making every screen wait for the network.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What is the local source of truth?

**Difficulty:** Intermediate

### Answer

For many offline-first reads, local storage is the source the UI observes. Network synchronization updates local storage, and the UI reacts to that change.

### When to use

This creates one observable path for screen data.

### When not to use / Common Mistake

If the UI chooses between network and database itself, consistency becomes a presentation concern.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What is the most important edge case for this API or concept?**

<details>
<summary>Reveal sample answer</summary>

Identify the failure or boundary condition that changes the normal behavior, then explain how the implementation handles it.

</details>

2. **How would you test this behavior?**

<details>
<summary>Reveal sample answer</summary>

Test the smallest observable contract directly, then add integration coverage only where framework or cross-component behavior changes the result.

</details>

3. **When would you choose a different approach?**

<details>
<summary>Reveal sample answer</summary>

Name the requirement that would invalidate the current choice, then compare the alternative against that requirement rather than treating either option as universally better.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** For many offline-first reads, local storage is the source the UI observes. Network synchronization updates local storage, and the UI reacts to that change.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How should offline writes work?

**Difficulty:** Intermediate

### Answer

Write user intent locally first when possible, mark synchronization state, and enqueue or trigger background synchronization. The UI can immediately reflect the local change.

### When to use

Use durable queues and explicit sync state for operations that must survive process death.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

Keeping pending writes only in memory loses user actions.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Write user intent locally first when possible, mark synchronization state, and enqueue or trigger background synchronization. The UI can immediately reflect the local change.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How do you resolve sync conflicts?

**Difficulty:** Advanced

### Answer

Choose a deterministic policy based on product semantics: server authority, last-write-wins, field-level merge, version checks, or domain-specific conflict resolution. There is no universally correct strategy.

### When to use

Document the conflict policy and test concurrent edits.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

Timestamp-based last-write-wins can silently discard meaningful user changes.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Choose a deterministic policy based on product semantics: server authority, last-write-wins, field-level merge, version checks, or domain-specific conflict resolution. There is no universally correct strategy.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. Where should sync orchestration live?

**Difficulty:** Intermediate

### Answer

A repository/synchronization component can coordinate local and remote sources, while durable background execution can be handled by WorkManager. ViewModels should trigger intent rather than own the sync engine.

### When to use

Separate sync policy from UI lifecycle.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

Starting complex synchronization directly from a composable creates fragile lifecycle coupling.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A repository/synchronization component can coordinate local and remote sources, while durable background execution can be handled by WorkManager. ViewModels should trigger intent rather than own the sync engine.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How should retries be designed for offline sync?

**Difficulty:** Advanced

### Answer

Use bounded backoff, classify retryable failures, persist pending work, and avoid duplicate side effects through idempotency or operation identifiers.

### When to use

Make retry safe across process restarts.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

Retrying a non-idempotent POST without deduplication can create duplicate server operations.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Use bounded backoff, classify retryable failures, persist pending work, and avoid duplicate side effects through idempotency or operation identifiers.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you expose sync status to the UI?

**Difficulty:** Intermediate

### Answer

Model meaningful states such as synced, pending, syncing, or failed as application state. Keep transport details out of UI models unless the product needs them.

### When to use

Expose actionable status and recovery actions.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

Showing a raw HTTP error code is rarely a good user-facing sync model.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Model meaningful states such as synced, pending, syncing, or failed as application state. Keep transport details out of UI models unless the product needs them.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you design offline-first for a large app?

**Difficulty:** Advanced

### Answer

Define local ownership per feature, common sync infrastructure, conflict policies, observability, retry budgets, and migration strategy. Ensure each feature can render from local state independently.

### When to use

Use metrics for queue depth, sync latency, failures, and conflict rates.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

A single global sync manager that knows every feature can become a massive coupling point.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Define local ownership per feature, common sync infrastructure, conflict policies, observability, retry budgets, and migration strategy. Ensure each feature can render from local state independently.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
