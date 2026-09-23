# DataStore

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is DataStore?

**Difficulty:** 🟢 Basic

### Answer

Jetpack DataStore is an asynchronous, consistent, transactional storage solution for small amounts of data. It supports key-value data through Preferences DataStore and typed objects through typed/Proto DataStore.

DataStore exposes a `Flow` for reads and transactional update operations. Android's documentation recommends it over SharedPreferences for new preference-style storage.

### Common Follow-ups

- Preferences vs Proto DataStore?
- Why not Room?
- How does updateData work?
- How do you handle corruption?

### Senior/Lead Perspective

A good answer identifies DataStore's boundary: it is excellent for small state, but it is not intended to replace a relational database.

### Common Mistake

Calling DataStore a replacement for every local-storage requirement.

## Q2. Preferences DataStore vs Proto DataStore?

**Difficulty:** 🟡 Intermediate

### Answer

Preferences DataStore stores values by keys and does not require a predefined schema. It is convenient for simple settings and flags.

Proto DataStore uses a predefined Protocol Buffers schema and gives strongly typed generated objects. It is useful when the stored state has a stable structured model and type safety is valuable.

Both are asynchronous and transactional; the choice is mainly about the shape and governance of the data.

### Common Follow-ups

- When would Preferences be simpler?
- What are the benefits of a schema?
- Can DataStore use another serializer?

### Senior/Lead Perspective

For a team-scale codebase, typed data can reduce ambiguous key usage and make schema evolution more explicit. Preferences can remain the simpler choice for genuinely preference-like values.

### Common Mistake

Assuming Proto DataStore is always better simply because it is typed.

## Q3. How does DataStore provide transactional updates?

**Difficulty:** 🟡 Intermediate

### Answer

`updateData` receives the current value and applies a transformation as a single read-modify-write operation. DataStore serializes updates so callers do not have to manually coordinate the same kind of read/write race.

For Preferences DataStore, `edit` provides a similar transactional update model for preferences.

### Common Follow-ups

- What happens if two updates occur concurrently?
- Why is this safer than read-then-write?
- Can the transform perform network calls?

### Senior/Lead Perspective

Keep the transformation focused on local state. Do not put slow network operations or unrelated side effects inside the update transaction.

### Common Mistake

Reading DataStore into memory, changing it later, and writing it back as a separate operation.

## Q4. Why should you create only one DataStore instance per file in a process?

**Difficulty:** 🔴 Advanced

### Answer

Android's DataStore documentation explicitly warns against creating more than one DataStore instance for the same file in the same process. Multiple active instances can break DataStore behavior and can cause an `IllegalStateException`.

A common pattern is a single top-level property delegate or a single repository-owned instance for a given file.

### Common Follow-ups

- How would you structure DataStore in a repository?
- What happens if two modules create it?
- How would you detect accidental duplicate instances?

### Senior/Lead Perspective

At architecture level, make ownership obvious. Centralizing the DataStore instance also makes dependency injection and testing easier.

### Common Mistake

Creating a new DataStore object inside every repository method or ViewModel.

## Q5. How should DataStore be used in a Compose application?

**Difficulty:** 🟡 Intermediate

### Answer

Keep DataStore in the data layer rather than reading or writing it directly from a composable. A repository can expose a `Flow`, the ViewModel can transform it into UI state, and the UI can collect that state lifecycle-safely.

This keeps persistence concerns out of the UI and prevents composable recomposition from accidentally becoming a storage access boundary.

### Common Follow-ups

- Why not access DataStore directly in Compose?
- Where should mapping happen?
- How do you test the ViewModel?

### Senior/Lead Perspective

The important principle is unidirectional data flow: persistent state enters the ViewModel through the repository and UI events trigger explicit update operations.

### Common Mistake

Collecting DataStore directly in many composables and creating storage coupling.

## Q6. How do you handle DataStore corruption?

**Difficulty:** 🔴 Advanced

### Answer

DataStore can throw `CorruptionException` when its persisted file cannot be read. It does not automatically recover by silently replacing the data.

A corruption handler can be configured to replace corrupted state with a known default. This is appropriate only when losing that state is acceptable; critical user data may require a different recovery strategy.

### Common Follow-ups

- When is ReplaceFileCorruptionHandler appropriate?
- What data should be backed up?
- How would you observe corruption in production?

### Senior/Lead Perspective

A senior decision depends on the value of the data. Resetting a theme preference is very different from resetting critical user-generated state.

### Common Mistake

Using a corruption handler that silently discards important user data.

## Q7. Can DataStore be used across processes?

**Difficulty:** 🔴 Advanced

### Answer

Modern DataStore supports multi-process access through `MultiProcessDataStore`. Android documents consistency guarantees such as serialized writes and read-after-write consistency.

You should not mix `SingleProcessDataStore` and `MultiProcessDataStore` for the same file. Multi-process access should be a deliberate architecture decision because it introduces process-boundary complexity.

### Common Follow-ups

- When would you need multi-process DataStore?
- What guarantees does it provide?
- Why not use a single-process store everywhere?

### Senior/Lead Perspective

First question whether multiple processes are genuinely required. If not, keep the design single-process; it is simpler to reason about and test.

### Common Mistake

Assuming the normal single-process DataStore instance can safely be shared between processes.

## Q8. How would you test a DataStore repository?

**Difficulty:** 🟡 Intermediate

### Answer

Test at least default values, successful reads/writes, concurrent updates, migration behavior, malformed/corrupt data where relevant, and expected I/O failures.

Keep the repository API domain-oriented and use isolated test storage. Do not make every ViewModel test depend on a real persistent file unless persistence behavior is what the test is intended to verify.

### Common Follow-ups

- What should be an integration test?
- How would you test migration?
- How do you test concurrent updates?

### Senior/Lead Perspective

A mature test strategy separates repository behavior from platform/storage integration tests. This keeps fast unit tests while still validating the real persistence layer.

### Common Mistake

Only testing that a value can be written once and assuming migration/concurrency are covered.

### References

- https://developer.android.com/topic/libraries/architecture/datastore
- https://developer.android.com/codelabs/android-preferences-datastore
- https://developer.android.com/codelabs/android-proto-datastore