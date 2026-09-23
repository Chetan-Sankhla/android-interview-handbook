# Senior/Lead Storage System Design

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. Design an offline-first e-commerce product catalog.

**Difficulty:** 🟣 System Design

### Answer

Use the server as the remote authority and Room as the local source of truth for the UI. Store normalized product/category/variant data in Room, use Paging for large lists, and store product images separately in a file/cache layer.

A repository coordinates initial load, delta synchronization, freshness, and user-triggered refresh. DataStore can hold small user preferences such as sort/filter settings.

The design should define stable server IDs, upsert semantics, deletion handling, sync checkpoints, retry behavior, and what happens when the catalog is stale.

### Common Follow-ups

- How do you sync deletions?
- How do you support search offline?
- How do you handle 100k products?
- Where do images go?

### Senior/Lead Perspective

The lead-level discussion is about data lifecycle and operational behavior: migration, observability, storage limits, schema evolution, sync failures, and rollout strategy—not just choosing Room.

### Common Mistake

Designing only the happy-path API call and database table.

## Q2. Design a 4 GB offline download manager.

**Difficulty:** 🟣 System Design

### Answer

Persist download metadata in Room: download ID, URL, destination URI, byte ranges/progress, status, checksum, and retry information. Use durable background execution such as WorkManager where appropriate.

Write to a temporary/in-progress location and publish only after completion and validation. For resumable downloads, the server and HTTP layer must support range requests or another resumable protocol.

The storage destination depends on ownership: app-specific storage for private downloads, MediaStore for user-visible media, or SAF when the user chooses the destination.

### Common Follow-ups

- How do you resume after process death?
- How do you handle checksum failure?
- What happens if storage is full?
- How do you cancel a download?

### Senior/Lead Perspective

Define the state machine explicitly: queued → downloading → paused/failed → completed. Each transition should be recoverable after process death.

### Common Mistake

Treating the download as one coroutine with no durable state.

## Q3. Design an Android news app with articles and images.

**Difficulty:** 🟣 System Design

### Answer

Keep article metadata/content in Room and image bytes in a disk cache or app-specific storage. The UI observes Room, while synchronization updates Room in batches.

Use stable article IDs, server timestamps/versions, pagination, and retention policies. Keep user preferences in DataStore. Cache eviction should remove binary data without corrupting article metadata.

### Common Follow-ups

- How do you support offline reading?
- How do you expire old articles?
- How do you avoid N+1 image lookups?

### Senior/Lead Perspective

The senior design should distinguish durable editorial content from disposable image cache and define what happens when storage pressure removes images.

### Common Mistake

Putting article HTML and all images into a single giant database blob.

## Q4. Design secure local storage for a banking app.

**Difficulty:** 🟣 System Design

### Answer

Separate concerns: Room for structured local state that genuinely needs persistence, DataStore for non-sensitive preferences, and Keystore-backed encryption for sensitive local secrets where required.

Define backup rules carefully. Credentials should not be casually restored to a different device. Minimize persisted sensitive data, protect sharing/export paths, and clear or invalidate sensitive state on logout/session expiry.

The threat model determines whether cached account information is encrypted, what is allowed offline, and how long data remains available.

### Common Follow-ups

- What can work offline?
- Should tokens be backed up?
- How do you handle device compromise?
- How do you audit storage?

### Senior/Lead Perspective

The lead-level answer must begin with threat modeling and product requirements. Security architecture should not be chosen from a generic 'secure storage' checklist alone.

### Common Mistake

Encrypting everything without defining which threats the encryption is meant to address.

## Q5. Design a local cache shared by multiple features.

**Difficulty:** 🟣 System Design

### Answer

Create a centralized cache component with a stable key strategy, metadata, size/TTL policies, and ownership boundaries. Use Room for metadata when queries/eviction need persistence and files for large payloads.

Define concurrency behavior so two callers requesting the same missing object can share work rather than downloading it twice. Ensure cleanup is safe after process death and cache eviction.

### Common Follow-ups

- How do you avoid duplicate downloads?
- How do you evict?
- How do you invalidate by user/account?
- What belongs in Room?

### Senior/Lead Perspective

The key architectural concern is preventing every feature from inventing its own cache semantics. A shared cache should have explicit contracts for freshness, ownership, and failure.

### Common Mistake

Creating one singleton Map<String, ByteArray> and calling it a cache.

## Q6. Design migration from SharedPreferences + JSON files to DataStore + Room.

**Difficulty:** 🟣 System Design

### Answer

First classify the existing data. Small preferences move to DataStore. Structured records that need queries or partial updates move to Room. Large opaque files move to appropriate file storage.

Run migrations from the existing formats, validate representative legacy data, and avoid maintaining two writable sources of truth after migration. Make the migration restart-safe and observable.

Roll out gradually and monitor crashes, migration failures, data counts, and unexpected resets.

### Common Follow-ups

- How do you handle malformed JSON?
- How do you roll back?
- How do you verify data parity?
- What if migration takes too long?

### Senior/Lead Perspective

A lead should plan migration as a release project: compatibility matrix, telemetry, staged rollout, recovery strategy, and a defined point at which legacy storage can be removed.

### Common Mistake

Doing a one-time copy on first launch and deleting the old data immediately.

## Q7. Design storage for a multi-account social app.

**Difficulty:** 🟣 System Design

### Answer

Partition account-specific state by stable account ID. Room rows should carry account identity where data is account-scoped, and caches should use account-aware keys.

Global preferences can remain separate in DataStore. On logout, revoke/delete credentials and clear or invalidate account-specific data according to product requirements.

If users can switch accounts offline, the local database must make account boundaries explicit so one account can never accidentally render another account's cached state.

### Common Follow-ups

- How do you prevent data leakage between accounts?
- What happens when an account is deleted remotely?
- How do you share global settings?

### Senior/Lead Perspective

Account isolation should be enforced at the data-query boundary, not merely by filtering results in the UI.

### Common Mistake

Fetching all cached records and filtering by account only in the ViewModel.

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

- What are your red flags?
- How do you prioritize technical debt?
- What metrics would you monitor?

### Senior/Lead Perspective

A lead review should produce architectural decisions and migration priorities, not just a list of APIs. The output should make ownership, lifecycle, failure handling, and operational risk explicit.

### Common Mistake

Reviewing only whether the app uses modern APIs, without checking whether those APIs are being used correctly.

### References

- https://developer.android.com/topic/architecture
- https://developer.android.com/topic/architecture/data-layer
- https://developer.android.com/training/data-storage
- https://developer.android.com/topic/libraries/architecture/workmanager