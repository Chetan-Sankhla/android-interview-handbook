# SharedPreferences

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is SharedPreferences?

**Difficulty:** 🟢 Basic

### Answer

`SharedPreferences` is a legacy Android key-value storage API commonly used for small primitive preference values.

It can store values such as booleans, strings, integers, longs, and sets of strings. It is simple, but it is not a relational database and is not designed for large or complex state.

For new preference-storage work, Android recommends considering DataStore instead.

### Common Follow-ups

- Why is DataStore preferred for new code?
- What are commit() and apply()?
- Is SharedPreferences encrypted?
- Can it replace Room?

### Senior/Lead Perspective

In a code review, distinguish legacy compatibility from new design. An existing SharedPreferences implementation may not need an immediate rewrite, but new code should have a deliberate reason if it continues to use it.

### Common Mistake

Describing SharedPreferences as a database or using it for large JSON documents.

## Q2. What is the difference between commit() and apply()?

**Difficulty:** 🟡 Intermediate

### Answer

`commit()` writes the change synchronously and returns whether the operation succeeded. `apply()` updates the in-memory representation immediately and schedules persistence asynchronously.

The important production point is that `apply()` does not mean SharedPreferences has become completely free of blocking I/O. Android's documentation notes that pending `fsync()` work can still contribute to UI-thread stalls.

For new development, DataStore avoids this API model by providing coroutine/Flow-based asynchronous storage.

### Common Follow-ups

- Can apply() still affect the UI thread?
- When would commit() be dangerous?
- How does DataStore improve this design?

### Senior/Lead Perspective

The answer should focus on observable behavior and failure semantics rather than saying simply 'commit is sync, apply is async'. A senior developer should know that asynchronous persistence does not eliminate all I/O-related performance concerns.

### Common Mistake

Claiming that apply() guarantees there can never be disk-related UI stalls.

## Q3. Is SharedPreferences safe for sensitive data?

**Difficulty:** 🟡 Intermediate

### Answer

SharedPreferences is not an encryption mechanism and does not encrypt values by default. Storing a secret there should therefore be evaluated against the application's threat model.

For credentials and cryptographic material, prefer Android Keystore-backed approaches and minimize how long sensitive values are persisted. Encryption should protect the data, while app-private storage limits ordinary cross-app access.

### Common Follow-ups

- What would you use for a refresh token?
- What is Android Keystore?
- Does internal storage mean encrypted storage?

### Senior/Lead Perspective

A senior engineer should distinguish **sandboxing, encryption, and key protection**. They solve different problems and should not be treated as synonyms.

### Common Mistake

Calling SharedPreferences 'secure storage' just because the XML file is in the app's private directory.

## Q4. Can SharedPreferences be used for large JSON?

**Difficulty:** 🟡 Intermediate

### Answer

It is technically possible to store JSON as a string, but that does not make it a good design. Large JSON values require serialization/deserialization, lack structured querying, and create large read/write operations.

If the data is structured and independently queryable, Room is a better fit. If it is small application state, DataStore is usually more appropriate.

### Common Follow-ups

- When is JSON storage acceptable?
- What happens if only one field changes?
- How would you migrate such data?

### Senior/Lead Perspective

The senior-level issue is not whether the API permits the operation; it is whether the data model matches the storage technology.

### Common Mistake

Using SharedPreferences as an improvised document database.

## Q5. How would you migrate SharedPreferences to DataStore?

**Difficulty:** 🔴 Advanced

### Answer

Use DataStore's migration support rather than manually reading and deleting the old file. `SharedPreferencesMigration` can map existing values into the new DataStore representation.

A production migration should be tested against realistic existing values, missing keys, malformed/legacy values, and rollback scenarios. Keep the migration idempotent and remove the old access path only after the new source is verified.

### Common Follow-ups

- How does SharedPreferencesMigration work?
- What if a key is missing?
- How do you test migration?
- What happens if migration throws?

### Senior/Lead Perspective

The key concern is not merely copying values; it is preserving user state across app versions without creating two competing sources of truth.

### Common Mistake

Running a one-time migration in an Activity and then continuing to read both stores.

## Q6. What are common concurrency problems with SharedPreferences?

**Difficulty:** 🔴 Advanced

### Answer

The API synchronizes its own operations, but application-level read-modify-write sequences can still be logically racy.

For example, two callers that both read `counter = 10`, increment locally, and then write `11` can lose an update. DataStore's transactional `updateData` model is designed for this type of state transformation.

### Common Follow-ups

- Does synchronization solve all race conditions?
- How does DataStore updateData help?
- How would you test concurrent updates?

### Senior/Lead Perspective

Look for compound operations rather than only individual API calls. Senior developers reason about the atomicity of the **whole business operation**.

### Common Mistake

Assuming synchronized methods automatically make every multi-step operation atomic.

### References

- https://developer.android.com/topic/libraries/architecture/datastore
- https://developer.android.com/training/data-storage