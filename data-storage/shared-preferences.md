# SharedPreferences

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is SharedPreferences?

**Difficulty:** 🟢 Basic

### Answer

`SharedPreferences` is a legacy Android key-value storage API commonly used for small primitive preference values.

It can store values such as booleans, strings, integers, longs, and sets of strings. It is simple, but it is not a relational database and is not designed for large or complex state.

For new preference-storage work, Android recommends considering DataStore instead.

### Example

```kotlin
// Legacy
val prefs = context.getSharedPreferences("settings", Context.MODE_PRIVATE)
prefs.edit().putBoolean("dark_mode", true).apply()

// DataStore
context.dataStore.edit { it[DARK_MODE] = true }
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why is DataStore preferred for new code?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What are commit() and apply()?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Is SharedPreferences encrypted?**

<details>
<summary>Reveal sample answer</summary>

DataStore provides coroutine/Flow-based APIs and transactional updates, while SharedPreferences exposes synchronous reads and an older mutation model. For new preference-style storage, DataStore is generally the modern choice.

</details>

4. **Can it replace Room?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Describing SharedPreferences as a database or using it for large JSON documents.



### Quick Revision

**Key idea:** `SharedPreferences` is a legacy Android key-value storage API commonly used for small primitive preference values. It can store values such as booleans, strings, integers, longs, and sets of strings.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What is the difference between commit() and apply()?

**Difficulty:** 🟡 Intermediate

### Answer

`commit()` writes the change synchronously and returns whether the operation succeeded. `apply()` updates the in-memory representation immediately and schedules persistence asynchronously.

The important production point is that `apply()` does not mean SharedPreferences has become completely free of blocking I/O. Android's documentation notes that pending `fsync()` work can still contribute to UI-thread stalls.

For new development, DataStore avoids this API model by providing coroutine/Flow-based asynchronous storage.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Can apply() still affect the UI thread?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **When would commit() be dangerous?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How does DataStore improve this design?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Claiming that apply() guarantees there can never be disk-related UI stalls.



### Quick Revision

**Key idea:** `commit()` writes the change synchronously and returns whether the operation succeeded. `apply()` updates the in-memory representation immediately and schedules persistence asynchronously.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. Is SharedPreferences safe for sensitive data?

**Difficulty:** 🟡 Intermediate

### Answer

SharedPreferences is not an encryption mechanism and does not encrypt values by default. Storing a secret there should therefore be evaluated against the application's threat model.

For credentials and cryptographic material, prefer Android Keystore-backed approaches and minimize how long sensitive values are persisted. Encryption should protect the data, while app-private storage limits ordinary cross-app access.

### Example

```kotlin
// Legacy
val prefs = context.getSharedPreferences("settings", Context.MODE_PRIVATE)
prefs.edit().putBoolean("dark_mode", true).apply()

// DataStore
context.dataStore.edit { it[DARK_MODE] = true }
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What would you use for a refresh token?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What is Android Keystore?** → [Open the related question](storage-security.md#what-is-android-keystore)

3. **Does internal storage mean encrypted storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Calling SharedPreferences 'secure storage' just because the XML file is in the app's private directory.



### Quick Revision

**Key idea:** SharedPreferences is not an encryption mechanism and does not encrypt values by default. Storing a secret there should therefore be evaluated against the application's threat model.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. Can SharedPreferences be used for large JSON?

**Difficulty:** 🟡 Intermediate

### Answer

It is technically possible to store JSON as a string, but that does not make it a good design. Large JSON values require serialization/deserialization, lack structured querying, and create large read/write operations.

If the data is structured and independently queryable, Room is a better fit. If it is small application state, DataStore is usually more appropriate.

### Example

```kotlin
// Legacy
val prefs = context.getSharedPreferences("settings", Context.MODE_PRIVATE)
prefs.edit().putBoolean("dark_mode", true).apply()

// DataStore
context.dataStore.edit { it[DARK_MODE] = true }
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When is JSON storage acceptable?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What happens if only one field changes?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you migrate such data?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using SharedPreferences as an improvised document database.



### Quick Revision

**Key idea:** It is technically possible to store JSON as a string, but that does not make it a good design. Large JSON values require serialization/deserialization, lack structured querying, and create large read/write operations.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How would you migrate SharedPreferences to DataStore?

**Difficulty:** 🔴 Advanced

### Answer

Use DataStore's migration support rather than manually reading and deleting the old file. `SharedPreferencesMigration` can map existing values into the new DataStore representation.

A production migration should be tested against realistic existing values, missing keys, malformed/legacy values, and rollback scenarios. Keep the migration idempotent and remove the old access path only after the new source is verified.

### Example

```kotlin
// Legacy
val prefs = context.getSharedPreferences("settings", Context.MODE_PRIVATE)
prefs.edit().putBoolean("dark_mode", true).apply()

// DataStore
context.dataStore.edit { it[DARK_MODE] = true }
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How does SharedPreferencesMigration work?**

<details>
<summary>Reveal sample answer</summary>

Place DataStore behind the existing repository or storage interface, migrate the old keys once, verify the result, and make the migration idempotent. Test both first launch and subsequent launches.

</details>

2. **What if a key is missing?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you test migration?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

4. **What happens if migration throws?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

### Common Mistake

Running a one-time migration in an Activity and then continuing to read both stores.



### Quick Revision

**Key idea:** Use DataStore's migration support rather than manually reading and deleting the old file. `SharedPreferencesMigration` can map existing values into the new DataStore representation.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. What are common concurrency problems with SharedPreferences?

**Difficulty:** 🔴 Advanced

### Answer

The API synchronizes its own operations, but application-level read-modify-write sequences can still be logically racy.

For example, two callers that both read `counter = 10`, increment locally, and then write `11` can lose an update. DataStore's transactional `updateData` model is designed for this type of state transformation.

### Example

```kotlin
// Legacy
val prefs = context.getSharedPreferences("settings", Context.MODE_PRIVATE)
prefs.edit().putBoolean("dark_mode", true).apply()

// DataStore
context.dataStore.edit { it[DARK_MODE] = true }
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/topic/libraries/architecture/datastore
- https://developer.android.com/training/data-storage

### Quick Revision

**Key idea:** The API synchronizes its own operations, but application-level read-modify-write sequences can still be logically racy. For example, two callers that both read `counter = 10`, increment locally, and then write `11` can lose an update.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
