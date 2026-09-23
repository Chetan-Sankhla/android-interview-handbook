# Architecture Fundamentals

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What problem does application architecture solve?

**Difficulty:** Basic

### Answer

Architecture separates responsibilities, controls dependencies, and gives the app predictable places for state, business rules, data access, and UI behavior. Good architecture is less about a particular pattern and more about making change, testing, and failure manageable.

### When to use

For a small prototype, keep the structure proportional to complexity. For a long-lived product, explicitly define boundaries so features do not become tightly coupled.

### When not to use / Common Mistake

Do not confuse more layers with better architecture. Extra abstractions have maintenance cost.

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

**Key idea:** Architecture separates responsibilities, controls dependencies, and gives the app predictable places for state, business rules, data access, and UI behavior. Good architecture is less about a particular pattern and more about making change, testing, and failure manageable.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What are the typical UI, data, and optional domain layers in modern Android architecture?

**Difficulty:** Basic

### Answer

The UI layer renders UI state and handles user interaction; the data layer exposes application data and owns data sources; a domain layer is optional and is useful when business logic is reused or complex. Dependencies should generally point toward stable abstractions rather than letting UI code reach directly into low-level data sources.

### When to use

Use a domain layer when it reduces duplication or isolates meaningful business rules. Skip it when it would only wrap a single repository call.

### When not to use / Common Mistake

Avoid treating the three layers as mandatory packages for every feature.

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

**Key idea:** The UI layer renders UI state and handles user interaction; the data layer exposes application data and owns data sources; a domain layer is optional and is useful when business logic is reused or complex. Dependencies should generally point toward stable abstractions rather than letting UI code reach directly into low-level data sources.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. Why should Activities and Fragments contain as little business logic as possible?

**Difficulty:** Basic

### Answer

They are framework-managed entry points with lifecycle constraints and can be recreated. Keeping business logic there makes it harder to test and easier to lose state. Move screen state and logic into appropriate state holders such as ViewModels and domain/data components.

### When to use

An Activity can coordinate framework concerns such as permissions, intents, and lifecycle callbacks, but should not become the application service layer.

### When not to use / Common Mistake

Do not move every line out of an Activity mechanically; framework integration still belongs there.

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

**Key idea:** They are framework-managed entry points with lifecycle constraints and can be recreated. Keeping business logic there makes it harder to test and easier to lose state.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. What does separation of concerns mean in an Android app?

**Difficulty:** Basic

### Answer

Each component should have a focused responsibility and expose a small contract. UI renders and emits events, state holders coordinate UI state, repositories coordinate data sources, and domain components implement reusable business rules when needed.

### When to use

Use boundaries where independent change is likely.

### When not to use / Common Mistake

Over-fragmenting trivial code creates indirection without meaningful isolation.

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

**Key idea:** Each component should have a focused responsibility and expose a small contract. UI renders and emits events, state holders coordinate UI state, repositories coordinate data sources, and domain components implement reusable business rules when needed.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What makes an architecture testable?

**Difficulty:** Intermediate

### Answer

Business logic should be callable without Android framework objects, dependencies should be replaceable, and side effects should sit behind clear interfaces. This allows fast unit tests for state transitions and business rules, with a smaller number of integration/UI tests for framework behavior.

### When to use

Constructor injection and pure transformations are especially useful.

### When not to use / Common Mistake

A large test suite cannot compensate for code that is intrinsically coupled to framework state.

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

**Key idea:** Business logic should be callable without Android framework objects, dependencies should be replaceable, and side effects should sit behind clear interfaces. This allows fast unit tests for state transitions and business rules, with a smaller number of integration/UI tests for framework behavior.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. What is dependency direction and why does it matter?

**Difficulty:** Intermediate

### Answer

Dependency direction describes which modules or layers are allowed to depend on others. A stable boundary prevents implementation details from leaking upward and makes replacement or migration possible.

### When to use

Define module rules and enforce them with Gradle dependencies or architectural checks.

### When not to use / Common Mistake

A Kotlin interface alone does not create architectural isolation if the module graph still exposes implementation details.

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

**Key idea:** Dependency direction describes which modules or layers are allowed to depend on others. A stable boundary prevents implementation details from leaking upward and makes replacement or migration possible.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you choose an architecture for a new Android feature?

**Difficulty:** Advanced

### Answer

Start from requirements: state complexity, data sources, navigation, offline behavior, reuse, team size, and expected lifetime. Then choose the smallest set of boundaries that keeps those concerns independent.

### When to use

For a simple screen, UI plus ViewModel plus repository may be enough. For complex business workflows, introduce explicit domain/use-case boundaries.

### When not to use / Common Mistake

Do not begin by copying the architecture of a much larger product without validating that its complexity exists in the new feature.

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

**Key idea:** Start from requirements: state complexity, data sources, navigation, offline behavior, reuse, team size, and expected lifetime. Then choose the smallest set of boundaries that keeps those concerns independent.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you review the architecture of a large Android codebase?

**Difficulty:** Advanced

### Answer

Map dependency direction, feature boundaries, state ownership, data-source ownership, navigation coupling, testability, build-time cost, and common failure paths. Look for repeated patterns that cause defects rather than judging package names alone.

### When to use

Use dependency graphs, build metrics, test failure patterns, and representative feature walkthroughs.

### When not to use / Common Mistake

Do not propose a rewrite merely because the architecture is imperfect; prioritize boundaries that reduce current delivery risk.

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

**Key idea:** Map dependency direction, feature boundaries, state ownership, data-source ownership, navigation coupling, testability, build-time cost, and common failure paths. Look for repeated patterns that cause defects rather than judging package names alone.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
