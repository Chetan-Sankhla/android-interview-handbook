# Files, Downloads & Caching

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. How would you store downloaded images for an offline-first app?

**Difficulty:** 🟡 Intermediate

### Answer

Store image bytes in app-specific file/cache storage and store queryable metadata in Room. The metadata can include URL, local URI/path, size, ETag, last-accessed time, and freshness information.

This keeps binary data out of relational queries while still allowing the application to efficiently determine whether an image exists and whether it is stale.

### Example

```kotlin
fun observeProducts(): Flow<List<Product>> = dao.observeProducts()

suspend fun refresh() {
    dao.replaceAll(api.fetchProducts())
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Would you use cacheDir?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How would you evict old images?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What happens after cache deletion?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Putting thousands of image blobs directly into Room.



### Quick Revision

**Key idea:** Store image bytes in app-specific file/cache storage and store queryable metadata in Room. The metadata can include URL, local URI/path, size, ETag, last-accessed time, and freshness information.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. How would you design an LRU file cache?

**Difficulty:** 🔴 Advanced

### Answer

Maintain metadata for each cached object, including size and last-access time. When the cache exceeds its configured limit, evict the least-recently-used or otherwise least valuable entries until it is below a target threshold.

The eviction operation should keep file and metadata state consistent. Temporary files and crash recovery also need to be handled.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why have a target below the maximum?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you handle missing files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Should eviction run on the main thread?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Implementing an in-memory LRU and assuming it is a durable disk-cache policy.



### Quick Revision

**Key idea:** Maintain metadata for each cached object, including size and last-access time. When the cache exceeds its configured limit, evict the least-recently-used or otherwise least valuable entries until it is below a target threshold.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How would you download a large file safely?

**Difficulty:** 🔴 Advanced

### Answer

Write to a temporary file rather than the final filename. Persist enough state to resume or retry if required, validate the completed file, and atomically publish it only after success.

For user-visible files, choose the final storage API based on ownership: app-specific storage, MediaStore, or SAF.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you resume downloads?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you detect corruption?**

<details>
<summary>Reveal sample answer</summary>

Use a corruption handler only when you have a safe recovery value. If data can be reconstructed from another source, restore it deliberately; otherwise avoid silently discarding potentially recoverable user data.

</details>

3. **How do you recover after process death?**

<details>
<summary>Reveal sample answer</summary>

ViewModel state survives configuration changes but not process death. Durable state belongs in persistent storage; small restorable UI state can use saved-state mechanisms where appropriate.

</details>

### Common Mistake

Writing directly to the final path and marking it complete before the stream closes.



### Quick Revision

**Key idea:** Write to a temporary file rather than the final filename. Persist enough state to resume or retry if required, validate the completed file, and atomically publish it only after success.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How do you make file writes crash-safe?

**Difficulty:** 🔴 Advanced

### Answer

Use a temporary file for the write, flush/close it successfully, validate if necessary, and only then publish the final reference. Keep metadata state transitions explicit so a crash cannot make a partial file appear valid.

For critical data, add checksums/versioning and a recovery path rather than relying on filename existence alone.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What if the process is killed during rename?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What should metadata contain?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you detect stale temp files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating 'file exists' as equivalent to 'file is valid and complete'.



### Quick Revision

**Key idea:** Use a temporary file for the write, flush/close it successfully, validate if necessary, and only then publish the final reference. Keep metadata state transitions explicit so a crash cannot make a partial file appear valid.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. When should a cache be in memory vs on disk?

**Difficulty:** 🟡 Intermediate

### Answer

Memory caches are fast but limited by process memory and are lost on process death. Disk caches survive process death and can hold much more data, but involve I/O.

Many production systems use both: a small memory cache for hot objects and a disk cache for larger/reusable content.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you avoid OOM?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you invalidate both layers?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What is the source of truth?** → [Open the related question](../android-architecture/offline-first-architecture.md#what-is-the-local-source-of-truth)

### Common Mistake

Treating an in-memory cache as durable application state.



### Quick Revision

**Key idea:** Memory caches are fast but limited by process memory and are lost on process death. Disk caches survive process death and can hold much more data, but involve I/O.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How should file-cache metadata be modeled in Room?

**Difficulty:** 🟡 Intermediate

### Answer

A cache-entry table can contain a stable cache key, local URI/path, byte size, MIME type, creation time, last access time, expiration/freshness information, and server validators such as ETag when applicable.

The table should support the queries needed for eviction and lookup. Do not store data you cannot use to make a cache decision.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Which fields should be indexed?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you clean orphan files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you handle duplicate URLs?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using a URL as the only key for content that varies by user or request context.



### Quick Revision

**Key idea:** A cache-entry table can contain a stable cache key, local URI/path, byte size, MIME type, creation time, last access time, expiration/freshness information, and server validators such as ETag when applicable. The table should support the queries needed for eviction and lookup.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How would you recover from an orphaned cache file?

**Difficulty:** 🟡 Intermediate

### Answer

Periodically scan metadata and/or storage for inconsistencies. Files without metadata can be deleted if they are known to be cache artifacts. Metadata entries whose files are missing should be invalidated and rebuilt on demand.

Avoid doing a full expensive filesystem scan on every app launch; schedule maintenance appropriately.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you avoid deleting user files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How would you schedule cleanup?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What if the cache directory is huge?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Scanning broad shared storage to find cache files.



### Quick Revision

**Key idea:** Periodically scan metadata and/or storage for inconsistencies. Files without metadata can be deleted if they are known to be cache artifacts.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. Would you store a PDF in Room as a BLOB?

**Difficulty:** 🟡 Intermediate

### Answer

Usually not unless there is a specific requirement for transactional database storage of the binary content. Large BLOBs can increase database size, backup cost, query overhead, and migration complexity.

A common design is Room for metadata and app-specific/shared storage for the PDF bytes, with a stable URI/reference connecting the two.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/training/data-storage/app-specific
- https://developer.android.com/training/data-storage

### Quick Revision

**Key idea:** Usually not unless there is a specific requirement for transactional database storage of the binary content. Large BLOBs can increase database size, backup cost, query overhead, and migration complexity.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
