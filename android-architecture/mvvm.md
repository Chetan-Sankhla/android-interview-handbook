# MVVM

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What is MVVM in Android?

**Difficulty:** Basic

### Answer

MVVM separates the UI from a state-producing ViewModel. The UI observes state and sends events; the ViewModel coordinates work and exposes UI state; repositories or other layers provide data and business capabilities.

### When to use

Use it when screen state has enough lifecycle or asynchronous complexity to benefit from a state holder.

### When not to use / Common Mistake

MVVM does not mean putting all application logic into ViewModel classes.

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

**Key idea:** MVVM separates the UI from a state-producing ViewModel. The UI observes state and sends events; the ViewModel coordinates work and exposes UI state; repositories or other layers provide data and business capabilities.

### Interview Insight

Explain responsibilities rather than the acronym: the UI renders state, the ViewModel owns screen state and coordinates work, and data/business boundaries stay explicit.
## Q2. Why is ViewModel useful for screen state?

**Difficulty:** Basic

### Answer

A ViewModel is designed for UI-related state that should survive configuration changes and can coordinate asynchronous work outside the Activity or Fragment lifecycle.

### When to use

Use it for screen-level state and work that belongs to that screen scope.

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

Do not treat ViewModel as a general-purpose singleton or application service.

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

**Key idea:** A ViewModel is designed for UI-related state that should survive configuration changes and can coordinate asynchronous work outside the Activity or Fragment lifecycle.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. Should a ViewModel expose MutableStateFlow?

**Difficulty:** Intermediate

### Answer

Usually no. Keep mutable state private and expose a read-only StateFlow or another immutable state representation. This preserves ownership of state transitions inside the ViewModel.

### When to use

Use private mutable state plus a public read-only stream when external consumers only need observation.

### Example

```kotlin
private val _uiState = MutableStateFlow(UiState())
val uiState: StateFlow<UiState> = _uiState.asStateFlow()
```

### When not to use / Common Mistake

Public mutable state lets any caller violate the intended state contract.

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

**Key idea:** Usually no. Keep mutable state private and expose a read-only StateFlow or another immutable state representation.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. Where should API calls happen in MVVM?

**Difficulty:** Intermediate

### Answer

The ViewModel can initiate a use case or repository operation, but the networking implementation belongs in the data layer. This keeps transport concerns away from UI state management.

### When to use

Inject a repository or use case into the ViewModel.

### When not to use / Common Mistake

Calling Retrofit directly from a composable or Activity creates lifecycle and testing problems.

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

**Key idea:** The ViewModel can initiate a use case or repository operation, but the networking implementation belongs in the data layer. This keeps transport concerns away from UI state management.

### Interview Insight

Explain responsibilities rather than the acronym: the UI renders state, the ViewModel owns screen state and coordinates work, and data/business boundaries stay explicit.
## Q5. Should every ViewModel have a repository?

**Difficulty:** Intermediate

### Answer

No. A ViewModel should depend on the abstraction it actually needs. That might be a repository, use case, or another state-producing component. A repository wrapper that adds no value is unnecessary.

### When to use

Choose the boundary based on ownership and reuse, not a rule that every class must have a repository.

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

Creating Repository and UseCase classes for every function can become ceremonial architecture.

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

**Key idea:** No. A ViewModel should depend on the abstraction it actually needs.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q6. How should loading, success, and error be modeled?

**Difficulty:** Intermediate

### Answer

Prefer an explicit immutable UI state that represents all information needed to render the screen. This avoids inconsistent combinations such as loading=false while stale data and an error flag accidentally conflict.

### When to use

Use a sealed hierarchy or a data class with clearly defined fields depending on the screen.

### When not to use / Common Mistake

Avoid several unrelated flows when their values must remain consistent.

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

**Key idea:** Prefer an explicit immutable UI state that represents all information needed to render the screen. This avoids inconsistent combinations such as loading=false while stale data and an error flag accidentally conflict.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you handle concurrent user actions in a ViewModel?

**Difficulty:** Advanced

### Answer

Define the desired semantics first: allow concurrency, cancel previous work, ignore duplicates, or serialize operations. Then encode that policy with coroutine jobs, Flow operators, mutexes, or repository transactions as appropriate.

### When to use

Search/typeahead often needs cancellation; independent uploads may need concurrency.

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

Launching every click blindly can create duplicate requests and race conditions.

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

**Key idea:** Define the desired semantics first: allow concurrency, cancel previous work, ignore duplicates, or serialize operations. Then encode that policy with coroutine jobs, Flow operators, mutexes, or repository transactions as appropriate.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you refactor a 1,500-line ViewModel?

**Difficulty:** Advanced

### Answer

Identify responsibilities by behavior rather than splitting methods arbitrarily. Extract reusable business rules, state transformations, data coordination, and feature-specific subcomponents while keeping one clear screen state contract.

### When to use

Refactor incrementally with characterization tests around existing behavior.

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

Do not create dozens of tiny classes merely to reduce line count.

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

**Key idea:** Identify responsibilities by behavior rather than splitting methods arbitrarily. Extract reusable business rules, state transformations, data coordination, and feature-specific subcomponents while keeping one clear screen state contract.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
