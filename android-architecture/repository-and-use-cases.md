# Repositories and Use Cases

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What should a repository own?

**Difficulty:** Basic

### Answer

A repository should provide application-oriented access to data and coordinate underlying sources such as network, database, and cache. It should hide source-selection details from callers.

### When to use

A repository is a good place to define the source-of-truth policy.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Do not put presentation state or navigation in a repository.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What responsibility should stay in the repository, and what should move to a use case?**

<details>
<summary>Reveal sample answer</summary>

The repository should hide data-source concerns. Business rules and multi-step orchestration belong in a use case when they form a meaningful operation; do not move every line of code into a use case just to create another layer.

</details>

2. **When does a repository abstraction become unnecessary indirection?**

<details>
<summary>Reveal sample answer</summary>

If the class only forwards every method to one dependency and provides no useful boundary, it may add little value. Keep an abstraction when it hides a real data-source decision, enables testing, or protects consumers from change.

</details>

3. **How would you test a repository and a use case?**

<details>
<summary>Reveal sample answer</summary>

Test repository behavior with controlled data sources and persistence integration where needed. Test business rules in use-case unit tests with fake or mock dependencies, keeping the tests focused on observable behavior.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A repository should provide application-oriented access to data and coordinate underlying sources such as network, database, and cache. It should hide source-selection details from callers.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q2. Should a repository expose Flow or suspend functions?

**Difficulty:** Intermediate

### Answer

Choose based on the data semantics. Observable data naturally fits Flow; one-shot commands often fit suspend functions. A repository can expose both when the domain requires both.

### When to use

Use Flow when consumers need ongoing updates.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Returning Flow for every operation can make simple commands unnecessarily complex.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What responsibility should stay in the repository, and what should move to a use case?**

<details>
<summary>Reveal sample answer</summary>

The repository should hide data-source concerns. Business rules and multi-step orchestration belong in a use case when they form a meaningful operation; do not move every line of code into a use case just to create another layer.

</details>

2. **When does a repository abstraction become unnecessary indirection?**

<details>
<summary>Reveal sample answer</summary>

If the class only forwards every method to one dependency and provides no useful boundary, it may add little value. Keep an abstraction when it hides a real data-source decision, enables testing, or protects consumers from change.

</details>

3. **How would you test a repository and a use case?**

<details>
<summary>Reveal sample answer</summary>

Test repository behavior with controlled data sources and persistence integration where needed. Test business rules in use-case unit tests with fake or mock dependencies, keeping the tests focused on observable behavior.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Choose based on the data semantics. Observable data naturally fits Flow; one-shot commands often fit suspend functions.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q3. How should a repository combine local and remote data?

**Difficulty:** Intermediate

### Answer

Define a clear source-of-truth strategy. In an offline-first design, local storage often drives reads while synchronization updates local storage. This prevents the UI from having to coordinate two sources directly.

### When to use

Persist network results locally and expose the local stream when local data should be authoritative for UI reads.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Returning network and database independently to the UI pushes consistency logic into presentation.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What responsibility should stay in the repository, and what should move to a use case?**

<details>
<summary>Reveal sample answer</summary>

The repository should hide data-source concerns. Business rules and multi-step orchestration belong in a use case when they form a meaningful operation; do not move every line of code into a use case just to create another layer.

</details>

2. **When does a repository abstraction become unnecessary indirection?**

<details>
<summary>Reveal sample answer</summary>

If the class only forwards every method to one dependency and provides no useful boundary, it may add little value. Keep an abstraction when it hides a real data-source decision, enables testing, or protects consumers from change.

</details>

3. **How would you test a repository and a use case?**

<details>
<summary>Reveal sample answer</summary>

Test repository behavior with controlled data sources and persistence integration where needed. Test business rules in use-case unit tests with fake or mock dependencies, keeping the tests focused on observable behavior.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Define a clear source-of-truth strategy. In an offline-first design, local storage often drives reads while synchronization updates local storage.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q4. Where should retry logic live?

**Difficulty:** Intermediate

### Answer

Retry policy belongs close to the operation whose failure semantics it understands, often the data layer or a synchronization component. UI code should not implement transport retry loops.

### When to use

Make retry bounded and classify errors as retryable or permanent.

### When not to use / Common Mistake

Blind exponential retry can amplify outages and battery/network usage.

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

**Key idea:** Retry policy belongs close to the operation whose failure semantics it understands, often the data layer or a synchronization component. UI code should not implement transport retry loops.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. Should repositories cache everything?

**Difficulty:** Advanced

### Answer

No. Cache according to freshness, cost, offline requirements, memory/storage constraints, and correctness. Some data should be fetched fresh, while other data benefits from durable local storage.

### When to use

Define TTL, invalidation, or versioning explicitly when stale data has consequences.

### When not to use / Common Mistake

A cache without an invalidation policy is often a second database with unpredictable correctness.

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

**Key idea:** No. Cache according to freshness, cost, offline requirements, memory/storage constraints, and correctness.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How do you handle repository failures?

**Difficulty:** Intermediate

### Answer

Translate low-level failures into stable application-facing outcomes while retaining enough information for logging and recovery. The UI should not need to understand Retrofit, SQL, or socket exception types.

### When to use

Use domain/data error types when callers need to distinguish retryable, authentication, validation, or unavailable cases.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Catching every exception and returning a generic failure loses actionable semantics.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What responsibility should stay in the repository, and what should move to a use case?**

<details>
<summary>Reveal sample answer</summary>

The repository should hide data-source concerns. Business rules and multi-step orchestration belong in a use case when they form a meaningful operation; do not move every line of code into a use case just to create another layer.

</details>

2. **When does a repository abstraction become unnecessary indirection?**

<details>
<summary>Reveal sample answer</summary>

If the class only forwards every method to one dependency and provides no useful boundary, it may add little value. Keep an abstraction when it hides a real data-source decision, enables testing, or protects consumers from change.

</details>

3. **How would you test a repository and a use case?**

<details>
<summary>Reveal sample answer</summary>

Test repository behavior with controlled data sources and persistence integration where needed. Test business rules in use-case unit tests with fake or mock dependencies, keeping the tests focused on observable behavior.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Translate low-level failures into stable application-facing outcomes while retaining enough information for logging and recovery. The UI should not need to understand Retrofit, SQL, or socket exception types.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q7. How do you test a repository?

**Difficulty:** Intermediate

### Answer

Test source selection, mapping, caching, error handling, and synchronization with fakes or test databases. Contract tests can validate important behavior independent of a specific implementation.

### When to use

Test both happy paths and stale/offline/failure scenarios.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Only mocking the repository itself does not test repository behavior.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What responsibility should stay in the repository, and what should move to a use case?**

<details>
<summary>Reveal sample answer</summary>

The repository should hide data-source concerns. Business rules and multi-step orchestration belong in a use case when they form a meaningful operation; do not move every line of code into a use case just to create another layer.

</details>

2. **When does a repository abstraction become unnecessary indirection?**

<details>
<summary>Reveal sample answer</summary>

If the class only forwards every method to one dependency and provides no useful boundary, it may add little value. Keep an abstraction when it hides a real data-source decision, enables testing, or protects consumers from change.

</details>

3. **How would you test a repository and a use case?**

<details>
<summary>Reveal sample answer</summary>

Test repository behavior with controlled data sources and persistence integration where needed. Test business rules in use-case unit tests with fake or mock dependencies, keeping the tests focused on observable behavior.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Test source selection, mapping, caching, error handling, and synchronization with fakes or test databases. Contract tests can validate important behavior independent of a specific implementation.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q8. How would you design a repository for multiple backends?

**Difficulty:** Advanced

### Answer

Expose one stable contract and isolate backend-specific implementations behind data-source interfaces or strategy components. Select implementations through DI/configuration.

### When to use

Useful for migration from REST to GraphQL, regional backends, or feature-flagged implementations.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Avoid leaking backend-specific models through the repository contract.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What responsibility should stay in the repository, and what should move to a use case?**

<details>
<summary>Reveal sample answer</summary>

The repository should hide data-source concerns. Business rules and multi-step orchestration belong in a use case when they form a meaningful operation; do not move every line of code into a use case just to create another layer.

</details>

2. **When does a repository abstraction become unnecessary indirection?**

<details>
<summary>Reveal sample answer</summary>

If the class only forwards every method to one dependency and provides no useful boundary, it may add little value. Keep an abstraction when it hides a real data-source decision, enables testing, or protects consumers from change.

</details>

3. **How would you test a repository and a use case?**

<details>
<summary>Reveal sample answer</summary>

Test repository behavior with controlled data sources and persistence integration where needed. Test business rules in use-case unit tests with fake or mock dependencies, keeping the tests focused on observable behavior.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Expose one stable contract and isolate backend-specific implementations behind data-source interfaces or strategy components. Select implementations through DI/configuration.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
