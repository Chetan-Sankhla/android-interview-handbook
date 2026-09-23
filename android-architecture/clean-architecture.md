# Clean Architecture

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What is the dependency rule in Clean Architecture?

**Difficulty:** Basic

### Answer

Dependencies should point inward toward policies and abstractions rather than outward toward volatile infrastructure. The exact number of layers is less important than protecting business rules from UI/framework details.

### When to use

Use interfaces or stable contracts at boundaries where implementation replacement matters.

### When not to use / Common Mistake

Do not interpret Clean Architecture as a fixed folder structure.

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

**Key idea:** Dependencies should point inward toward policies and abstractions rather than outward toward volatile infrastructure. The exact number of layers is less important than protecting business rules from UI/framework details.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What belongs in the domain layer?

**Difficulty:** Intermediate

### Answer

Reusable business rules, entities/value concepts meaningful to the business, and use cases that coordinate those rules can belong there. Android-specific APIs should generally stay outside pure domain logic.

### When to use

Introduce domain types when data-layer or UI models do not represent the business concept cleanly.

### When not to use / Common Mistake

Do not duplicate every DTO into a domain model without a reason.

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

**Key idea:** Reusable business rules, entities/value concepts meaningful to the business, and use cases that coordinate those rules can belong there. Android-specific APIs should generally stay outside pure domain logic.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. Should domain depend on Android classes?

**Difficulty:** Intermediate

### Answer

Prefer not to. Keeping domain logic framework-independent improves portability and unit testing. Some practical apps may accept limited dependencies when the trade-off is deliberate.

### When to use

Keep time, dispatchers, persistence, and Android context behind boundaries when they are not intrinsic to the rule.

### When not to use / Common Mistake

Passing Context into use cases just because it is convenient often leaks framework concerns inward.

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

**Key idea:** Prefer not to. Keeping domain logic framework-independent improves portability and unit testing.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. What is the difference between a DTO, entity, and UI model?

**Difficulty:** Intermediate

### Answer

A DTO mirrors an external data contract, a persistence entity models storage, and a UI model represents what the screen needs. They can sometimes be the same type, but separate models are valuable when contracts evolve independently.

### When to use

Map models at boundaries when that prevents coupling or invalid states.

### When not to use / Common Mistake

Mapping every object mechanically can create noise and performance overhead without improving isolation.

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

**Key idea:** A DTO mirrors an external data contract, a persistence entity models storage, and a UI model represents what the screen needs. They can sometimes be the same type, but separate models are valuable when contracts evolve independently.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What is a use case and when should you create one?

**Difficulty:** Intermediate

### Answer

A use case represents a meaningful application operation or business workflow. It is useful when logic is reused, non-trivial, or deserves independent testing.

### When to use

Create one around a real behavior, not merely because a repository method exists.

### Example

```kotlin
class PlaceOrderUseCase(
    private val repository: OrderRepository
) {
    suspend operator fun invoke(cart: Cart) =
        repository.placeOrder(cart)
}
```

### When not to use / Common Mistake

A UseCase class containing one line that only delegates to a repository may be unnecessary.

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

**Key idea:** A use case represents a meaningful application operation or business workflow. It is useful when logic is reused, non-trivial, or deserves independent testing.

### Interview Insight

The important judgment is whether the operation has meaningful business rules or orchestration. Do not create one-line wrappers just to satisfy a folder structure.
## Q6. Is Repository part of Clean Architecture?

**Difficulty:** Advanced

### Answer

Repository is a common boundary for data access and coordination, but Clean Architecture does not mandate one exact repository implementation. Android guidance commonly places repositories in the data layer.

### When to use

Use repositories to hide data sources and expose application-oriented data operations.

### Example

```kotlin
interface UserRepository {
    fun observeUser(id: String): Flow<User>
    suspend fun refreshUser(id: String)
}
```

### When not to use / Common Mistake

Do not make repositories god objects that contain UI state and every business rule.

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

**Key idea:** Repository is a common boundary for data access and coordination, but Clean Architecture does not mandate one exact repository implementation. Android guidance commonly places repositories in the data layer.

### Interview Insight

Focus on the boundary a repository provides: consumers should not need to know whether data comes from a database, network, cache, or another source.
## Q7. How do you avoid Clean Architecture becoming over-engineering?

**Difficulty:** Advanced

### Answer

Apply boundaries where they buy independence, reuse, testability, or team scalability. Keep simple flows simple and introduce layers when complexity appears.

### When to use

Review abstraction cost during code review and remove wrappers that have no meaningful responsibility.

### When not to use / Common Mistake

More interfaces and packages do not automatically mean better architecture.

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

**Key idea:** Apply boundaries where they buy independence, reuse, testability, or team scalability. Keep simple flows simple and introduce layers when complexity appears.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you migrate a legacy MVC app toward Clean Architecture?

**Difficulty:** Advanced

### Answer

Start with a high-value feature, identify its current responsibilities, extract data access behind a repository, move screen state into a state holder, and add tests before broad migration. Repeat incrementally.

### When to use

Use strangler-style migration rather than rewriting the whole application.

### When not to use / Common Mistake

A big-bang rewrite can delay product work and introduce behavioral regressions.

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

**Key idea:** Start with a high-value feature, identify its current responsibilities, extract data access behind a repository, move screen state into a state holder, and add tests before broad migration. Repeat incrementally.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
