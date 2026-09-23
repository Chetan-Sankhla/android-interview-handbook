# Dependency Injection

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What is dependency injection?

**Difficulty:** Basic

### Answer

Dependency injection means a class receives the objects it needs instead of constructing them internally. This makes dependencies explicit and replaceable.

### When to use

Constructor injection is the default choice for ordinary classes.

### Example

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    fun provideApi(retrofit: Retrofit): UserApi =
        retrofit.create(UserApi::class.java)
}
```

### When not to use / Common Mistake

A service locator is not the same as dependency injection; it hides dependencies behind lookup.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you choose the scope of a dependency?**

<details>
<summary>Reveal sample answer</summary>

Scope it to the shortest lifecycle that safely owns the resource. Singleton scope is appropriate for truly process-wide resources, while feature or ViewModel-scoped dependencies should not be promoted globally without a reason.

</details>

2. **How does dependency injection improve testing?**

<details>
<summary>Reveal sample answer</summary>

The consumer receives an interface or dependency that can be replaced with a fake or test implementation. This removes construction logic from the behavior under test.

</details>

3. **What problems can excessive DI introduce?**

<details>
<summary>Reveal sample answer</summary>

Too many interfaces, modules, scopes, and providers can obscure simple construction and make the graph difficult to understand. Add DI boundaries where replacement or lifetime management is actually useful.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Dependency injection means a class receives the objects it needs instead of constructing them internally. This makes dependencies explicit and replaceable.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Why is constructor injection preferred?

**Difficulty:** Basic

### Answer

It makes required dependencies visible, keeps objects valid after construction, and makes unit tests straightforward because tests can pass fakes directly.

### When to use

Use constructor injection whenever the framework does not require another injection style.

### When not to use / Common Mistake

Field injection hides required dependencies and makes plain unit construction harder.

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

**Key idea:** It makes required dependencies visible, keeps objects valid after construction, and makes unit tests straightforward because tests can pass fakes directly.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What problem does Hilt solve?

**Difficulty:** Intermediate

### Answer

Hilt provides standardized dependency containers and generated wiring for Android lifecycles on top of Dagger, reducing manual boilerplate. It also supports Android-specific scopes and testing integration.

### When to use

Use it when the app has enough dependency graph complexity that manual composition becomes costly.

### Example

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    fun provideApi(retrofit: Retrofit): UserApi =
        retrofit.create(UserApi::class.java)
}
```

### When not to use / Common Mistake

Do not add Hilt to make a tiny project appear more sophisticated.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you choose the scope of a dependency?**

<details>
<summary>Reveal sample answer</summary>

Scope it to the shortest lifecycle that safely owns the resource. Singleton scope is appropriate for truly process-wide resources, while feature or ViewModel-scoped dependencies should not be promoted globally without a reason.

</details>

2. **How does dependency injection improve testing?**

<details>
<summary>Reveal sample answer</summary>

The consumer receives an interface or dependency that can be replaced with a fake or test implementation. This removes construction logic from the behavior under test.

</details>

3. **What problems can excessive DI introduce?**

<details>
<summary>Reveal sample answer</summary>

Too many interfaces, modules, scopes, and providers can obscure simple construction and make the graph difficult to understand. Add DI boundaries where replacement or lifetime management is actually useful.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Hilt provides standardized dependency containers and generated wiring for Android lifecycles on top of Dagger, reducing manual boilerplate. It also supports Android-specific scopes and testing integration.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. What is a Hilt scope?

**Difficulty:** Intermediate

### Answer

A scope controls how long a provided dependency instance is retained within a component lifecycle. The scope should match the intended ownership lifetime.

### When to use

Use singleton scope only for genuinely application-wide objects.

### Example

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    fun provideApi(retrofit: Retrofit): UserApi =
        retrofit.create(UserApi::class.java)
}
```

### When not to use / Common Mistake

Over-scoping objects can retain memory longer than intended and make state unexpectedly global.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you choose the scope of a dependency?**

<details>
<summary>Reveal sample answer</summary>

Scope it to the shortest lifecycle that safely owns the resource. Singleton scope is appropriate for truly process-wide resources, while feature or ViewModel-scoped dependencies should not be promoted globally without a reason.

</details>

2. **How does dependency injection improve testing?**

<details>
<summary>Reveal sample answer</summary>

The consumer receives an interface or dependency that can be replaced with a fake or test implementation. This removes construction logic from the behavior under test.

</details>

3. **What problems can excessive DI introduce?**

<details>
<summary>Reveal sample answer</summary>

Too many interfaces, modules, scopes, and providers can obscure simple construction and make the graph difficult to understand. Add DI boundaries where replacement or lifetime management is actually useful.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A scope controls how long a provided dependency instance is retained within a component lifecycle. The scope should match the intended ownership lifetime.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What is the difference between @Provides and @Binds?

**Difficulty:** Intermediate

### Answer

@Provides supplies an instance using executable provider code and is useful when you construct third-party or complex objects. @Binds maps an implementation to an interface and is typically simpler for injectable implementations.

### When to use

Prefer constructor injection plus @Binds where possible; use @Provides for objects you cannot constructor-inject.

### When not to use / Common Mistake

Using @Provides for every interface implementation adds unnecessary boilerplate.

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

**Key idea:** @Provides supplies an instance using executable provider code and is useful when you construct third-party or complex objects. @Binds maps an implementation to an interface and is typically simpler for injectable implementations.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How does DI improve testing?

**Difficulty:** Intermediate

### Answer

Tests can construct classes with fake dependencies without booting the full DI graph. Integration tests can use Hilt when validating production-style wiring.

### When to use

Keep unit tests independent of Hilt where practical.

### When not to use / Common Mistake

If every unit test requires a DI framework, the architecture may be hiding dependencies rather than exposing them.

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

**Key idea:** Tests can construct classes with fake dependencies without booting the full DI graph. Integration tests can use Hilt when validating production-style wiring.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you avoid a giant SingletonComponent?

**Difficulty:** Advanced

### Answer

Keep lifetimes aligned with ownership, expose narrow interfaces, and avoid registering feature-specific state globally. Feature modules should contribute only what broader scopes genuinely need.

### When to use

Use activity/retained/view-model or custom component boundaries where appropriate.

### When not to use / Common Mistake

Making everything singleton because it is convenient creates hidden shared state.

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

**Key idea:** Keep lifetimes aligned with ownership, expose narrow interfaces, and avoid registering feature-specific state globally. Feature modules should contribute only what broader scopes genuinely need.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you migrate manual Dagger to Hilt?

**Difficulty:** Advanced

### Answer

Map the existing graph and lifetimes first, migrate the application root, then feature by feature while preserving interfaces and tests. Avoid changing architecture and DI framework simultaneously unless necessary.

### When to use

Use compatibility bridges during incremental migration.

### Example

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    fun provideApi(retrofit: Retrofit): UserApi =
        retrofit.create(UserApi::class.java)
}
```

### When not to use / Common Mistake

A mechanical annotation migration can accidentally change object lifetimes or initialization order.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you choose the scope of a dependency?**

<details>
<summary>Reveal sample answer</summary>

Scope it to the shortest lifecycle that safely owns the resource. Singleton scope is appropriate for truly process-wide resources, while feature or ViewModel-scoped dependencies should not be promoted globally without a reason.

</details>

2. **How does dependency injection improve testing?**

<details>
<summary>Reveal sample answer</summary>

The consumer receives an interface or dependency that can be replaced with a fake or test implementation. This removes construction logic from the behavior under test.

</details>

3. **What problems can excessive DI introduce?**

<details>
<summary>Reveal sample answer</summary>

Too many interfaces, modules, scopes, and providers can obscure simple construction and make the graph difficult to understand. Add DI boundaries where replacement or lifetime management is actually useful.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Map the existing graph and lifetimes first, migrate the application root, then feature by feature while preserving interfaces and tests. Avoid changing architecture and DI framework simultaneously unless necessary.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
