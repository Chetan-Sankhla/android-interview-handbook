# Navigation and Deep Links

This section is part of the Android Interview Handbook. Questions are organized from fundamentals to Senior/Lead-level design. Answers focus on clear concepts, practical code, and progressively deeper interview follow-ups.

## Q1. What responsibilities should navigation have?

**Difficulty:** Basic

### Answer

Navigation should coordinate destinations, back-stack behavior, and route/deep-link interpretation. Screen business logic should remain outside navigation infrastructure.

### When to use

Keep navigation contracts small and pass stable identifiers rather than large objects.

### Example

```kotlin
composable("details/{id}") { entry ->
    val id = entry.arguments?.getString("id")
    DetailsScreen(id = id)
}
```

### When not to use / Common Mistake

Putting business decisions directly into route strings makes navigation hard to test and evolve.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a destination receive as navigation arguments, and what should it load itself?**

<details>
<summary>Reveal sample answer</summary>

Pass stable identifiers or small arguments through navigation and let the destination obtain its current data from its state/data layer. Avoid passing large mutable objects or relying on transient in-memory navigation state.

</details>

2. **What happens to navigation state when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

The back stack and in-memory objects should not be treated as the durable source of screen data. Persist or reconstruct the required state from stable inputs such as route arguments, saved state, or a repository.

</details>

3. **How would you test a deep link?**

<details>
<summary>Reveal sample answer</summary>

Test that the URI resolves to the intended destination, arguments are parsed correctly, and the destination can render when entered directly without first visiting the normal navigation path.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Navigation should coordinate destinations, back-stack behavior, and route/deep-link interpretation. Screen business logic should remain outside navigation infrastructure.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Why should you pass IDs instead of large objects between destinations?

**Difficulty:** Intermediate

### Answer

Navigation state has limited saved-state capacity, and large objects increase coupling. Passing a stable identifier lets the destination load or observe the authoritative data.

### When to use

Pass only the minimum data needed to reconstruct the destination.

### When not to use / Common Mistake

Passing entire domain/database objects through navigation couples destinations and risks state-size problems.

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

**Key idea:** Navigation state has limited saved-state capacity, and large objects increase coupling. Passing a stable identifier lets the destination load or observe the authoritative data.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is a deep link?

**Difficulty:** Basic

### Answer

A deep link is a URI or intent that takes the user directly to content or a destination inside an app. Android routes matching intents to interested apps.

### When to use

Use App Links for trusted web-domain links to your own site.

### Example

```kotlin
composable("details/{id}") { entry ->
    val id = entry.arguments?.getString("id")
    DetailsScreen(id = id)
}
```

### When not to use / Common Mistake

Treat incoming deep-link data as untrusted input and validate it.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a destination receive as navigation arguments, and what should it load itself?**

<details>
<summary>Reveal sample answer</summary>

Pass stable identifiers or small arguments through navigation and let the destination obtain its current data from its state/data layer. Avoid passing large mutable objects or relying on transient in-memory navigation state.

</details>

2. **What happens to navigation state when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

The back stack and in-memory objects should not be treated as the durable source of screen data. Persist or reconstruct the required state from stable inputs such as route arguments, saved state, or a repository.

</details>

3. **How would you test a deep link?**

<details>
<summary>Reveal sample answer</summary>

Test that the URI resolves to the intended destination, arguments are parsed correctly, and the destination can render when entered directly without first visiting the normal navigation path.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** A deep link is a URI or intent that takes the user directly to content or a destination inside an app. Android routes matching intents to interested apps.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How should deep links work in a multi-module app?

**Difficulty:** Advanced

### Answer

Each feature can own its navigation contract and matching logic while the app-level navigation composition combines them. This prevents the root module from knowing every feature implementation detail.

### When to use

An API/implementation split or DI-contributed navigation entries can scale the graph.

### Example

```kotlin
composable("details/{id}") { entry ->
    val id = entry.arguments?.getString("id")
    DetailsScreen(id = id)
}
```

### When not to use / Common Mistake

A central router containing every feature-specific detail becomes a bottleneck.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a destination receive as navigation arguments, and what should it load itself?**

<details>
<summary>Reveal sample answer</summary>

Pass stable identifiers or small arguments through navigation and let the destination obtain its current data from its state/data layer. Avoid passing large mutable objects or relying on transient in-memory navigation state.

</details>

2. **What happens to navigation state when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

The back stack and in-memory objects should not be treated as the durable source of screen data. Persist or reconstruct the required state from stable inputs such as route arguments, saved state, or a repository.

</details>

3. **How would you test a deep link?**

<details>
<summary>Reveal sample answer</summary>

Test that the URI resolves to the intended destination, arguments are parsed correctly, and the destination can render when entered directly without first visiting the normal navigation path.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Each feature can own its navigation contract and matching logic while the app-level navigation composition combines them. This prevents the root module from knowing every feature implementation detail.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How do you handle a deep link when the user is logged out?

**Difficulty:** Advanced

### Answer

Parse and validate the link, preserve the intended destination, route through authentication, and resume only after authorization succeeds. The exact behavior depends on product security requirements.

### When to use

Keep pending navigation intent separate from authentication state.

### Example

```kotlin
composable("details/{id}") { entry ->
    val id = entry.arguments?.getString("id")
    DetailsScreen(id = id)
}
```

### When not to use / Common Mistake

Navigating directly to a protected screen before authentication can leak information or create broken back stacks.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a destination receive as navigation arguments, and what should it load itself?**

<details>
<summary>Reveal sample answer</summary>

Pass stable identifiers or small arguments through navigation and let the destination obtain its current data from its state/data layer. Avoid passing large mutable objects or relying on transient in-memory navigation state.

</details>

2. **What happens to navigation state when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

The back stack and in-memory objects should not be treated as the durable source of screen data. Persist or reconstruct the required state from stable inputs such as route arguments, saved state, or a repository.

</details>

3. **How would you test a deep link?**

<details>
<summary>Reveal sample answer</summary>

Test that the URI resolves to the intended destination, arguments are parsed correctly, and the destination can render when entered directly without first visiting the normal navigation path.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Parse and validate the link, preserve the intended destination, route through authentication, and resume only after authorization succeeds. The exact behavior depends on product security requirements.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How do you test deep links?

**Difficulty:** Intermediate

### Answer

Test URI parsing, matching, argument validation, back-stack construction, authentication gates, and cold/warm process behavior.

### When to use

Include malformed, missing-argument, repeated-tap, and already-running-app cases.

### Example

```kotlin
composable("details/{id}") { entry ->
    val id = entry.arguments?.getString("id")
    DetailsScreen(id = id)
}
```

### When not to use / Common Mistake

Testing only the happy-path URI misses most navigation failures.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a destination receive as navigation arguments, and what should it load itself?**

<details>
<summary>Reveal sample answer</summary>

Pass stable identifiers or small arguments through navigation and let the destination obtain its current data from its state/data layer. Avoid passing large mutable objects or relying on transient in-memory navigation state.

</details>

2. **What happens to navigation state when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

The back stack and in-memory objects should not be treated as the durable source of screen data. Persist or reconstruct the required state from stable inputs such as route arguments, saved state, or a repository.

</details>

3. **How would you test a deep link?**

<details>
<summary>Reveal sample answer</summary>

Test that the URI resolves to the intended destination, arguments are parsed correctly, and the destination can render when entered directly without first visiting the normal navigation path.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Test URI parsing, matching, argument validation, back-stack construction, authentication gates, and cold/warm process behavior.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you support multiple back stacks?

**Difficulty:** Advanced

### Answer

Use the navigation APIs and state model appropriate to your navigation stack so each top-level destination can preserve its own history when switching tabs.

### When to use

Test tab switching, process recreation, and back behavior together.

### When not to use / Common Mistake

Manually maintaining several stacks without clear ownership often creates restoration bugs.

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

**Key idea:** Use the navigation APIs and state model appropriate to your navigation stack so each top-level destination can preserve its own history when switching tabs.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you design navigation for 50+ feature modules?

**Difficulty:** Advanced

### Answer

Make features own their navigation contracts, keep implementation private, and compose entries/routes at the app boundary or through DI. Establish rules for cross-feature navigation so dependencies stay directional.

### When to use

Document who can navigate to whom and how arguments are represented.

### Example

```kotlin
composable("details/{id}") { entry ->
    val id = entry.arguments?.getString("id")
    DetailsScreen(id = id)
}
```

### When not to use / Common Mistake

A single mega-navigation module that depends on every feature can erase modularization benefits.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should a destination receive as navigation arguments, and what should it load itself?**

<details>
<summary>Reveal sample answer</summary>

Pass stable identifiers or small arguments through navigation and let the destination obtain its current data from its state/data layer. Avoid passing large mutable objects or relying on transient in-memory navigation state.

</details>

2. **What happens to navigation state when the process is killed?**

<details>
<summary>Reveal sample answer</summary>

The back stack and in-memory objects should not be treated as the durable source of screen data. Persist or reconstruct the required state from stable inputs such as route arguments, saved state, or a repository.

</details>

3. **How would you test a deep link?**

<details>
<summary>Reveal sample answer</summary>

Test that the URI resolves to the intended destination, arguments are parsed correctly, and the destination can render when entered directly without first visiting the normal navigation path.

</details>
### References

- [Android app architecture](https://developer.android.com/topic/architecture)
- [Android UI layer and UDF](https://developer.android.com/topic/architecture/ui-layer)



### Quick Revision

**Key idea:** Make features own their navigation contracts, keep implementation private, and compose entries/routes at the app boundary or through DI. Establish rules for cross-feature navigation so dependencies stay directional.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
