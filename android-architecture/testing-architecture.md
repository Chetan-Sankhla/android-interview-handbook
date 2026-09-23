# Testing Architecture

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What should be unit tested in an Android architecture?

**Difficulty:** Basic

### Answer

Pure business rules, reducers, state transformations, mapping, repository policies, and ViewModel behavior are strong unit-test candidates. Framework integration belongs in focused integration or UI tests.

### When to use

Maximize fast deterministic tests around logic.

### When not to use / Common Mistake

Do not turn every private helper into a separate test target.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Where should business rules live?**

<details>
<summary>Reveal sample answer</summary>

Put business rules at the boundary where they are independent of UI and concrete data sources. The exact number of layers matters less than keeping dependencies pointed toward stable abstractions.

</details>

2. **When does Clean Architecture become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When abstractions exist without a meaningful change boundary, especially for simple features. Add layers when they isolate business rules, data sources, or independent ownership—not because the template requires them.

</details>

3. **How would you introduce this architecture into an existing app?**

<details>
<summary>Reveal sample answer</summary>

Start with one feature that has clear pain or active development, establish the desired boundaries, add tests around current behavior, and migrate incrementally while old and new paths can coexist.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Pure business rules, reducers, state transformations, mapping, repository policies, and ViewModel behavior are strong unit-test candidates. Framework integration belongs in focused integration or UI tests.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Why does decoupling improve testing?

**Difficulty:** Basic

### Answer

A decoupled class can be instantiated with fakes and exercised without the Android runtime. This makes tests smaller, faster, and less flaky.

### When to use

Prefer interfaces or constructor parameters when replacement is valuable.

### When not to use / Common Mistake

Adding an interface without reducing coupling does not automatically improve testability.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be a unit test versus an integration test here?**

<details>
<summary>Reveal sample answer</summary>

Unit-test deterministic business and state-transition logic. Use integration tests for database/network/framework boundaries and UI tests for critical end-to-end user behavior.

</details>

2. **Which failure cases are worth testing?**

<details>
<summary>Reveal sample answer</summary>

Prioritize failures that change user-visible behavior or data integrity: empty states, errors, cancellation, retries, corrupted data, and lifecycle interruption where relevant.

</details>

3. **How do you keep architecture testable without over-abstracting?**

<details>
<summary>Reveal sample answer</summary>

Create seams around real sources of change such as network, database, clock, or dispatcher. Avoid interfaces that exist only to make a trivial class mockable.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A decoupled class can be instantiated with fakes and exercised without the Android runtime. This makes tests smaller, faster, and less flaky.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How do you test a ViewModel using Flow?

**Difficulty:** Intermediate

### Answer

Use deterministic test dispatchers and fake repositories, collect the UI state, trigger events, and assert the resulting sequence or final state.

### When to use

Test cancellation, errors, retries, and concurrent events where relevant.

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

Tests that rely on arbitrary delays are often flaky.

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

**Key idea:** Use deterministic test dispatchers and fake repositories, collect the UI state, trigger events, and assert the resulting sequence or final state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How should repositories be tested with a local database?

**Difficulty:** Intermediate

### Answer

Use an in-memory or test database when validating SQL, transactions, mappings, and observable queries. Keep pure source-selection logic separately unit-tested when useful.

### When to use

Use realistic schema and migration tests for important persistence behavior.

### When not to use / Common Mistake

Mocking Room everywhere means SQL and transaction behavior are never exercised.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be a unit test versus an integration test here?**

<details>
<summary>Reveal sample answer</summary>

Unit-test deterministic business and state-transition logic. Use integration tests for database/network/framework boundaries and UI tests for critical end-to-end user behavior.

</details>

2. **Which failure cases are worth testing?**

<details>
<summary>Reveal sample answer</summary>

Prioritize failures that change user-visible behavior or data integrity: empty states, errors, cancellation, retries, corrupted data, and lifecycle interruption where relevant.

</details>

3. **How do you keep architecture testable without over-abstracting?**

<details>
<summary>Reveal sample answer</summary>

Create seams around real sources of change such as network, database, clock, or dispatcher. Avoid interfaces that exist only to make a trivial class mockable.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Use an in-memory or test database when validating SQL, transactions, mappings, and observable queries. Keep pure source-selection logic separately unit-tested when useful.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What should UI tests verify?

**Difficulty:** Basic

### Answer

UI tests should validate critical user-visible behavior and interactions rather than implementation details. They are particularly useful for navigation, rendering states, accessibility, and end-to-end feature flows.

### When to use

Keep the suite focused on high-value journeys.

### When not to use / Common Mistake

Asserting internal ViewModel calls from UI tests makes them brittle.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be a unit test versus an integration test here?**

<details>
<summary>Reveal sample answer</summary>

Unit-test deterministic business and state-transition logic. Use integration tests for database/network/framework boundaries and UI tests for critical end-to-end user behavior.

</details>

2. **Which failure cases are worth testing?**

<details>
<summary>Reveal sample answer</summary>

Prioritize failures that change user-visible behavior or data integrity: empty states, errors, cancellation, retries, corrupted data, and lifecycle interruption where relevant.

</details>

3. **How do you keep architecture testable without over-abstracting?**

<details>
<summary>Reveal sample answer</summary>

Create seams around real sources of change such as network, database, clock, or dispatcher. Avoid interfaces that exist only to make a trivial class mockable.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** UI tests should validate critical user-visible behavior and interactions rather than implementation details. They are particularly useful for navigation, rendering states, accessibility, and end-to-end feature flows.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How do fakes compare with mocks?

**Difficulty:** Intermediate

### Answer

Fakes implement useful behavior and often produce more realistic tests; mocks verify specific interactions. Fakes are valuable for repositories and data sources when behavior matters more than exact calls.

### When to use

Prefer the least coupled test double that proves the behavior.

### When not to use / Common Mistake

Overusing mocks can lock tests to implementation details.

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

**Key idea:** Fakes implement useful behavior and often produce more realistic tests; mocks verify specific interactions. Fakes are valuable for repositories and data sources when behavior matters more than exact calls.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you test architecture boundaries?

**Difficulty:** Advanced

### Answer

Use static analysis, Gradle dependency constraints, module graphs, or custom lint/architecture tests to prevent forbidden dependencies.

### When to use

Automate rules such as feature-to-feature dependency restrictions.

### When not to use / Common Mistake

Documenting architecture without enforcement lets boundaries erode over time.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Where should business rules live?**

<details>
<summary>Reveal sample answer</summary>

Put business rules at the boundary where they are independent of UI and concrete data sources. The exact number of layers matters less than keeping dependencies pointed toward stable abstractions.

</details>

2. **When does Clean Architecture become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When abstractions exist without a meaningful change boundary, especially for simple features. Add layers when they isolate business rules, data sources, or independent ownership—not because the template requires them.

</details>

3. **How would you introduce this architecture into an existing app?**

<details>
<summary>Reveal sample answer</summary>

Start with one feature that has clear pain or active development, establish the desired boundaries, add tests around current behavior, and migrate incrementally while old and new paths can coexist.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Use static analysis, Gradle dependency constraints, module graphs, or custom lint/architecture tests to prevent forbidden dependencies.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you build a testing strategy for a large Android app?

**Difficulty:** Advanced

### Answer

Use many fast unit tests, focused integration tests for data/DI/navigation, and a smaller set of critical UI/end-to-end tests. Prioritize failure-prone business paths and user journeys.

### When to use

Track flakiness and test execution time as engineering metrics.

### When not to use / Common Mistake

Trying to cover everything with end-to-end tests produces slow, fragile feedback.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be a unit test versus an integration test here?**

<details>
<summary>Reveal sample answer</summary>

Unit-test deterministic business and state-transition logic. Use integration tests for database/network/framework boundaries and UI tests for critical end-to-end user behavior.

</details>

2. **Which failure cases are worth testing?**

<details>
<summary>Reveal sample answer</summary>

Prioritize failures that change user-visible behavior or data integrity: empty states, errors, cancellation, retries, corrupted data, and lifecycle interruption where relevant.

</details>

3. **How do you keep architecture testable without over-abstracting?**

<details>
<summary>Reveal sample answer</summary>

Create seams around real sources of change such as network, database, clock, or dispatcher. Avoid interfaces that exist only to make a trivial class mockable.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Use many fast unit tests, focused integration tests for data/DI/navigation, and a smaller set of critical UI/end-to-end tests. Prioritize failure-prone business paths and user journeys.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
