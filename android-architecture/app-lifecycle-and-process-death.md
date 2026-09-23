# Lifecycle and Process Death

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. Configuration change vs process death?

**Difficulty:** Basic

### Answer

Configuration changes recreate UI components while the process usually remains alive, allowing ViewModels to survive. Process death can remove in-memory state entirely, so durable or saved state is required for recovery.

### When to use

Design separately for recreation and true process restoration.

### When not to use / Common Mistake

Assuming ViewModel guarantees recovery after process death is a common mistake.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Which state should survive configuration changes versus process death?**

<details>
<summary>Reveal sample answer</summary>

ViewModel state can survive configuration changes. State required after process recreation must be persisted or represented through an appropriate saved-state mechanism.

</details>

2. **How should coroutine work be cancelled?**

<details>
<summary>Reveal sample answer</summary>

Tie coroutine lifetime to the owner that should control it, such as viewModelScope or lifecycleScope. Cancellation should stop work that no longer has a consumer unless it is intentionally longer-lived.

</details>

3. **How would you test recreation and process-death behavior?**

<details>
<summary>Reveal sample answer</summary>

Separate state reconstruction from UI rendering and test that the required state can be restored from stable inputs. Use instrumentation for framework recreation behavior where unit tests cannot represent it.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Configuration changes recreate UI components while the process usually remains alive, allowing ViewModels to survive. Process death can remove in-memory state entirely, so durable or saved state is required for recovery.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What should a ViewModel store?

**Difficulty:** Basic

### Answer

Store screen state and coordination state that can be reconstructed from durable data or saved state. Avoid holding references to Activities, Views, or short-lived UI contexts.

### When to use

Keep application-wide durable data in repositories/storage.

### Example

```kotlin
class DetailsViewModel(
    private val repository: ProductRepository
) : ViewModel() {
    private val _uiState = MutableStateFlow(UiState())
    val uiState = _uiState.asStateFlow()
}
```

### When not to use / Common Mistake

A ViewModel is not a replacement for a database.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a ViewModel own, and what should it avoid owning?**

<details>
<summary>Reveal sample answer</summary>

It should own screen state and coordinate screen-related operations. It should avoid holding Activity/Fragment references or becoming a general-purpose service locator.

</details>

2. **How should a ViewModel handle one-time UI events?**

<details>
<summary>Reveal sample answer</summary>

Do not encode a transient event as durable state unless replay is actually desired. Use explicit effect/event semantics and make collection behavior clear.

</details>

3. **What changes when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

A ViewModel is recreated, so in-memory state is lost. Reconstruct durable state from persistence and use saved-state mechanisms only for small state that must survive recreation.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Store screen state and coordination state that can be reconstructed from durable data or saved state. Avoid holding references to Activities, Views, or short-lived UI contexts.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How should a screen recover after recreation?

**Difficulty:** Intermediate

### Answer

Recreate UI from its state holder and authoritative data sources. Use saved-state mechanisms for small navigation or user-input details that cannot be reconstructed cheaply.

### When to use

Make the initial state deterministic and restoration explicit.

### When not to use / Common Mistake

Depending on incidental view state makes restoration fragile.

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

**Key idea:** Recreate UI from its state holder and authoritative data sources. Use saved-state mechanisms for small navigation or user-input details that cannot be reconstructed cheaply.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How do you avoid memory leaks in state holders?

**Difficulty:** Intermediate

### Answer

Do not retain lifecycle-bound objects beyond their lifetime, use lifecycle-aware collection, and cancel work through structured concurrency. Application-scoped objects should not capture Activity/Fragment references.

### When to use

Review coroutine scopes and callback registrations carefully.

### When not to use / Common Mistake

Using a global CoroutineScope for screen work can outlive the screen and leak behavior.

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

**Key idea:** Do not retain lifecycle-bound objects beyond their lifetime, use lifecycle-aware collection, and cancel work through structured concurrency. Application-scoped objects should not capture Activity/Fragment references.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What happens to coroutines in a ViewModel when the ViewModel is cleared?

**Difficulty:** Intermediate

### Answer

viewModelScope is cancelled when the ViewModel is cleared, so its child coroutines are cancelled. Work that must outlive the screen belongs in a longer-lived component such as a repository/application worker where appropriate.

### When to use

Choose the scope according to business ownership of the work.

### Example

```kotlin
class DetailsViewModel(
    private val repository: ProductRepository
) : ViewModel() {
    private val _uiState = MutableStateFlow(UiState())
    val uiState = _uiState.asStateFlow()
}
```

### When not to use / Common Mistake

Moving every coroutine to application scope just to avoid cancellation causes orphaned work.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a ViewModel own, and what should it avoid owning?**

<details>
<summary>Reveal sample answer</summary>

It should own screen state and coordinate screen-related operations. It should avoid holding Activity/Fragment references or becoming a general-purpose service locator.

</details>

2. **How should a ViewModel handle one-time UI events?**

<details>
<summary>Reveal sample answer</summary>

Do not encode a transient event as durable state unless replay is actually desired. Use explicit effect/event semantics and make collection behavior clear.

</details>

3. **What changes when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

A ViewModel is recreated, so in-memory state is lost. Reconstruct durable state from persistence and use saved-state mechanisms only for small state that must survive recreation.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** viewModelScope is cancelled when the ViewModel is cleared, so its child coroutines are cancelled. Work that must outlive the screen belongs in a longer-lived component such as a repository/application worker where appropriate.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How should long-running work survive app restarts?

**Difficulty:** Advanced

### Answer

Persist the work intent/state and use an appropriate durable scheduling mechanism such as WorkManager for deferrable background work. The process lifecycle should not be the source of truth for durable work.

### When to use

Use WorkManager when constraints, retries, and persistence across process death matter.

### When not to use / Common Mistake

A foreground Activity coroutine is not a durable job scheduler.

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

**Key idea:** Persist the work intent/state and use an appropriate durable scheduling mechanism such as WorkManager for deferrable background work. The process lifecycle should not be the source of truth for durable work.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you design for cold start restoration?

**Difficulty:** Advanced

### Answer

Make the initial route/state reconstructable from persisted identifiers and local data. Defer expensive remote work until the UI can render useful local state when possible.

### When to use

Measure startup and restoration rather than restoring every cached object eagerly.

### When not to use / Common Mistake

Blocking the main thread to reconstruct a large object graph creates poor startup behavior.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What is the source of truth in this design?**

<details>
<summary>Reveal sample answer</summary>

Name it explicitly and explain how other representations are derived or synchronized. Ambiguous ownership is a common cause of stale or conflicting state.

</details>

2. **What happens when a dependency is unavailable?**

<details>
<summary>Reveal sample answer</summary>

Define degraded behavior, retry/backoff, persistence of pending work, and what the user sees. Do not assume the network or a remote service is always available.

</details>

3. **How would you evolve this design without a rewrite?**

<details>
<summary>Reveal sample answer</summary>

Introduce boundaries around one feature or data flow, keep compatibility with existing callers, add tests and observability, and migrate incrementally as code is touched.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Make the initial route/state reconstructable from persisted identifiers and local data. Defer expensive remote work until the UI can render useful local state when possible.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How do you reason about lifecycle-aware collection?

**Difficulty:** Intermediate

### Answer

The UI should collect state only while it is in an appropriate lifecycle state, avoiding unnecessary work when not visible. In Compose, collectAsStateWithLifecycle is a common pattern for lifecycle-aware collection.

### When to use

Use lifecycle-aware APIs at UI boundaries.

### When not to use / Common Mistake

Collecting indefinitely from a UI that is not visible can waste resources and trigger unexpected work.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Which state should survive configuration changes versus process death?**

<details>
<summary>Reveal sample answer</summary>

ViewModel state can survive configuration changes. State required after process recreation must be persisted or represented through an appropriate saved-state mechanism.

</details>

2. **How should coroutine work be cancelled?**

<details>
<summary>Reveal sample answer</summary>

Tie coroutine lifetime to the owner that should control it, such as viewModelScope or lifecycleScope. Cancellation should stop work that no longer has a consumer unless it is intentionally longer-lived.

</details>

3. **How would you test recreation and process-death behavior?**

<details>
<summary>Reveal sample answer</summary>

Separate state reconstruction from UI rendering and test that the required state can be restored from stable inputs. Use instrumentation for framework recreation behavior where unit tests cannot represent it.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** The UI should collect state only while it is in an appropriate lifecycle state, avoiding unnecessary work when not visible. In Compose, collectAsStateWithLifecycle is a common pattern for lifecycle-aware collection.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
