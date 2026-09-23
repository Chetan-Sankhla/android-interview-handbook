# Android Architecture System Design

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. How would you design the architecture of a large e-commerce Android app?

**Difficulty:** Advanced

### Answer

Separate features by business capability, use a clear UI/data boundary, centralize cross-cutting infrastructure without making it a god module, and use local persistence for appropriate offline/read performance. Navigation and DI should compose features without exposing implementation details.

### When to use

Discuss catalog, cart, checkout, authentication, payments, caching, analytics, offline behavior, modularization, and observability.

### When not to use / Common Mistake

Do not jump directly to libraries; start with requirements and failure modes.

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

**Key idea:** Separate features by business capability, use a clear UI/data boundary, centralize cross-cutting infrastructure without making it a god module, and use local persistence for appropriate offline/read performance. Navigation and DI should compose features without exposing implementation details.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. How would you design a feed for millions of users?

**Difficulty:** Advanced

### Answer

Treat the local database as a renderable cache/source for the client, page incrementally, cancel obsolete requests, persist useful data, and synchronize efficiently. Server-side pagination and API design strongly influence the client architecture.

### When to use

Discuss cache invalidation, pagination keys, refresh semantics, memory pressure, and analytics.

### When not to use / Common Mistake

Loading the entire feed into memory or treating refresh as a full replacement is not scalable.

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

**Key idea:** Treat the local database as a renderable cache/source for the client, page incrementally, cancel obsolete requests, persist useful data, and synchronize efficiently. Server-side pagination and API design strongly influence the client architecture.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How would you design an offline-capable notes app?

**Difficulty:** Advanced

### Answer

Persist notes locally, expose local state to UI, record pending mutations, synchronize in the background, and define conflict resolution. IDs and operation metadata should be stable across retries.

### When to use

Discuss encryption, sync conflicts, deletion tombstones, attachments, and multi-device edits.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### When not to use / Common Mistake

A simple last-write-wins rule may be insufficient for collaborative edits.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should be the source of truth when the network and database disagree?**

<details>
<summary>Reveal sample answer</summary>

Define the local consistency policy explicitly. In an offline-first design, the database is commonly the UI source of truth while synchronization reconciles it with the server.

</details>

2. **How would you handle a sync conflict?**

<details>
<summary>Reveal sample answer</summary>

Choose a product-level conflict policy such as server-wins, client-wins, merge, or explicit conflict resolution. The important point is that the policy is deterministic and testable.

</details>

3. **How do you test offline behavior?**

<details>
<summary>Reveal sample answer</summary>

Run the feature with the network unavailable, verify cached data and queued work, then restore connectivity and verify synchronization and error recovery.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Persist notes locally, expose local state to UI, record pending mutations, synchronize in the background, and define conflict resolution. IDs and operation metadata should be stable across retries.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How would you design a multi-module Android app for 20 engineers?

**Difficulty:** Advanced

### Answer

Create feature boundaries aligned with team ownership, stable shared contracts, limited common modules, and automated dependency rules. Build tooling and code ownership become part of the architecture.

### When to use

Discuss API/implementation splits, navigation composition, DI, build performance, and release coordination.

### When not to use / Common Mistake

A shared utilities module owned by everyone often becomes the new monolith.

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

**Key idea:** Create feature boundaries aligned with team ownership, stable shared contracts, limited common modules, and automated dependency rules. Build tooling and code ownership become part of the architecture.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How would you design app-wide authentication state?

**Difficulty:** Advanced

### Answer

Keep credentials/token handling in a secure data/auth layer, expose an application-level auth state, and let features react without directly managing token storage. Expiry and refresh must have deterministic behavior.

### When to use

Discuss startup restoration, logout propagation, concurrent refresh, and unauthorized requests.

### When not to use / Common Mistake

Storing tokens in arbitrary preferences or letting every repository refresh independently causes security and consistency issues.

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

**Key idea:** Keep credentials/token handling in a secure data/auth layer, expose an application-level auth state, and let features react without directly managing token storage. Expiry and refresh must have deterministic behavior.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How would you design global error handling?

**Difficulty:** Advanced

### Answer

Define an error taxonomy at boundaries, centralize logging/observability, and let features decide user-facing behavior based on actionable categories. Avoid one global catch block that swallows context.

### When to use

Discuss authentication, validation, connectivity, server, storage, and unexpected errors separately.

### When not to use / Common Mistake

A generic “Something went wrong” object everywhere loses recovery semantics.

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

**Key idea:** Define an error taxonomy at boundaries, centralize logging/observability, and let features decide user-facing behavior based on actionable categories. Avoid one global catch block that swallows context.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How would you design feature flags in the architecture?

**Difficulty:** Advanced

### Answer

Expose feature configuration through a stable provider, cache evaluated values appropriately, and keep flag checks close to the decision they control. Remove stale flags after rollout.

### When to use

Discuss remote config failure, default values, experiment assignment, and testability.

### When not to use / Common Mistake

Scattering raw string flag keys across UI code creates unmanageable technical debt.

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

**Key idea:** Expose feature configuration through a stable provider, cache evaluated values appropriately, and keep flag checks close to the decision they control. Remove stale flags after rollout.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How do you evaluate architecture trade-offs in a system-design interview?

**Difficulty:** Advanced

### Answer

State requirements first, propose a simple baseline, identify bottlenecks/failure modes, then introduce complexity only where it addresses a requirement. Explain ownership, state flow, persistence, concurrency, and testing.

### When to use

Make assumptions explicit and distinguish must-have constraints from optimizations.

### When not to use / Common Mistake

Listing MVVM, Clean, Hilt, Room, and Retrofit without explaining why does not constitute architecture design.

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

**Key idea:** State requirements first, propose a simple baseline, identify bottlenecks/failure modes, then introduce complexity only where it addresses a requirement. Explain ownership, state flow, persistence, concurrency, and testing.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
