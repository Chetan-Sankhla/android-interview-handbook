# Files, Downloads & Caching

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. How would you store downloaded images for an offline-first app?

**Difficulty:** 🟡 Intermediate

### Answer

Store image bytes in app-specific file/cache storage and store queryable metadata in Room. The metadata can include URL, local URI/path, size, ETag, last-accessed time, and freshness information.

This keeps binary data out of relational queries while still allowing the application to efficiently determine whether an image exists and whether it is stale.

### Common Follow-ups

- Would you use cacheDir?
- How would you evict old images?
- What happens after cache deletion?

### Senior/Lead Perspective

Whether the bytes belong in cache or persistent app-specific storage depends on whether the app can safely re-download them. The repository should not assume cache contents are permanent.

### Common Mistake

Putting thousands of image blobs directly into Room.

## Q2. How would you design an LRU file cache?

**Difficulty:** 🔴 Advanced

### Answer

Maintain metadata for each cached object, including size and last-access time. When the cache exceeds its configured limit, evict the least-recently-used or otherwise least valuable entries until it is below a target threshold.

The eviction operation should keep file and metadata state consistent. Temporary files and crash recovery also need to be handled.

### Common Follow-ups

- Why have a target below the maximum?
- How do you handle missing files?
- Should eviction run on the main thread?

### Senior/Lead Perspective

A production cache should tolerate metadata/file divergence. If the metadata says a file exists but the file is gone, the system should rebuild the entry rather than crash.

### Common Mistake

Implementing an in-memory LRU and assuming it is a durable disk-cache policy.

## Q3. How would you download a large file safely?

**Difficulty:** 🔴 Advanced

### Answer

Write to a temporary file rather than the final filename. Persist enough state to resume or retry if required, validate the completed file, and atomically publish it only after success.

For user-visible files, choose the final storage API based on ownership: app-specific storage, MediaStore, or SAF.

### Common Follow-ups

- How do you resume downloads?
- How do you detect corruption?
- How do you recover after process death?

### Senior/Lead Perspective

The important property is that consumers never see a partially written 'complete' file. Temporary state should be distinguishable from published state.

### Common Mistake

Writing directly to the final path and marking it complete before the stream closes.

## Q4. How do you make file writes crash-safe?

**Difficulty:** 🔴 Advanced

### Answer

Use a temporary file for the write, flush/close it successfully, validate if necessary, and only then publish the final reference. Keep metadata state transitions explicit so a crash cannot make a partial file appear valid.

For critical data, add checksums/versioning and a recovery path rather than relying on filename existence alone.

### Common Follow-ups

- What if the process is killed during rename?
- What should metadata contain?
- How do you detect stale temp files?

### Senior/Lead Perspective

Think in terms of a small state machine: `pending → complete` or `pending → failed/retry`. This is more robust than using file existence as the only signal.

### Common Mistake

Treating 'file exists' as equivalent to 'file is valid and complete'.

## Q5. When should a cache be in memory vs on disk?

**Difficulty:** 🟡 Intermediate

### Answer

Memory caches are fast but limited by process memory and are lost on process death. Disk caches survive process death and can hold much more data, but involve I/O.

Many production systems use both: a small memory cache for hot objects and a disk cache for larger/reusable content.

### Common Follow-ups

- How do you avoid OOM?
- How do you invalidate both layers?
- What is the source of truth?

### Senior/Lead Perspective

Define cache layers and invalidation explicitly. A memory cache should be an optimization, not a second authoritative data model.

### Common Mistake

Treating an in-memory cache as durable application state.

## Q6. How should file-cache metadata be modeled in Room?

**Difficulty:** 🟡 Intermediate

### Answer

A cache-entry table can contain a stable cache key, local URI/path, byte size, MIME type, creation time, last access time, expiration/freshness information, and server validators such as ETag when applicable.

The table should support the queries needed for eviction and lookup. Do not store data you cannot use to make a cache decision.

### Common Follow-ups

- Which fields should be indexed?
- How do you clean orphan files?
- How do you handle duplicate URLs?

### Senior/Lead Perspective

A lead should define the cache key carefully. URL alone may be insufficient if headers, user identity, locale, or request parameters change the content.

### Common Mistake

Using a URL as the only key for content that varies by user or request context.

## Q7. How would you recover from an orphaned cache file?

**Difficulty:** 🟡 Intermediate

### Answer

Periodically scan metadata and/or storage for inconsistencies. Files without metadata can be deleted if they are known to be cache artifacts. Metadata entries whose files are missing should be invalidated and rebuilt on demand.

Avoid doing a full expensive filesystem scan on every app launch; schedule maintenance appropriately.

### Common Follow-ups

- How do you avoid deleting user files?
- How would you schedule cleanup?
- What if the cache directory is huge?

### Senior/Lead Perspective

Keep cache files in a dedicated directory with a predictable naming scheme so maintenance never needs to guess whether a file is application-owned.

### Common Mistake

Scanning broad shared storage to find cache files.

## Q8. Would you store a PDF in Room as a BLOB?

**Difficulty:** 🟡 Intermediate

### Answer

Usually not unless there is a specific requirement for transactional database storage of the binary content. Large BLOBs can increase database size, backup cost, query overhead, and migration complexity.

A common design is Room for metadata and app-specific/shared storage for the PDF bytes, with a stable URI/reference connecting the two.

### Common Follow-ups

- When might a BLOB be justified?
- How do you back up the file?
- How do you keep metadata and file state consistent?

### Senior/Lead Perspective

The decision should consider transactional requirements, file size, access patterns, backup, and whether the database is the authoritative source of the binary content.

### Common Mistake

Assuming a database is always safer because it is transactional.

### References

- https://developer.android.com/training/data-storage/app-specific
- https://developer.android.com/training/data-storage