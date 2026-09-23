# MVI and Unidirectional Data Flow

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What is unidirectional data flow (UDF)?

**Difficulty:** Basic

### Answer

State flows toward the UI and user events flow back to the state holder. The state holder processes events and produces a new state. This creates a predictable feedback loop.

### When to use

Use UDF for screens where explicit state transitions improve reasoning and testing.

### When not to use / Common Mistake

UDF is a flow of information, not a requirement to use a specific MVI library.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you model one-time effects in an MVI architecture?**

<details>
<summary>Reveal sample answer</summary>

Keep durable screen state separate from transient effects such as navigation or snackbars. The effect channel should have explicit delivery semantics rather than being encoded as a boolean in persistent UI state.

</details>

2. **What belongs in the reducer and what should stay outside it?**

<details>
<summary>Reveal sample answer</summary>

Reducers should be deterministic state transformations. Network calls, database writes, and other side effects belong in effect-handling or orchestration code, not inside the reducer.

</details>

3. **How would you test an MVI ViewModel?**

<details>
<summary>Reveal sample answer</summary>

Given an initial state and an intent, assert the resulting state and emitted effects. Keep reducers pure where possible so most state-transition tests do not require Android framework components.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** State flows toward the UI and user events flow back to the state holder. The state holder processes events and produces a new state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What is MVI?

**Difficulty:** Basic

### Answer

MVI is commonly implemented as an event/intent-driven state model where a reducer or state producer turns inputs into new UI state, with side effects handled explicitly. Implementations vary, so the architectural idea matters more than naming.

### When to use

It is useful for complex screens with many states and transitions.

### When not to use / Common Mistake

Do not assume every MVI implementation is identical or that MVI is always better than simpler UDF.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you model one-time effects in an MVI architecture?**

<details>
<summary>Reveal sample answer</summary>

Keep durable screen state separate from transient effects such as navigation or snackbars. The effect channel should have explicit delivery semantics rather than being encoded as a boolean in persistent UI state.

</details>

2. **What belongs in the reducer and what should stay outside it?**

<details>
<summary>Reveal sample answer</summary>

Reducers should be deterministic state transformations. Network calls, database writes, and other side effects belong in effect-handling or orchestration code, not inside the reducer.

</details>

3. **How would you test an MVI ViewModel?**

<details>
<summary>Reveal sample answer</summary>

Given an initial state and an intent, assert the resulting state and emitted effects. Keep reducers pure where possible so most state-transition tests do not require Android framework components.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** MVI is commonly implemented as an event/intent-driven state model where a reducer or state producer turns inputs into new UI state, with side effects handled explicitly. Implementations vary, so the architectural idea matters more than naming.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. StateFlow vs SharedFlow for UI state and events?

**Difficulty:** Intermediate

### Answer

StateFlow represents current state and always has a latest value. SharedFlow is better suited to broadcasts/events where replay semantics are chosen explicitly. For durable screen state, StateFlow is generally the natural fit.

### When to use

Use event streams carefully and define what happens if the collector is absent.

### When not to use / Common Mistake

Using SharedFlow for state can make restoration and current-value semantics harder.

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

**Key idea:** StateFlow represents current state and always has a latest value. SharedFlow is better suited to broadcasts/events where replay semantics are chosen explicitly.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How should one-time UI events be handled?

**Difficulty:** Intermediate

### Answer

First ask whether the event is actually state. Navigation, snackbar messages, or transient effects can require event semantics, but many apparent one-time events are better represented by state that the UI acknowledges.

### When to use

Define delivery and retry semantics before choosing a mechanism.

### When not to use / Common Mistake

A boolean like showToast=true can easily be re-triggered after recreation unless consumption is modeled carefully.

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

**Key idea:** First ask whether the event is actually state. Navigation, snackbar messages, or transient effects can require event semantics, but many apparent one-time events are better represented by state that the UI acknowledges.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What is a reducer and when is it useful?

**Difficulty:** Intermediate

### Answer

A reducer is a deterministic transformation from current state plus an action/event into new state. It is useful because pure state transitions are easy to test exhaustively.

### When to use

Use reducers for complex state machines or highly interactive screens.

### When not to use / Common Mistake

Do not force network/database side effects into a pure reducer.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you model one-time effects in an MVI architecture?**

<details>
<summary>Reveal sample answer</summary>

Keep durable screen state separate from transient effects such as navigation or snackbars. The effect channel should have explicit delivery semantics rather than being encoded as a boolean in persistent UI state.

</details>

2. **What belongs in the reducer and what should stay outside it?**

<details>
<summary>Reveal sample answer</summary>

Reducers should be deterministic state transformations. Network calls, database writes, and other side effects belong in effect-handling or orchestration code, not inside the reducer.

</details>

3. **How would you test an MVI ViewModel?**

<details>
<summary>Reveal sample answer</summary>

Given an initial state and an intent, assert the resulting state and emitted effects. Keep reducers pure where possible so most state-transition tests do not require Android framework components.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A reducer is a deterministic transformation from current state plus an action/event into new state. It is useful because pure state transitions are easy to test exhaustively.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How should side effects be separated from state reduction?

**Difficulty:** Advanced

### Answer

Keep pure state calculation deterministic and execute network, database, navigation, or analytics side effects in a controlled effect layer or coordinator. Feed meaningful results back as events/state changes.

### When to use

This separation makes state transitions unit-testable and prevents hidden effects.

### When not to use / Common Mistake

Calling arbitrary suspend functions from a reducer destroys its predictability.

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

**Key idea:** Keep pure state calculation deterministic and execute network, database, navigation, or analytics side effects in a controlled effect layer or coordinator. Feed meaningful results back as events/state changes.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. When is MVI overkill?

**Difficulty:** Advanced

### Answer

If a screen has only a few independent fields and simple interactions, a straightforward ViewModel with immutable UiState may provide the same benefits with less ceremony.

### When to use

Choose MVI when the explicit event/state model pays for itself.

### When not to use / Common Mistake

Do not adopt MVI solely because it sounds more scalable.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you model one-time effects in an MVI architecture?**

<details>
<summary>Reveal sample answer</summary>

Keep durable screen state separate from transient effects such as navigation or snackbars. The effect channel should have explicit delivery semantics rather than being encoded as a boolean in persistent UI state.

</details>

2. **What belongs in the reducer and what should stay outside it?**

<details>
<summary>Reveal sample answer</summary>

Reducers should be deterministic state transformations. Network calls, database writes, and other side effects belong in effect-handling or orchestration code, not inside the reducer.

</details>

3. **How would you test an MVI ViewModel?**

<details>
<summary>Reveal sample answer</summary>

Given an initial state and an intent, assert the resulting state and emitted effects. Keep reducers pure where possible so most state-transition tests do not require Android framework components.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** If a screen has only a few independent fields and simple interactions, a straightforward ViewModel with immutable UiState may provide the same benefits with less ceremony.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you test an MVI screen?

**Difficulty:** Advanced

### Answer

Test reducer/state transitions as pure functions, then test effect orchestration separately with fakes. Add UI tests for rendering and critical interaction paths.

### When to use

Cover success, failure, retry, cancellation, duplicate events, and restoration semantics.

### When not to use / Common Mistake

Testing only that a button exists does not validate the state machine.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you model one-time effects in an MVI architecture?**

<details>
<summary>Reveal sample answer</summary>

Keep durable screen state separate from transient effects such as navigation or snackbars. The effect channel should have explicit delivery semantics rather than being encoded as a boolean in persistent UI state.

</details>

2. **What belongs in the reducer and what should stay outside it?**

<details>
<summary>Reveal sample answer</summary>

Reducers should be deterministic state transformations. Network calls, database writes, and other side effects belong in effect-handling or orchestration code, not inside the reducer.

</details>

3. **How would you test an MVI ViewModel?**

<details>
<summary>Reveal sample answer</summary>

Given an initial state and an intent, assert the resulting state and emitted effects. Keep reducers pure where possible so most state-transition tests do not require Android framework components.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Test reducer/state transitions as pure functions, then test effect orchestration separately with fakes. Add UI tests for rendering and critical interaction paths.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
