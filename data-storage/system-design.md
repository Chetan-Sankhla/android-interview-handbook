# Senior/Lead Storage System Design

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. Design an offline-first e-commerce product catalog.

**Difficulty:** 🟣 System Design

### Answer

Use the server as the remote authority and Room as the local source of truth for the UI. Store normalized product/category/variant data in Room, use Paging for large lists, and store product images separately in a file/cache layer.

A repository coordinates initial load, delta synchronization, freshness, and user-triggered refresh. DataStore can hold small user preferences such as sort/filter settings.

The design should define stable server IDs, upsert semantics, deletion handling, sync checkpoints, retry behavior, and what happens when the catalog is stale.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you sync deletions?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

2. **How do you support search offline?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

3. **How do you handle 100k products?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **Where do images go?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Designing only the happy-path API call and database table.



### Quick Revision

**Key idea:** Use the server as the remote authority and Room as the local source of truth for the UI. Store normalized product/category/variant data in Room, use Paging for large lists, and store product images separately in a file/cache layer.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. Design a 4 GB offline download manager.

**Difficulty:** 🟣 System Design

### Answer

Persist download metadata in Room: download ID, URL, destination URI, byte ranges/progress, status, checksum, and retry information. Use durable background execution such as WorkManager where appropriate.

Write to a temporary/in-progress location and publish only after completion and validation. For resumable downloads, the server and HTTP layer must support range requests or another resumable protocol.

The storage destination depends on ownership: app-specific storage for private downloads, MediaStore for user-visible media, or SAF when the user chooses the destination.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you resume after process death?**

<details>
<summary>Reveal sample answer</summary>

ViewModel state survives configuration changes but not process death. Durable state belongs in persistent storage; small restorable UI state can use saved-state mechanisms where appropriate.

</details>

2. **How do you handle checksum failure?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What happens if storage is full?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **How do you cancel a download?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating the download as one coroutine with no durable state.



### Quick Revision

**Key idea:** Persist download metadata in Room: download ID, URL, destination URI, byte ranges/progress, status, checksum, and retry information. Use durable background execution such as WorkManager where appropriate.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. Design an Android news app with articles and images.

**Difficulty:** 🟣 System Design

### Answer

Keep article metadata/content in Room and image bytes in a disk cache or app-specific storage. The UI observes Room, while synchronization updates Room in batches.

Use stable article IDs, server timestamps/versions, pagination, and retention policies. Keep user preferences in DataStore. Cache eviction should remove binary data without corrupting article metadata.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you support offline reading?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

2. **How do you expire old articles?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you avoid N+1 image lookups?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Putting article HTML and all images into a single giant database blob.



### Quick Revision

**Key idea:** Keep article metadata/content in Room and image bytes in a disk cache or app-specific storage. The UI observes Room, while synchronization updates Room in batches.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. Design secure local storage for a banking app.

**Difficulty:** 🟣 System Design

### Answer

Separate concerns: Room for structured local state that genuinely needs persistence, DataStore for non-sensitive preferences, and Keystore-backed encryption for sensitive local secrets where required.

Define backup rules carefully. Credentials should not be casually restored to a different device. Minimize persisted sensitive data, protect sharing/export paths, and clear or invalidate sensitive state on logout/session expiry.

The threat model determines whether cached account information is encrypted, what is allowed offline, and how long data remains available.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What can work offline?** → [Open the related question](../android-architecture/offline-first-architecture.md#how-should-offline-writes-work)

2. **Should tokens be backed up?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

3. **How do you handle device compromise?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **How do you audit storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Encrypting everything without defining which threats the encryption is meant to address.



### Quick Revision

**Key idea:** Separate concerns: Room for structured local state that genuinely needs persistence, DataStore for non-sensitive preferences, and Keystore-backed encryption for sensitive local secrets where required. Define backup rules carefully.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. Design a local cache shared by multiple features.

**Difficulty:** 🟣 System Design

### Answer

Create a centralized cache component with a stable key strategy, metadata, size/TTL policies, and ownership boundaries. Use Room for metadata when queries/eviction need persistence and files for large payloads.

Define concurrency behavior so two callers requesting the same missing object can share work rather than downloading it twice. Ensure cleanup is safe after process death and cache eviction.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you avoid duplicate downloads?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you evict?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you invalidate by user/account?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **What belongs in Room?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Creating one singleton Map<String, ByteArray> and calling it a cache.



### Quick Revision

**Key idea:** Create a centralized cache component with a stable key strategy, metadata, size/TTL policies, and ownership boundaries. Use Room for metadata when queries/eviction need persistence and files for large payloads.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. Design migration from SharedPreferences + JSON files to DataStore + Room.

**Difficulty:** 🟣 System Design

### Answer

First classify the existing data. Small preferences move to DataStore. Structured records that need queries or partial updates move to Room. Large opaque files move to appropriate file storage.

Run migrations from the existing formats, validate representative legacy data, and avoid maintaining two writable sources of truth after migration. Make the migration restart-safe and observable.

Roll out gradually and monitor crashes, migration failures, data counts, and unexpected resets.

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

1. **How do you handle malformed JSON?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you roll back?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you verify data parity?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **What if migration takes too long?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

### Common Mistake

Doing a one-time copy on first launch and deleting the old data immediately.



### Quick Revision

**Key idea:** First classify the existing data. Small preferences move to DataStore.

### Interview Insight

A complete answer covers the old and new representations, existing-user compatibility, migration testing, and rollout/data-loss safety.
## Q7. Design storage for a multi-account social app.

**Difficulty:** 🟣 System Design

### Answer

Partition account-specific state by stable account ID. Room rows should carry account identity where data is account-scoped, and caches should use account-aware keys.

Global preferences can remain separate in DataStore. On logout, revoke/delete credentials and clear or invalidate account-specific data according to product requirements.

If users can switch accounts offline, the local database must make account boundaries explicit so one account can never accidentally render another account's cached state.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you prevent data leakage between accounts?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What happens when an account is deleted remotely?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you share global settings?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Fetching all cached records and filtering by account only in the ViewModel.



### Quick Revision

**Key idea:** Partition account-specific state by stable account ID. Room rows should carry account identity where data is account-scoped, and caches should use account-aware keys.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you review an Android app's storage architecture as a Lead?

**Difficulty:** 🟣 System Design

### Answer

Start with an inventory of every persisted artifact: preferences, database tables, files, caches, credentials, media, and exported documents.

For each one, ask:
- Who owns it?
- How long must it live?
- Can it be recreated?
- Who can access it?
- Does it need queries/transactions?
- Should it survive uninstall?
- Should it be backed up?
- What happens when storage is full or corrupted?
- How is it migrated?

Then review performance, security, observability, test coverage, and platform compatibility.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/topic/architecture
- https://developer.android.com/topic/architecture/data-layer
- https://developer.android.com/training/data-storage
- https://developer.android.com/topic/libraries/architecture/workmanager

### Quick Revision

**Key idea:** Start with an inventory of every persisted artifact: preferences, database tables, files, caches, credentials, media, and exported documents. For each one, ask: - Who owns it?

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
