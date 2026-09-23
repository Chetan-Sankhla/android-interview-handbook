# Android Data Storage — Interview Questions & Answers

> **209 interview questions** covering Android storage fundamentals through Senior/Lead architecture and system design.

**Difficulty:** 🟢 Basic · 🟡 Intermediate · 🔴 Advanced · 🟣 System Design

**Last reviewed:** September 2026

## Official references
- https://developer.android.com/training/data-storage
- https://developer.android.com/training/data-storage/app-specific
- https://developer.android.com/topic/libraries/architecture/datastore
- https://developer.android.com/training/data-storage/room/migrating-db-versions
- https://developer.android.com/training/data-storage/shared/media
- https://developer.android.com/training/data-storage/shared/documents-files
- https://developer.android.com/training/data-storage/manage-all-files

## 1. Storage Fundamentals

### Q1. What are the different ways to store data in Android?

**Difficulty:** 🟢 Basic

**Answer:**

Android provides app-specific storage, shared storage, preferences, and databases. Common APIs include internal/external app-specific files, MediaStore, Storage Access Framework, DataStore, and Room.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q2. What is the difference between internal storage and external storage?

**Difficulty:** 🟢 Basic

**Answer:**

Internal storage is private to the app and normally inaccessible to other apps. External/shared storage is designed for larger or shareable content and is governed by modern scoped-access rules.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q3. What is app-specific storage?

**Difficulty:** 🟢 Basic

**Answer:**

Storage intended only for the app, such as `filesDir`, `cacheDir`, `getExternalFilesDir()`, and `externalCacheDir`. App-specific files are removed when the app is uninstalled.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q4. What is shared storage?

**Difficulty:** 🟢 Basic

**Answer:**

Storage for user/shareable content such as photos, videos, audio, downloads, and documents. MediaStore and SAF are the main modern access mechanisms.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q5. When would you use internal storage instead of external storage?

**Difficulty:** 🟢 Basic

**Answer:**

Use internal storage for private application data, especially sensitive or critical data that other apps should not access.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q6. What happens to app-specific files when the app is uninstalled?

**Difficulty:** 🟢 Basic

**Answer:**

They are normally removed with the app.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q7. Can another application access files stored in your app's internal storage?

**Difficulty:** 🟢 Basic

**Answer:**

Normally no; Android's app sandbox prevents ordinary apps from accessing another app's internal files.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q8. Why shouldn't you hard-code storage paths in Android?

**Difficulty:** 🟢 Basic

**Answer:**

Storage layouts vary and Android's storage model has changed over time. Use framework APIs such as `filesDir`, MediaStore, and SAF instead.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q9. What is the difference between filesDir and cacheDir?

**Difficulty:** 🟢 Basic

**Answer:**

`filesDir` is persistent app data. `cacheDir` is temporary data that the system may remove under storage pressure.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q10. What is the difference between getExternalFilesDir() and getExternalCacheDir()?

**Difficulty:** 🟢 Basic

**Answer:**

Both are app-specific external locations; the former is for persistent app files and the latter for disposable cache data.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q11. When should you use cache storage?

**Difficulty:** 🟢 Basic

**Answer:**

For data that can be recreated or downloaded again, such as thumbnails, temporary responses, or generated files.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q12. What happens to cache files when the device needs additional storage?

**Difficulty:** 🟢 Basic

**Answer:**

The system may remove cache files. The app must be able to recreate them.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q13. Can an app rely on external storage being permanently available?

**Difficulty:** 🟢 Basic

**Answer:**

No. External volumes can be unavailable or removable, so availability should be checked.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q14. Where would you store sensitive application data?

**Difficulty:** 🟢 Basic

**Answer:**

Prefer private internal storage. For cryptographic keys, use Android Keystore rather than ordinary files.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q15. What type of data should be stored in a database rather than a file?

**Difficulty:** 🟢 Basic

**Answer:**

Structured data that needs queries, relationships, indexes, transactions, sorting, filtering, or partial updates.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 2. SharedPreferences

### Q16. What is SharedPreferences?

**Difficulty:** 🟢 Basic

**Answer:**

A legacy key-value API for small primitive values and simple preferences.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q17. What type of data should be stored in SharedPreferences?

**Difficulty:** 🟢 Basic

**Answer:**

Historically, small settings and flags. For new preference storage, DataStore is generally preferred.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q18. What are the limitations of SharedPreferences?

**Difficulty:** 🟢 Basic

**Answer:**

It is not designed for large/complex data, lacks a schema, has synchronous APIs, and has weaker transactional/error-handling characteristics than DataStore.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q19. What is the difference between commit() and apply()?

**Difficulty:** 🟢 Basic

**Answer:**

`commit()` writes synchronously and returns success. `apply()` updates memory immediately and schedules persistence asynchronously.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q20. Is apply() synchronous or asynchronous?

**Difficulty:** 🟢 Basic

**Answer:**

Disk persistence is asynchronous, although SharedPreferences can still contribute to synchronous I/O and fsync-related main-thread stalls.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q21. What happens if two threads modify SharedPreferences simultaneously?

**Difficulty:** 🟢 Basic

**Answer:**

SharedPreferences synchronizes its internal operations, but multi-step read-modify-write business logic can still have logical race conditions.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q22. Can SharedPreferences be used to store large JSON objects?

**Difficulty:** 🟢 Basic

**Answer:**

It can technically store a JSON string, but this is a poor design for large/structured data; use Room or another suitable store.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q23. Would you store an authentication token in SharedPreferences?

**Difficulty:** 🟢 Basic

**Answer:**

Not as a default secure-storage strategy. Sensitive credentials should use private/protected storage and, where appropriate, Keystore-backed encryption.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q24. Is SharedPreferences encrypted by default?

**Difficulty:** 🟢 Basic

**Answer:**

No.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q25. What happens to SharedPreferences when the application is uninstalled?

**Difficulty:** 🟢 Basic

**Answer:**

Its private data is removed with the app.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q26. Can SharedPreferences be accessed from multiple processes?

**Difficulty:** 🟢 Basic

**Answer:**

It is not a good modern choice for multi-process synchronization. Use a mechanism designed for the required process model.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q27. Why is Android documentation recommending DataStore over SharedPreferences for new development?

**Difficulty:** 🟢 Basic

**Answer:**

DataStore provides asynchronous, Flow-based, transactional updates and better consistency/error handling.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 3. DataStore

### Q28. What is DataStore?

**Difficulty:** 🟢 Basic

**Answer:**

Jetpack DataStore stores key-value preferences or typed objects asynchronously using coroutines and Flow.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q29. What is the difference between Preferences DataStore and Proto DataStore?

**Difficulty:** 🟢 Basic

**Answer:**

Preferences DataStore uses untyped keys and values with no predefined schema. Proto/typed DataStore uses a schema and strongly typed objects.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q30. When would you choose Preferences DataStore?

**Difficulty:** 🟢 Basic

**Answer:**

For small preference-like values such as theme, language, flags, or sort settings.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q31. When would you choose Proto DataStore?

**Difficulty:** 🟢 Basic

**Answer:**

For small structured application state where a schema and type safety are useful.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q32. Why is DataStore preferable to SharedPreferences?

**Difficulty:** 🟢 Basic

**Answer:**

It is asynchronous, transactional, Flow-based, and designed to avoid several SharedPreferences consistency and synchronous-I/O problems.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q33. How does DataStore use Kotlin Flow?

**Difficulty:** 🟢 Basic

**Answer:**

Its `data` API exposes persisted state as a Flow, so consumers can react to changes.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q34. Why is DataStore considered asynchronous?

**Difficulty:** 🟢 Basic

**Answer:**

Its persistence APIs are coroutine-friendly and avoid requiring callers to perform blocking disk operations.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q35. What happens if multiple coroutines update DataStore simultaneously?

**Difficulty:** 🟢 Basic

**Answer:**

DataStore serializes updates through its update mechanism, avoiding the caller-managed read-modify-write race common in naive preference code.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q36. How does DataStore provide consistency?

**Difficulty:** 🟢 Basic

**Answer:**

Updates are transactional; the transformation operates against the current state and persistence/notification are coordinated by DataStore.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q37. How would you migrate an existing SharedPreferences implementation to DataStore?

**Difficulty:** 🟢 Basic

**Answer:**

Use `SharedPreferencesMigration`, map the existing keys, validate the migrated values, and then remove the old access path.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q38. What happens if DataStore contains corrupted data?

**Difficulty:** 🟢 Basic

**Answer:**

Handle the appropriate corruption exception and, where acceptable, use a corruption handler to replace/recover the stored state. Do not silently discard important data.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q39. How would you handle IOException while reading DataStore?

**Difficulty:** 🟢 Basic

**Answer:**

Catch expected `IOException` at the data/repository boundary and emit a safe default or domain error; rethrow unexpected exceptions.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q40. Can DataStore replace Room?

**Difficulty:** 🟢 Basic

**Answer:**

No. DataStore is for small datasets; Room is for large/complex relational data, queries, partial updates, and referential integrity.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q41. Can you use DataStore for thousands of records?

**Difficulty:** 🟢 Basic

**Answer:**

It is technically possible to serialize data, but it is usually the wrong model. Use Room when records need independent queries/updates.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q42. Why shouldn't DataStore be treated as a database?

**Difficulty:** 🟢 Basic

**Answer:**

It lacks relational querying, indexes, relationships, and record-level partial updates.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q43. How would you expose DataStore data from a Repository?

**Difficulty:** 🟢 Basic

**Answer:**

Expose domain-oriented Flow and suspend update operations from the repository; keep DataStore details inside the data layer.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q44. How would you test a DataStore-based repository?

**Difficulty:** 🟢 Basic

**Answer:**

Test defaults, reads, updates, concurrent updates, migrations, corruption handling, and expected I/O failures using isolated test storage.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q45. How would you design DataStore in a Clean Architecture project?

**Difficulty:** 🟢 Basic

**Answer:**

UI → ViewModel → use case → repository → DataStore. Keep persistence implementation details out of presentation/domain layers.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 4. Room / SQLite

### Q46. What is Room?

**Difficulty:** 🟢 Basic

**Answer:**

A Jetpack persistence library that provides an abstraction over SQLite with entities, DAOs, migrations, transactions, and compile-time query verification.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q47. Why would you choose Room instead of SQLite APIs directly?

**Difficulty:** 🟢 Basic

**Answer:**

Room reduces boilerplate, validates queries at compile time, integrates with coroutines/Flow, and provides a structured migration model.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q48. What are the main components of Room?

**Difficulty:** 🟢 Basic

**Answer:**

Entity, DAO, and RoomDatabase.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q49. What is an Entity?

**Difficulty:** 🟢 Basic

**Answer:**

A class mapped to a database table.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q50. What is a DAO?

**Difficulty:** 🟢 Basic

**Answer:**

A Data Access Object that defines database operations such as queries, inserts, updates, and deletes.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q51. What is RoomDatabase?

**Difficulty:** 🟢 Basic

**Answer:**

The database holder that defines entities, DAOs, database version, migrations, and configuration.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q52. What is a Primary Key?

**Difficulty:** 🟢 Basic

**Answer:**

A column or set of columns that uniquely identifies a row.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q53. Can an Entity have multiple primary-key columns?

**Difficulty:** 🟢 Basic

**Answer:**

Yes. Room supports composite primary keys.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q54. What is a foreign key?

**Difficulty:** 🟢 Basic

**Answer:**

A constraint that represents a relationship between tables and helps maintain referential integrity.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q55. What is a Room relation?

**Difficulty:** 🟢 Basic

**Answer:**

A way to model and retrieve related entities, such as a user and its orders.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q56. What is the difference between @Embedded and @Relation?

**Difficulty:** 🟢 Basic

**Answer:**

`@Embedded` flattens fields into the containing result. `@Relation` represents data from another entity/table.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q57. How does Room verify SQL queries?

**Difficulty:** 🟢 Basic

**Answer:**

Room's compiler/annotation processing analyzes DAO queries against the known schema and can report many errors at compile time.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q58. What happens if your SQL query has an invalid column name?

**Difficulty:** 🟢 Basic

**Answer:**

Room can normally detect the mismatch during compilation.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q59. Can Room return Flow<T>?

**Difficulty:** 🟢 Basic

**Answer:**

Yes. Observable DAO queries can return Flow and emit when relevant tables change.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q60. Can Room perform suspend operations?

**Difficulty:** 🟢 Basic

**Answer:**

Yes. DAO operations can be `suspend` and execute off the main thread when used correctly.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q61. Why shouldn't database operations run on the main thread?

**Difficulty:** 🟢 Basic

**Answer:**

They can block UI rendering and cause jank or ANRs.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q62. What is allowMainThreadQueries() and why should it generally be avoided?

**Difficulty:** 🟢 Basic

**Answer:**

It permits DB operations on the main thread. It is generally inappropriate for production because slow queries can block UI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q63. How would you handle database transactions in Room?

**Difficulty:** 🟢 Basic

**Answer:**

Use transaction APIs or `@Transaction` for operations that must succeed or fail as one unit.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q64. What is @Transaction?

**Difficulty:** 🟢 Basic

**Answer:**

It marks a DAO method or operation to execute atomically in a database transaction.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q65. What happens if one operation inside a transaction fails?

**Difficulty:** 🟢 Basic

**Answer:**

The transaction is rolled back, preventing a partial commit.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q66. How would you model one-to-many relationships?

**Difficulty:** 🟢 Basic

**Answer:**

Use separate tables with a foreign key from the child to the parent, then query the relationship explicitly or with `@Relation`.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q67. How would you model many-to-many relationships?

**Difficulty:** 🟢 Basic

**Answer:**

Use two entities plus a junction table with a composite primary key or unique constraint.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q68. What are TypeConverters?

**Difficulty:** 🟢 Basic

**Answer:**

Converters that map unsupported application types to database-supported types and back.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q69. When would you need a TypeConverter?

**Difficulty:** 🟢 Basic

**Answer:**

For types Room cannot directly persist, such as certain date/time types, enums, or custom value objects.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q70. Can Room store an enum directly?

**Difficulty:** 🟢 Basic

**Answer:**

Use an explicit converter when needed so the persisted representation is deliberate and migration-safe.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q71. Can Room store a custom object?

**Difficulty:** 🟢 Basic

**Answer:**

Use `@Embedded`, a separate Entity, or a TypeConverter depending on whether the object's fields need independent querying.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q72. How would you store a list in Room?

**Difficulty:** 🟢 Basic

**Answer:**

A TypeConverter can serialize it, but if list elements need independent querying/relationships, model them as rows in another table.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 5. Intermediate → Senior Room

### Q73. Suppose User → Orders → Products → Payments. How would you model it?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use normalized tables: User, Order(userId), Product, OrderItem(orderId/productId), and Payment(orderId), with foreign keys and appropriate indexes.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q74. When would you use @Embedded versus a separate Entity?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use @Embedded for fields that conceptually belong to the same row. Use an Entity when the data has identity, relationships, independent queries, or its own lifecycle.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q75. What problems can occur if you use @Relation heavily?

**Difficulty:** 🟡 Intermediate

**Answer:**

Large object graphs can produce complex queries and excessive data loading. Performance-sensitive screens often benefit from explicit projections and SQL.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q76. How would you prevent N+1 database queries?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use joins, batch queries, projections, or carefully designed relations instead of querying related data separately for every row.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q77. How would you paginate 100,000 records from Room?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use Paging 3 with a Room PagingSource rather than loading all records into memory.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q78. How does Paging 3 integrate with Room?

**Difficulty:** 🟡 Intermediate

**Answer:**

Room can provide a PagingSource; Pager exposes PagingData that the UI consumes incrementally.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q79. What happens when Room observes database changes using Flow?

**Difficulty:** 🟡 Intermediate

**Answer:**

Room invalidates the observable query when relevant tables change and emits updated results.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q80. What is the difference between Flow<List<User>> and Flow<User?>?

**Difficulty:** 🟡 Intermediate

**Answer:**

The first represents a changing collection; the second represents one possibly absent record. Their query semantics and result sizes differ.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q81. How would you handle database operations when the application goes offline?

**Difficulty:** 🟡 Intermediate

**Answer:**

Treat Room as the local store, read from it immediately, and synchronize with the server separately. For writes, an outbox/pending-operation model may be appropriate.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q82. How would you design a local cache using Room?

**Difficulty:** 🟡 Intermediate

**Answer:**

Store normalized/queryable metadata in Room, define freshness metadata, and keep large binary files in suitable file storage.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q83. API returns 10,000 products. Would you store all in Room?

**Difficulty:** 🟡 Intermediate

**Answer:**

Only if offline/query requirements justify it. Consider product size, device storage, sync frequency, pagination, and whether the UI needs local search.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q84. How would you prevent duplicate records while syncing API data with Room?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use stable server IDs as primary/unique keys and upsert rather than blindly inserting.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q85. How would you design an offline-first repository?

**Difficulty:** 🟡 Intermediate

**Answer:**

API updates Room; Room is the observable local source of truth; UI observes Room through Flow.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q86. What is your source of truth: API or Room?

**Difficulty:** 🟡 Intermediate

**Answer:**

The server is the remote source of truth; Room is typically the local source of truth for offline UI state.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q87. How would you resolve conflicts between local and remote data?

**Difficulty:** 🟡 Intermediate

**Answer:**

Define a policy such as server-wins, client-wins, version-based, field-level merge, or explicit user resolution.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q88. How would you implement 'last updated' synchronization?

**Difficulty:** 🟡 Intermediate

**Answer:**

Persist server version/cursor or server timestamps where possible, plus local sync metadata. Prefer server-provided versions over device clocks.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q89. How would you handle partial synchronization?

**Difficulty:** 🟡 Intermediate

**Answer:**

Track independent checkpoints/cursors per resource or sync job and make the sync idempotent.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q90. What indexes would you add to a large Room table?

**Difficulty:** 🟡 Intermediate

**Answer:**

Index columns frequently used in WHERE, JOIN, and sometimes ORDER BY clauses. Avoid unnecessary indexes because they increase storage and write cost.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 6. Database Migration — Senior/Lead

### Q91. What is a Room database migration?

**Difficulty:** 🟡 Intermediate

**Answer:**

A controlled schema transformation from one database version to another while preserving existing user data.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q92. Why are migrations required?

**Difficulty:** 🟡 Intermediate

**Answer:**

Existing users can have older on-device schemas when a new app version is installed.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q93. What happens if you change an Entity without providing a migration?

**Difficulty:** 🟡 Intermediate

**Answer:**

If Room cannot find a valid path from the existing schema version to the new one, opening the database fails.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q94. What is the difference between Migration 1→2/2→3 and destructive migration?

**Difficulty:** 🟡 Intermediate

**Answer:**

Incremental migrations transform and preserve data. Destructive migration recreates tables and loses stored data.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q95. When would you use fallbackToDestructiveMigration()?

**Difficulty:** 🟡 Intermediate

**Answer:**

Only when the database data is disposable, such as a rebuildable cache, and data loss is explicitly acceptable.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q96. Why can destructive migration be dangerous in production?

**Difficulty:** 🟡 Intermediate

**Answer:**

It can permanently delete user data when a migration path is missing.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q97. Production database is v10 and app is v15. How does Room migrate it?

**Difficulty:** 🟡 Intermediate

**Answer:**

Room follows a valid migration path from v10 through the required versions to v15, using automatic and/or manual migrations.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q98. What happens if the user skips several application versions?

**Difficulty:** 🟡 Intermediate

**Answer:**

The database still needs a valid migration path from the user's existing database version to the current version.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q99. How would you test database migrations?

**Difficulty:** 🟡 Intermediate

**Answer:**

Export schemas, create migration tests for supported starting versions, migrate a real old schema, and verify data, constraints, indexes, and relationships.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q100. How would you migrate name → firstName + lastName?

**Difficulty:** 🟡 Intermediate

**Answer:**

Perform a deliberate data migration, handling null/empty/multi-word names and defining a documented fallback for ambiguous values.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q101. How would you migrate String → Int?

**Difficulty:** 🟡 Intermediate

**Answer:**

Convert existing values in SQL or migration code and define behavior for invalid strings before enforcing the new type.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q102. How would you rename a table?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use a supported schema migration/rename operation and verify indexes, foreign keys, and the resulting Room schema.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q103. How would you rename a column without losing data?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use a rename-aware migration or table-rebuild strategy rather than dropping and recreating the column.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q104. How would you add a non-null column to an existing table?

**Difficulty:** 🟡 Intermediate

**Answer:**

Provide a valid default for existing rows or explicitly populate the column during migration.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q105. What happens when existing rows don't have a value for the new column?

**Difficulty:** 🟡 Intermediate

**Answer:**

The migration must provide a default or transformation so every existing row satisfies the new non-null constraint.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q106. How would you migrate millions of local records without freezing the application?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use efficient SQL, avoid loading all rows into memory, test timing, and consider staged/background transformation where appropriate.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q107. What would you do if a migration works locally but crashes for production users?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use crash telemetry to identify the starting schema/data state, reproduce it, add migration tests for that state, and use staged rollout.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q108. How would you recover from a corrupted database after an app update?

**Difficulty:** 🟡 Intermediate

**Answer:**

Determine whether data can be recovered; for disposable/cache data rebuild it, but for user-critical data prefer recovery/remote restoration and avoid blind destructive recreation.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 7. Scoped Storage

### Q109. What is Scoped Storage?

**Difficulty:** 🟡 Intermediate

**Answer:**

A modern Android storage model that restricts arbitrary access to shared external storage and encourages purpose-based APIs such as app-specific storage, MediaStore, SAF, and Photo Picker.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q110. Why was Scoped Storage introduced?

**Difficulty:** 🟡 Intermediate

**Answer:**

To improve user privacy, reduce arbitrary filesystem access, and reduce storage clutter.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q111. Which Android version introduced Scoped Storage?

**Difficulty:** 🟡 Intermediate

**Answer:**

Android 10/API 29 introduced the scoped-storage model.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q112. What changed in Android 10?

**Difficulty:** 🟡 Intermediate

**Answer:**

Apps targeting API 29+ were given scoped external-storage behavior by default, with transitional compatibility behavior at the time.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q113. What changed in Android 11?

**Difficulty:** 🟡 Intermediate

**Answer:**

Scoped storage became required for apps targeting Android 11/API 30; the legacy opt-out was no longer effective for those targets.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q114. How does Scoped Storage affect access to external storage?

**Difficulty:** 🟡 Intermediate

**Answer:**

Apps have access to their app-specific external directory and appropriate shared media/selected documents rather than arbitrary shared paths.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q115. Difference between app-specific external and shared external storage?

**Difficulty:** 🟡 Intermediate

**Answer:**

App-specific external files are intended for the app and are removed on uninstall; shared storage is for user/shareable content and can persist after uninstall.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q116. Can your application directly access another application's external files?

**Difficulty:** 🟡 Intermediate

**Answer:**

Normally no under scoped storage.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q117. Why doesn't WRITE_EXTERNAL_STORAGE work the same way on newer Android?

**Difficulty:** 🟡 Intermediate

**Answer:**

Modern storage access is use-case based; the permission does not provide a universal bypass of scoped storage.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q118. What is MANAGE_EXTERNAL_STORAGE?

**Difficulty:** 🟡 Intermediate

**Answer:**

A special broad-access permission for apps whose core functionality genuinely requires access to most shared files.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q119. Should every file-management application request MANAGE_EXTERNAL_STORAGE?

**Difficulty:** 🟡 Intermediate

**Answer:**

No. First determine whether MediaStore, SAF, app-specific storage, or other APIs satisfy the core use case.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q120. What are legitimate use cases for all-files access?

**Difficulty:** 🟡 Intermediate

**Answer:**

Examples include file managers, backup/restore, antivirus, and certain document-management tools where broad access is core functionality.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q121. Why should most applications avoid MANAGE_EXTERNAL_STORAGE?

**Difficulty:** 🟡 Intermediate

**Answer:**

It grants broad access, is unnecessary for most use cases, and is subject to platform/Google Play policy constraints.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q122. What is requestLegacyExternalStorage?

**Difficulty:** 🟡 Intermediate

**Answer:**

A transitional compatibility flag that allowed an app targeting Android 10 to opt out of scoped storage.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q123. What happens to requestLegacyExternalStorage when targeting Android 11+?

**Difficulty:** 🟡 Intermediate

**Answer:**

It is ignored for apps targeting Android 11/API 30 or higher.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q124. How would you migrate an old application from legacy storage to Scoped Storage?

**Difficulty:** 🟡 Intermediate

**Answer:**

Inventory legacy paths, classify each file as private/cache/shared media/document, migrate to the appropriate modern location, make the migration idempotent, and verify before deleting legacy copies.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q125. Old app stores files at /sdcard/MyApp/. How would you migrate?

**Difficulty:** 🟡 Intermediate

**Answer:**

Detect legacy files, copy/move them to appropriate app-specific or shared storage, persist migration progress, handle process death, verify results, then clean up safely.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 8. MediaStore

### Q126. What is MediaStore?

**Difficulty:** 🟡 Intermediate

**Answer:**

An indexed Android API for shared media collections such as images, video, and audio.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q127. When should you use MediaStore?

**Difficulty:** 🟡 Intermediate

**Answer:**

When media is intended to be user-visible/shareable and should live in shared media collections.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q128. How would you save an image captured by your app into the user's gallery?

**Difficulty:** 🟡 Intermediate

**Answer:**

Insert into MediaStore.Images with metadata such as display name/MIME type/relative path, write through the returned content URI, and finalize the item.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q129. How would you save a video into shared storage?

**Difficulty:** 🟡 Intermediate

**Answer:**

Insert into the appropriate MediaStore video collection and stream the bytes through the returned content URI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q130. What is ContentValues used for with MediaStore?

**Difficulty:** 🟡 Intermediate

**Answer:**

It supplies metadata for the item being inserted or updated.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q131. What is RELATIVE_PATH?

**Difficulty:** 🟡 Intermediate

**Answer:**

A logical location within a shared media collection, avoiding hard-coded filesystem paths.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q132. What is IS_PENDING?

**Difficulty:** 🟡 Intermediate

**Answer:**

A flag used while a shared media item is being written so it isn't treated as finalized content prematurely.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q133. Why is IS_PENDING useful for large media?

**Difficulty:** 🟡 Intermediate

**Answer:**

It prevents partially written media from being exposed as completed media.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q134. What happens if the app crashes while writing media?

**Difficulty:** 🟡 Intermediate

**Answer:**

The item may remain pending; production code should have cleanup/recovery logic for abandoned writes.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q135. How would you retrieve images from MediaStore?

**Difficulty:** 🟡 Intermediate

**Answer:**

Query the Images external content URI through ContentResolver with a minimal projection and appropriate selection/sort.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q136. How would you retrieve only videos?

**Difficulty:** 🟡 Intermediate

**Answer:**

Query the MediaStore video collection rather than scanning all media types.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q137. How would you query MediaStore efficiently?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use only required columns, appropriate filters, sensible sorting, and incremental loading where needed.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q138. Difference between file path and content URI?

**Difficulty:** 🟡 Intermediate

**Answer:**

A file path identifies a filesystem location; a content URI identifies data through a provider and should be accessed via ContentResolver.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q139. Why avoid assuming a MediaStore item has a filesystem path?

**Difficulty:** 🟡 Intermediate

**Answer:**

A content URI may represent provider-managed data without a stable traditional path.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q140. How would you delete media created by your app?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use the media item's URI with ContentResolver and follow current ownership/permission rules.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q141. How would you modify media belonging to another app?

**Difficulty:** 🟡 Intermediate

**Answer:**

Access depends on Android's permission and user-consent model; don't assume ownership.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q142. How would you handle user confirmation for deleting shared media?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use the appropriate platform APIs to request user authorization when required.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 9. Storage Access Framework

### Q143. What is Storage Access Framework?

**Difficulty:** 🟡 Intermediate

**Answer:**

A system picker framework that lets users choose documents or directories and grants the app access to the resulting URI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q144. Why was SAF introduced?

**Difficulty:** 🟡 Intermediate

**Answer:**

To give users explicit control over document access while supporting local and cloud document providers.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q145. ACTION_OPEN_DOCUMENT vs ACTION_GET_CONTENT?

**Difficulty:** 🟡 Intermediate

**Answer:**

OPEN_DOCUMENT is designed for document-provider access and durable URI permissions; GET_CONTENT is generally for obtaining content for immediate use.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q146. What is ACTION_CREATE_DOCUMENT?

**Difficulty:** 🟡 Intermediate

**Answer:**

A user-driven flow for creating/saving a new document at a chosen location.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q147. What is ACTION_OPEN_DOCUMENT_TREE?

**Difficulty:** 🟡 Intermediate

**Answer:**

A user-driven flow for selecting a directory and granting access to that tree.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q148. When would you use each?

**Difficulty:** 🟡 Intermediate

**Answer:**

Open existing file → ACTION_OPEN_DOCUMENT; create/export → ACTION_CREATE_DOCUMENT; choose directory → ACTION_OPEN_DOCUMENT_TREE.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q149. What is a content:// URI?

**Difficulty:** 🟡 Intermediate

**Answer:**

An identifier for data exposed by a ContentProvider or document provider; it is not necessarily a filesystem path.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q150. What is URI permission persistence?

**Difficulty:** 🟡 Intermediate

**Answer:**

A mechanism that lets an app retain access to a user-selected document URI after the original activity/session ends, when the provider grants it.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q151. What does takePersistableUriPermission() do?

**Difficulty:** 🟡 Intermediate

**Answer:**

It asks the system to persist granted URI access so the app can reuse the URI later.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q152. User selects a PDF and closes the app. Can you access it later?

**Difficulty:** 🟡 Intermediate

**Answer:**

Only if the grant is persistable, you take the permission, and you save the URI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q153. How would you persist access to a user-selected document?

**Difficulty:** 🟡 Intermediate

**Answer:**

Request a persistable grant, call takePersistableUriPermission(), and persist the URI string.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q154. Why shouldn't you convert every content URI into a filesystem path?

**Difficulty:** 🟡 Intermediate

**Answer:**

Some providers, including cloud/virtual providers, have no conventional local path.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q155. How would you upload a file selected through SAF?

**Difficulty:** 🟡 Intermediate

**Answer:**

Use ContentResolver.openInputStream() or an equivalent streaming API and send it through the HTTP request body.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q156. How would you stream a large file without loading it into memory?

**Difficulty:** 🟡 Intermediate

**Answer:**

Stream from the URI/InputStream directly into the network request body.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 10. Advanced File Handling

### Q157. You need to download a 2 GB video. Where would you store it?

**Difficulty:** 🔴 Advanced

**Answer:**

Private-only video → app-specific storage; user-visible shared video → MediaStore; user chooses destination → SAF.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q158. The video should be private to your application. What storage would you choose?

**Difficulty:** 🔴 Advanced

**Answer:**

App-specific storage, with cleanup and available-space checks.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q159. The video should appear in the user's gallery. What storage would you choose?

**Difficulty:** 🔴 Advanced

**Answer:**

MediaStore's video collection.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q160. The user should choose where to save the video. What API would you use?

**Difficulty:** 🔴 Advanced

**Answer:**

ACTION_CREATE_DOCUMENT/SAF.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q161. How would you download a PDF and allow the user to share it?

**Difficulty:** 🔴 Advanced

**Answer:**

Keep it in app-specific storage and share via a content URI/FileProvider, or let the user export it via SAF if it should become user-owned.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q162. You need to download 500 images for offline use. Would you store them in Room?

**Difficulty:** 🔴 Advanced

**Answer:**

Store metadata in Room and binary images in appropriate file/cache storage.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q163. How would you design a local file cache?

**Difficulty:** 🔴 Advanced

**Answer:**

Use Room for metadata such as URL, URI/path, size, timestamps and ETag; store binary files separately.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q164. How would you prevent cache from consuming all storage?

**Difficulty:** 🔴 Advanced

**Answer:**

Set size/TTL limits and evict least-recently-used or expired entries.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q165. How would you implement cache eviction?

**Difficulty:** 🔴 Advanced

**Answer:**

Track size and last-accessed time, delete oldest/least-used files until below a target threshold, and keep metadata consistent.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q166. Difference between cache and persistent app data?

**Difficulty:** 🔴 Advanced

**Answer:**

Cache is disposable/recreatable; persistent app data is required to preserve application/user state.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q167. What happens when Android deletes cache?

**Difficulty:** 🔴 Advanced

**Answer:**

The app must recreate it; correctness cannot depend on cache contents.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q168. How would you detect and recover from a partially downloaded file?

**Difficulty:** 🔴 Advanced

**Answer:**

Use a temporary file plus persisted download metadata and validate size/checksum before marking the final file complete.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q169. How would you make file writes atomic?

**Difficulty:** 🔴 Advanced

**Answer:**

Write to a temporary file and publish/rename it only after successful completion.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q170. How would you prevent corruption if the app is killed while writing a file?

**Difficulty:** 🔴 Advanced

**Answer:**

Use temp files, persisted state, atomic publication, validation/checksums, and resume/retry logic.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 11. Storage Security

### Q171. Where would you store an authentication token?

**Difficulty:** 🔴 Advanced

**Answer:**

Use private/protected storage and minimize exposure. For sensitive credentials, consider Keystore-backed encryption.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q172. Is storing a token in internal storage automatically secure?

**Difficulty:** 🔴 Advanced

**Answer:**

Internal storage provides app isolation but is not the same as cryptographic encryption or protection against a compromised/rooted device.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q173. Should sensitive information be stored in a plain JSON file?

**Difficulty:** 🔴 Advanced

**Answer:**

Avoid it when the information is sensitive. Use protected storage and encryption where the threat model requires it.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q174. How would you encrypt locally stored sensitive data?

**Difficulty:** 🔴 Advanced

**Answer:**

Use authenticated encryption such as AES-GCM with keys protected by Android Keystore.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q175. Where would you store encryption keys?

**Difficulty:** 🔴 Advanced

**Answer:**

Android Keystore, not alongside the encrypted data.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q176. What is Android Keystore?

**Difficulty:** 🔴 Advanced

**Answer:**

A platform mechanism for generating and protecting cryptographic keys with controlled access; hardware-backed protection may be available.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q177. Difference between storing data securely and storing it privately?

**Difficulty:** 🔴 Advanced

**Answer:**

Private storage controls ordinary app access; encryption protects the data cryptographically. They address different threats.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q178. Can a rooted device access your application's internal files?

**Difficulty:** 🔴 Advanced

**Answer:**

A privileged/compromised device can invalidate normal sandbox assumptions.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q179. Should passwords ever be stored locally?

**Difficulty:** 🔴 Advanced

**Answer:**

Normally no. Use session/refresh tokens and appropriate authentication flows rather than storing raw passwords.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q180. How would you securely store refresh tokens?

**Difficulty:** 🔴 Advanced

**Answer:**

Use minimal-lifetime credentials, private/protected storage, and Keystore-backed encryption where appropriate.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q181. What information should never be included in filenames?

**Difficulty:** 🔴 Advanced

**Answer:**

Avoid filenames that expose sensitive personal, medical, financial, or credential information.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q182. What other local-storage security concerns should a senior developer consider?

**Difficulty:** 🔴 Advanced

**Answer:**

Backups, logs, screenshots, clipboard, temporary files, share intents, memory exposure, exported components, and encryption/key management.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 12. Architecture

### Q183. Where should storage logic live in Clean Architecture?

**Difficulty:** 🔴 Advanced

**Answer:**

In the data layer, behind repositories and data sources. Presentation/domain should not depend directly on Room/DataStore implementation details.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q184. Should the ViewModel directly access Room?

**Difficulty:** 🔴 Advanced

**Answer:**

Usually no. Prefer ViewModel → use case → repository → local data source.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q185. Should the Repository know whether data comes from Room, DataStore, or an API?

**Difficulty:** 🔴 Advanced

**Answer:**

Yes. The repository is a suitable abstraction boundary for coordinating local and remote sources.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q186. How would you design UI → ViewModel → UseCase → Repository → Remote + Local?

**Difficulty:** 🔴 Advanced

**Answer:**

Keep the UI reactive to domain/UI state; the repository coordinates remote and local data sources; for offline-first reads, Room can be the local source of truth.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q187. How would you define a single source of truth?

**Difficulty:** 🔴 Advanced

**Answer:**

Define one authoritative state representation for each concern. In offline-first UI, Room commonly acts as the local source of truth while the server remains remote truth.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q188. Suppose API and Room both expose Flow<List<Product>>. How would you combine them?

**Difficulty:** 🔴 Advanced

**Answer:**

Prefer API → Repository → Room → UI. The API updates Room, and the UI observes Room instead of combining network and DB flows per screen.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q189. Would you expose Room entities directly to the UI?

**Difficulty:** 🔴 Advanced

**Answer:**

Usually no. Map Entity → Domain Model → UI Model to decouple storage schema from presentation.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q190. Why separate Entity, DTO, Domain Model, UI Model?

**Difficulty:** 🔴 Advanced

**Answer:**

They represent different contracts: database, network, business logic, and presentation. Separation reduces coupling and makes changes safer.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q191. How would you synchronize Room with a REST API?

**Difficulty:** 🔴 Advanced

**Answer:**

Fetch remote changes, map DTOs to entities, apply updates transactionally, track cursors/versions, handle retries and conflicts, then let Room notify the UI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q192. Who should decide when data needs refreshing?

**Difficulty:** 🔴 Advanced

**Answer:**

The repository/use-case/data layer should own refresh policy. The ViewModel can express user intent such as pull-to-refresh.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q193. How would you prevent multiple screens from triggering the same synchronization?

**Difficulty:** 🔴 Advanced

**Answer:**

Centralize sync coordination and use a mutex/single-flight mechanism or WorkManager depending on the task.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q194. How would you handle cache invalidation?

**Difficulty:** 🔴 Advanced

**Answer:**

Define explicit TTL, version, ETag, cursor, manual-refresh, or event-driven rules. Avoid implicit/undefined staleness.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q195. How would you handle stale data?

**Difficulty:** 🔴 Advanced

**Answer:**

Keep freshness metadata and allow cached data to render while refresh happens in the background when appropriate.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q196. How would you expose local database changes using Flow?

**Difficulty:** 🔴 Advanced

**Answer:**

Return Flow from DAO, map it in repository/domain layers, and collect it into ViewModel UI state.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q197. How would you handle database errors in a Repository?

**Difficulty:** 🔴 Advanced

**Answer:**

Translate low-level exceptions into meaningful domain/data errors rather than exposing SQLite/Room exceptions directly to UI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q198. How would you test your storage layer independently from Android framework APIs?

**Difficulty:** 🔴 Advanced

**Answer:**

Unit-test repository behavior with fakes, DAO/integration-test persistence separately, and run dedicated migration tests.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## 13. Lead-Level System Design Scenarios

### Q199. Design an offline-first e-commerce app.

**Difficulty:** 🟣 System Design

**Answer:**

Use API + Room with Room as local UI source of truth. Use Paging for large catalogs, server IDs for upserts, explicit freshness/cursors, transactional sync, and a documented conflict policy.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q200. Design a news app with thousands of articles and images.

**Difficulty:** 🟣 System Design

**Answer:**

Store article metadata/content in Room, image binaries in file/cache storage, preferences in DataStore, and synchronize API → Room → Flow → UI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q201. Design an image-heavy app with 10,000 images.

**Difficulty:** 🟣 System Design

**Answer:**

Store metadata in Room and binaries in file/cache storage. Track URI/path, size, ETag and last-accessed time; enforce cache eviction.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q202. Design SharedPreferences → DataStore → Room migration.

**Difficulty:** 🟣 System Design

**Answer:**

Migrate preferences with SharedPreferencesMigration. Move only data that has become relational/queryable into Room; don't migrate everything blindly.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q203. Design a 4 GB download manager.

**Difficulty:** 🟣 System Design

**Answer:**

Persist download metadata in Room, use resumable background work, write to temporary files, validate and atomically publish on completion, and expose progress through observable state.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q204. Design an Android image gallery.

**Difficulty:** 🟣 System Design

**Answer:**

Use Photo Picker where appropriate for user selection, MediaStore for shared media, content URIs rather than assumed paths, and stream selected content for upload.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q205. Design an offline banking application's storage.

**Difficulty:** 🟣 System Design

**Answer:**

Use Room for structured account/transaction data, DataStore for non-sensitive preferences, protected storage/Keystore-backed encryption for credentials, and app-specific or user-selected storage for statements.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q206. Design a local file cache.

**Difficulty:** 🟣 System Design

**Answer:**

Keep binary files separate from queryable metadata. Use Room for cache index/freshness and file storage for bytes, with LRU/TTL/size eviction.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q207. Where should API caching happen?

**Difficulty:** 🟣 System Design

**Answer:**

At the repository/data layer. The API updates local persistence; the UI reads from the local source of truth.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q208. What if Room and API return different data?

**Difficulty:** 🟣 System Design

**Answer:**

Define remote/local truth explicitly and a conflict policy. For read-heavy offline-first apps, API normally reconciles into Room, which drives UI.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

### Q209. How would you handle stale cache?

**Difficulty:** 🟣 System Design

**Answer:**

Store server/local freshness metadata, render valid cached data, and revalidate asynchronously according to product requirements.

**Senior/Lead follow-up:**

Be ready to explain the trade-off, failure mode, testing strategy, and why you would choose this approach over the obvious alternatives.

---

## Interview mindset

For any storage question, move beyond the API name. Explain:

1. What problem it solves.
2. When to use it.
3. When not to use it.
4. Privacy/security implications.
5. Lifecycle and uninstall behavior.
6. Performance implications.
7. Failure and recovery behavior.
8. Migration strategy.
9. Testing strategy.
10. How the choice changes at scale.

## Quick decision tree

```text
What kind of data?
        |
        +-- Small preferences/state ------> DataStore
        |
        +-- Structured/queryable data -----> Room
        |
        +-- Private app file -------------> App-specific storage
        |
        +-- Temporary/recreatable --------> Cache
        |
        +-- User-visible media ------------> MediaStore
        |
        +-- User-selected document --------> Storage Access Framework
```

> The exact API choice should follow the data's ownership, lifecycle, visibility, size, and access requirements—not developer familiarity.
