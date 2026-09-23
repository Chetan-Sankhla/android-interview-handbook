# Offline-First Storage Architecture

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What does offline-first mean?

**Difficulty:** 🟡 Intermediate

### Answer

Offline-first means the application is designed so useful behavior does not depend on a network being available for every interaction. Local persistence is therefore a first-class part of the data architecture.

A common Android approach is API ↔ Repository ↔ Room, with Room acting as the local source of truth for UI reads. Network synchronization updates Room, and the UI observes Room through Flow.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What is the source of truth?** → [Open the related question](../android-architecture/offline-first-architecture.md#what-is-the-local-source-of-truth)

2. **How do writes work offline?** → [Open the related question](../android-architecture/offline-first-architecture.md#how-should-offline-writes-work)

3. **How do you handle conflicts?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

### Common Mistake

Calling an app offline-first because it shows the last API response from memory.



### Quick Revision

**Key idea:** Offline-first means the application is designed so useful behavior does not depend on a network being available for every interaction. Local persistence is therefore a first-class part of the data architecture.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Why can Room be the local source of truth?

**Difficulty:** 🟡 Intermediate

### Answer

Room provides durable local state, queryability, transactions, and observable queries. The UI can render from Room consistently whether data came from a previous sync or a fresh network response.

The repository can synchronize remote data into Room, making the UI independent of network timing.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Does that make the server irrelevant?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you refresh Room?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What happens on sync failure?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

### Common Mistake

Treating local Room state as automatically authoritative over server state.



### Quick Revision

**Key idea:** Room provides durable local state, queryability, transactions, and observable queries. The UI can render from Room consistently whether data came from a previous sync or a fresh network response.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How would you implement stale-while-revalidate?

**Difficulty:** 🟡 Intermediate

### Answer

Read and display locally persisted data immediately, then determine whether it is stale. If stale, trigger a background refresh. Successful remote data is written into Room, which automatically updates observers.

The freshness policy can use timestamps, server versions, ETags, cursors, or product-specific rules.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Where should freshness metadata live?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Should every screen trigger refresh?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you prevent duplicate refreshes?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Making every ViewModel implement its own TTL and refresh logic.



### Quick Revision

**Key idea:** Read and display locally persisted data immediately, then determine whether it is stale. If stale, trigger a background refresh.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How would you handle offline writes?

**Difficulty:** 🔴 Advanced

### Answer

Persist the user's intended operation locally, usually as durable state or an outbox entry, then synchronize it when connectivity is available.

Each operation should be idempotent or carry a stable operation ID so retries do not duplicate remote side effects. The UI should represent states such as pending, synchronized, failed, or requiring conflict resolution.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What is an outbox?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you retry safely?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What if the server rejects the operation?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Keeping offline writes only in memory or blindly retrying non-idempotent operations.



### Quick Revision

**Key idea:** Persist the user's intended operation locally, usually as durable state or an outbox entry, then synchronize it when connectivity is available. Each operation should be idempotent or carry a stable operation ID so retries do not duplicate remote side effects.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How do you handle conflicts between local and remote data?

**Difficulty:** 🔴 Advanced

### Answer

First define ownership and conflict semantics. Possible strategies include server-wins, client-wins, version-based resolution, field-level merging, or explicit user resolution.

For collaborative or critical data, include server versions/timestamps or revision IDs rather than relying on device clocks alone.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When is server-wins acceptable?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you detect stale writes?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What if two devices edit the same record?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Choosing last-write-wins without checking whether overwriting user data is acceptable.



### Quick Revision

**Key idea:** First define ownership and conflict semantics. Possible strategies include server-wins, client-wins, version-based resolution, field-level merging, or explicit user resolution.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How would you synchronize 1 million local records?

**Difficulty:** 🔴 Advanced

### Answer

Avoid full-table replacement on every sync. Use server-provided cursors, versions, delta endpoints, or change tokens when available. Apply changes in batches and transactions while keeping memory usage bounded.

Index the fields used for reconciliation, track sync checkpoints, and make each batch restartable after process death.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you resume after a crash?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you delete remote records?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you avoid duplicate work?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Downloading the complete dataset and replacing the entire local database on every sync.



### Quick Revision

**Key idea:** Avoid full-table replacement on every sync. Use server-provided cursors, versions, delta endpoints, or change tokens when available.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How would you prevent multiple simultaneous syncs?

**Difficulty:** 🔴 Advanced

### Answer

Centralize synchronization and coordinate concurrent triggers. Depending on the use case, use a mutex/single-flight mechanism in-process and WorkManager for durable background work.

The sync operation itself should still be idempotent because process death, retries, and multiple app versions can produce repeated work.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When would you use WorkManager?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can a mutex solve cross-process duplication?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you persist sync state?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

### Common Mistake

Relying only on an in-memory mutex for work that must survive process death.



### Quick Revision

**Key idea:** Centralize synchronization and coordinate concurrent triggers. Depending on the use case, use a mutex/single-flight mechanism in-process and WorkManager for durable background work.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How should API DTOs, Room entities, and domain models interact?

**Difficulty:** 🟡 Intermediate

### Answer

Keep them separate when the boundaries have meaningful differences:

- **DTO** represents the network contract.
- **Entity** represents persistence.
- **Domain model** represents business concepts.
- **UI model** represents presentation needs.

Mappings add code, but they prevent API or database schema changes from propagating through every layer.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/topic/architecture/data-layer
- https://developer.android.com/topic/architecture
- https://developer.android.com/training/data-storage/room

### Quick Revision

**Key idea:** Keep them separate when the boundaries have meaningful differences: - **DTO** represents the network contract. - **Entity** represents persistence.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
