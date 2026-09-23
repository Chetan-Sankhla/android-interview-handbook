# DataStore

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is DataStore?

**Difficulty:** 🟢 Basic

### Answer

Jetpack DataStore is an asynchronous, consistent, transactional storage solution for small amounts of data. It supports key-value data through Preferences DataStore and typed objects through typed/Proto DataStore.

DataStore exposes a `Flow` for reads and transactional update operations. Android's documentation recommends it over SharedPreferences for new preference-style storage.

### Example

```kotlin
private val Context.dataStore by preferencesDataStore(name = "settings")

val darkMode: Flow<Boolean> = context.dataStore.data
    .map { it[DARK_MODE] ?: false }

suspend fun setDarkMode(enabled: Boolean) {
    context.dataStore.edit { it[DARK_MODE] = enabled }
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Preferences vs Proto DataStore?** → [Open the related question](datastore.md#preferences-datastore-vs-proto-datastore)

2. **Why not Room?**

<details>
<summary>Reveal sample answer</summary>

Preferences DataStore is a natural fit for a small set of key/value settings. Proto DataStore is useful when the stored state has a defined structure and you want generated, strongly typed models plus explicit schema evolution.

</details>

3. **How does updateData work?**

<details>
<summary>Reveal sample answer</summary>

The transformation is applied as a transactional read-modify-write operation. DataStore serializes updates, so callers do not have to coordinate separate reads and writes themselves.

</details>

4. **How do you handle corruption?** → [Open the related question](datastore.md#how-do-you-handle-datastore-corruption)

### Common Mistake

Calling DataStore a replacement for every local-storage requirement.



### Quick Revision

**Key idea:** Jetpack DataStore is an asynchronous, consistent, transactional storage solution for small amounts of data. It supports key-value data through Preferences DataStore and typed objects through typed/Proto DataStore.

### Interview Insight

Start with DataStore’s scope: small application state. Then distinguish Preferences vs Proto and explain why Room is a better fit for query-heavy structured data.
## Q2. Preferences DataStore vs Proto DataStore?

**Difficulty:** 🟡 Intermediate

### Answer

Preferences DataStore stores values by keys and does not require a predefined schema. It is convenient for simple settings and flags.

Proto DataStore uses a predefined Protocol Buffers schema and gives strongly typed generated objects. It is useful when the stored state has a stable structured model and type safety is valuable.

Both are asynchronous and transactional; the choice is mainly about the shape and governance of the data.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When would Preferences be simpler?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What are the benefits of a schema?**

<details>
<summary>Reveal sample answer</summary>

A schema makes the stored shape explicit, gives stronger type guarantees, and forces compatibility decisions to be visible as the model evolves.

</details>

3. **Can DataStore use another serializer?**

<details>
<summary>Reveal sample answer</summary>

Proto DataStore uses a Serializer to convert between bytes and the typed object. The serializer defines how data is read and written and how corruption is surfaced.

</details>

### Common Mistake

Assuming Proto DataStore is always better simply because it is typed.



### Quick Revision

**Key idea:** Preferences DataStore stores values by keys and does not require a predefined schema. It is convenient for simple settings and flags.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How does DataStore provide transactional updates?

**Difficulty:** 🟡 Intermediate

### Answer

`updateData` receives the current value and applies a transformation as a single read-modify-write operation. DataStore serializes updates so callers do not have to manually coordinate the same kind of read/write race.

For Preferences DataStore, `edit` provides a similar transactional update model for preferences.

### Example

```kotlin
private val Context.dataStore by preferencesDataStore(name = "settings")

val darkMode: Flow<Boolean> = context.dataStore.data
    .map { it[DARK_MODE] ?: false }

suspend fun setDarkMode(enabled: Boolean) {
    context.dataStore.edit { it[DARK_MODE] = enabled }
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What happens if two updates occur concurrently?**

<details>
<summary>Reveal sample answer</summary>

Concurrent updates are serialized by the storage API. This avoids a lost update caused by two callers independently reading the same old value and then overwriting each other.

</details>

2. **Why is this safer than read-then-write?**

<details>
<summary>Reveal sample answer</summary>

A separate read and write has a race: another caller can change the value between them. A transactional update keeps the read-modify-write operation together.

</details>

3. **Can the transform perform network calls?**

<details>
<summary>Reveal sample answer</summary>

Keep network work outside the DataStore transformation. The update block should be a focused state transformation; external I/O makes the transaction slower and couples persistence to network failure.

</details>

### Common Mistake

Reading DataStore into memory, changing it later, and writing it back as a separate operation.



### Quick Revision

**Key idea:** `updateData` receives the current value and applies a transformation as a single read-modify-write operation. DataStore serializes updates so callers do not have to manually coordinate the same kind of read/write race.

### Interview Insight

Explain the atomic boundary and the invariant it protects. Interviewers usually care more about what happens on partial failure than about the annotation name.
## Q4. Why should you create only one DataStore instance per file in a process?

**Difficulty:** 🔴 Advanced

### Answer

Android's DataStore documentation explicitly warns against creating more than one DataStore instance for the same file in the same process. Multiple active instances can break DataStore behavior and can cause an `IllegalStateException`.

A common pattern is a single top-level property delegate or a single repository-owned instance for a given file.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you structure DataStore in a repository?**

<details>
<summary>Reveal sample answer</summary>

A repository should expose the data contract a feature needs and hide whether data comes from network, database, cache, or another source. It should not become a catch-all for unrelated business rules.

</details>

2. **What happens if two modules create it?**

<details>
<summary>Reveal sample answer</summary>

Choose module boundaries around feature ownership and dependency direction. The goal is lower coupling and build impact without creating so many tiny modules that navigation and coordination become harder.

</details>

3. **How would you detect accidental duplicate instances?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Creating a new DataStore object inside every repository method or ViewModel.



### Quick Revision

**Key idea:** Android's DataStore documentation explicitly warns against creating more than one DataStore instance for the same file in the same process. Multiple active instances can break DataStore behavior and can cause an `IllegalStateException`.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How should DataStore be used in a Compose application?

**Difficulty:** 🟡 Intermediate

### Answer

Keep DataStore in the data layer rather than reading or writing it directly from a composable. A repository can expose a `Flow`, the ViewModel can transform it into UI state, and the UI can collect that state lifecycle-safely.

This keeps persistence concerns out of the UI and prevents composable recomposition from accidentally becoming a storage access boundary.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why not access DataStore directly in Compose?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Where should mapping happen?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you test the ViewModel?**

<details>
<summary>Reveal sample answer</summary>

Test the smallest meaningful contract directly, then add integration or UI tests for Android framework behavior and cross-component interaction. Include error, empty, cancellation, concurrency, or lifecycle cases when they can change the result.

</details>

### Common Mistake

Collecting DataStore directly in many composables and creating storage coupling.



### Quick Revision

**Key idea:** Keep DataStore in the data layer rather than reading or writing it directly from a composable. A repository can expose a `Flow`, the ViewModel can transform it into UI state, and the UI can collect that state lifecycle-safely.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How do you handle DataStore corruption?

**Difficulty:** 🔴 Advanced

### Answer

DataStore can throw `CorruptionException` when its persisted file cannot be read. It does not automatically recover by silently replacing the data.

A corruption handler can be configured to replace corrupted state with a known default. This is appropriate only when losing that state is acceptable; critical user data may require a different recovery strategy.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When is ReplaceFileCorruptionHandler appropriate?**

<details>
<summary>Reveal sample answer</summary>

Use a corruption handler only when you have a safe recovery value. If data can be reconstructed from another source, restore it deliberately; otherwise avoid silently discarding potentially recoverable user data.

</details>

2. **What data should be backed up?** → [Open the related question](storage-security.md#should-sensitive-datastore-data-be-backed-up)

3. **How would you observe corruption in production?**

<details>
<summary>Reveal sample answer</summary>

Use a corruption handler only when you have a safe recovery value. If data can be reconstructed from another source, restore it deliberately; otherwise avoid silently discarding potentially recoverable user data.

</details>

### Common Mistake

Using a corruption handler that silently discards important user data.



### Quick Revision

**Key idea:** DataStore can throw `CorruptionException` when its persisted file cannot be read. It does not automatically recover by silently replacing the data.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. Can DataStore be used across processes?

**Difficulty:** 🔴 Advanced

### Answer

Modern DataStore supports multi-process access through `MultiProcessDataStore`. Android documents consistency guarantees such as serialized writes and read-after-write consistency.

You should not mix `SingleProcessDataStore` and `MultiProcessDataStore` for the same file. Multi-process access should be a deliberate architecture decision because it introduces process-boundary complexity.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When would you need multi-process DataStore?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What guarantees does it provide?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Why not use a single-process store everywhere?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming the normal single-process DataStore instance can safely be shared between processes.



### Quick Revision

**Key idea:** Modern DataStore supports multi-process access through `MultiProcessDataStore`. Android documents consistency guarantees such as serialized writes and read-after-write consistency.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you test a DataStore repository?

**Difficulty:** 🟡 Intermediate

### Answer

Test at least default values, successful reads/writes, concurrent updates, migration behavior, malformed/corrupt data where relevant, and expected I/O failures.

Keep the repository API domain-oriented and use isolated test storage. Do not make every ViewModel test depend on a real persistent file unless persistence behavior is what the test is intended to verify.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/topic/libraries/architecture/datastore
- https://developer.android.com/codelabs/android-preferences-datastore
- https://developer.android.com/codelabs/android-proto-datastore

### Quick Revision

**Key idea:** Test at least default values, successful reads/writes, concurrent updates, migration behavior, malformed/corrupt data where relevant, and expected I/O failures. Keep the repository API domain-oriented and use isolated test storage.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
