# State Management

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What is UI state?

**Difficulty:** Basic

### Answer

UI state is the complete set of information required to render a screen correctly at a point in time. It should be explicit enough that rendering does not require hidden mutable state.

### When to use

Model state around what the UI needs, not around the shape of database tables.

### When not to use / Common Mistake

Multiple unrelated flags can create impossible combinations.

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

**Key idea:** UI state is the complete set of information required to render a screen correctly at a point in time. It should be explicit enough that rendering does not require hidden mutable state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Why prefer a single UiState object?

**Difficulty:** Intermediate

### Answer

Related values emitted together stay consistent. A single state object makes rendering and state restoration easier to reason about.

### When to use

Use separate streams only when their lifecycles and consistency requirements are genuinely independent.

### When not to use / Common Mistake

A dozen StateFlows for one screen can create race conditions between emissions.

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

**Key idea:** Related values emitted together stay consistent. A single state object makes rendering and state restoration easier to reason about.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. StateFlow vs Compose mutableStateOf?

**Difficulty:** Intermediate

### Answer

Both can represent observable state. StateFlow integrates naturally with coroutine/data streams and ViewModel APIs; Compose state is convenient for local UI state. Choose based on ownership and interoperability.

### When to use

Keep transient widget state local; expose screen-level application state from a state holder.

### Example

```kotlin
private val _uiState = MutableStateFlow(UiState())
val uiState: StateFlow<UiState> = _uiState.asStateFlow()
```

### When not to use / Common Mistake

Using one mechanism everywhere regardless of ownership can make state boundaries unclear.

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

**Key idea:** Both can represent observable state. StateFlow integrates naturally with coroutine/data streams and ViewModel APIs; Compose state is convenient for local UI state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. What state should live in the composable?

**Difficulty:** Basic

### Answer

Ephemeral presentation state tightly coupled to a small UI element can live locally, such as an expanded menu or text-field draft. State needed across recomposition, configuration changes, business logic, or multiple UI elements usually belongs in a higher-level state holder.

### When to use

Hoist state when another component needs to own or coordinate it.

### When not to use / Common Mistake

Putting repository data into remember creates an incorrect ownership boundary.

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

**Key idea:** Ephemeral presentation state tightly coupled to a small UI element can live locally, such as an expanded menu or text-field draft. State needed across recomposition, configuration changes, business logic, or multiple UI elements usually belongs in a higher-level state holder.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What is state hoisting?

**Difficulty:** Basic

### Answer

State hoisting moves state ownership to the lowest common ancestor that needs to read or change it, while child composables receive state and callbacks.

### When to use

Use it to make reusable composables stateless and easier to test.

### When not to use / Common Mistake

Hoisting everything to the top of the app can make APIs and ownership unnecessarily complex.

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

**Key idea:** State hoisting moves state ownership to the lowest common ancestor that needs to read or change it, while child composables receive state and callbacks.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How do you handle state restoration after process death?

**Difficulty:** Advanced

### Answer

Do not rely on ViewModel alone: it survives configuration changes but not process death. Persist important state in durable storage or use saved-state mechanisms for small transient navigation/UI state.

### When to use

Classify state by whether it is reconstructable, user input, navigation state, or durable application data.

### When not to use / Common Mistake

Saving every object into a Bundle can exceed limits and creates brittle serialization.

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

**Key idea:** Do not rely on ViewModel alone: it survives configuration changes but not process death. Persist important state in durable storage or use saved-state mechanisms for small transient navigation/UI state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How should loading and stale data coexist?

**Difficulty:** Advanced

### Answer

A screen can have usable stale data while a refresh is running. Model these independently instead of replacing data with a full-screen loading state whenever a refresh starts.

### When to use

Represent refresh indicators separately from whether content is available.

### When not to use / Common Mistake

Treating every request as either loading or content often causes UI flicker.

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

**Key idea:** A screen can have usable stale data while a refresh is running. Model these independently instead of replacing data with a full-screen loading state whenever a refresh starts.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How do you prevent state races?

**Difficulty:** Advanced

### Answer

Centralize ownership, serialize conflicting writes, use immutable snapshots, and define ordering semantics. Database transactions or Mutex may be needed when multiple coroutines update the same logical state.

### When to use

Use structured concurrency and explicit state transitions.

### When not to use / Common Mistake

Assuming coroutine launches execute in the order they were called can produce subtle bugs.

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

**Key idea:** Centralize ownership, serialize conflicting writes, use immutable snapshots, and define ordering semantics. Database transactions or Mutex may be needed when multiple coroutines update the same logical state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
