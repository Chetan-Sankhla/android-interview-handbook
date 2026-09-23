# Modularization

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. Why modularize an Android app?

**Difficulty:** Basic

### Answer

Modules can isolate features, enforce dependency direction, improve ownership, enable reuse, and sometimes reduce build work through better task boundaries.

### When to use

Modularize when codebase size, team boundaries, build time, or feature isolation justifies it.

### When not to use / Common Mistake

Modules are not automatically beneficial if every small component becomes its own module.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Modules can isolate features, enforce dependency direction, improve ownership, enable reuse, and sometimes reduce build work through better task boundaries.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Feature modules vs core modules?

**Difficulty:** Intermediate

### Answer

Feature modules represent product capabilities; core/common modules contain reusable infrastructure or shared contracts. A healthy graph prevents feature A from reaching into feature B implementation details.

### When to use

Keep shared modules stable and narrow.

### When not to use / Common Mistake

A giant core module becomes a dumping ground that couples the whole app.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Feature modules represent product capabilities; core/common modules contain reusable infrastructure or shared contracts. A healthy graph prevents feature A from reaching into feature B implementation details.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is an API/implementation module split?

**Difficulty:** Advanced

### Answer

The API module exposes the minimal contract other modules need, while the implementation module contains concrete UI/data/navigation code. This reduces dependency leakage and can enable independently composed features.

### When to use

This is especially useful for large feature graphs and modular navigation.

### When not to use / Common Mistake

Putting implementation classes in the API module defeats the boundary.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** The API module exposes the minimal contract other modules need, while the implementation module contains concrete UI/data/navigation code. This reduces dependency leakage and can enable independently composed features.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How do you prevent circular module dependencies?

**Difficulty:** Intermediate

### Answer

Move shared contracts to a lower-level module, invert a dependency with an interface, or introduce an orchestrator at a higher level. Do not solve cycles by making everything depend on a giant common module.

### When to use

Draw the intended dependency graph before creating modules.

### When not to use / Common Mistake

Circularity is usually a boundary design problem, not a Gradle problem.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Move shared contracts to a lower-level module, invert a dependency with an interface, or introduce an orchestrator at a higher level. Do not solve cycles by making everything depend on a giant common module.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. Should every feature have data/domain/ui modules?

**Difficulty:** Intermediate

### Answer

Not necessarily. Module granularity should follow ownership and dependency needs. A feature may start as one module and split when implementation isolation or build/team constraints justify it.

### When to use

Use package-level separation before module-level separation when the project is small.

### When not to use / Common Mistake

Copying a 100-module architecture into a small app creates friction.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Not necessarily. Module granularity should follow ownership and dependency needs.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How does modularization affect testing?

**Difficulty:** Intermediate

### Answer

Smaller modules can make unit tests more focused and allow feature-level integration tests. Stable contracts also make fake implementations easier.

### When to use

Define test ownership along module boundaries.

### When not to use / Common Mistake

A module boundary alone does not make code testable if classes remain tightly coupled internally.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Smaller modules can make unit tests more focused and allow feature-level integration tests. Stable contracts also make fake implementations easier.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How can modularization improve build performance?

**Difficulty:** Advanced

### Answer

A well-designed dependency graph can reduce the amount of code affected by changes and allow Gradle to reuse more work. The actual benefit depends on module size, annotation processing, source sets, and dependency graph.

### When to use

Measure build times before and after modularization.

### When not to use / Common Mistake

Creating hundreds of modules without measuring can increase configuration and dependency overhead.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A well-designed dependency graph can reduce the amount of code affected by changes and allow Gradle to reuse more work. The actual benefit depends on module size, annotation processing, source sets, and dependency graph.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you modularize a large legacy app incrementally?

**Difficulty:** Advanced

### Answer

Start with a dependency inventory, select one feature with a clear boundary, extract it, add API contracts, and enforce dependencies. Repeat while preventing new cross-feature coupling.

### When to use

Use architectural tests and code ownership to keep extracted boundaries intact.

### When not to use / Common Mistake

Extracting packages without changing dependency direction is only cosmetic modularization.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you decide where one feature module ends and another begins?**

<details>
<summary>Reveal sample answer</summary>

Align boundaries with ownership, dependency direction, and independently changeable functionality. Avoid splitting classes into modules merely by package type.

</details>

2. **How do you prevent circular module dependencies?**

<details>
<summary>Reveal sample answer</summary>

Keep dependencies directed toward lower-level shared modules or clearly defined feature contracts. If two features need each other directly, introduce a smaller shared abstraction rather than reversing the dependency graph.

</details>

3. **When does modularization become over-engineering?**

<details>
<summary>Reveal sample answer</summary>

When modules are tiny, tightly coupled, or require frequent cross-module coordination without reducing build impact or ownership ambiguity. The goal is useful isolation, not a maximum module count.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Start with a dependency inventory, select one feature with a clear boundary, extract it, add API contracts, and enforce dependencies. Repeat while preventing new cross-feature coupling.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
