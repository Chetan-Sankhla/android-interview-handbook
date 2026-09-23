# Storage Fundamentals

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What storage options are available to an Android application?

**Difficulty:** 🟢 Basic

### Answer

Android provides several storage mechanisms, and the correct choice depends on **ownership, size, structure, visibility, and lifecycle**.

- **App-specific internal storage**: private files and cache.
- **App-specific external storage**: larger app-private files on external storage.
- **DataStore**: small preferences or typed application state.
- **Room/SQLite**: structured, queryable relational data.
- **MediaStore**: user-visible shared photos, videos, audio, and related media.
- **Storage Access Framework (SAF)**: user-selected documents and directories.
- **Cache storage**: disposable data that can be recreated.

A senior developer should choose based on the data's requirements rather than simply asking which API is easiest.

### Common Follow-ups

- How do DataStore and Room differ?
- When should a file be used instead of a database?
- When should data go into shared storage?
- How does uninstall affect each storage type?

### Senior/Lead Perspective

A strong answer starts with the data model rather than the API. For example, an e-commerce app might keep preferences in DataStore, products/orders in Room, downloaded images in app-specific storage, and user-exported invoices through SAF.

### Common Mistake

Choosing Room for every kind of local data, or storing large binary files as database blobs without a specific reason.

## Q2. What is app-specific storage?

**Difficulty:** 🟢 Basic

### Answer

App-specific storage is intended for files that belong to your application and are not meant to be independently accessed by other apps.

Android provides app-specific **internal** and **external** directories. Internal storage is private to the app. App-specific external storage is still intended for the app, but its access characteristics differ from internal storage.

Use it for things such as downloaded content that only your app needs, generated files, private exports, or local working data. If the data can be recreated, cache storage is usually more appropriate.

### Common Follow-ups

- Internal vs app-specific external storage?
- What happens when the app is uninstalled?
- Where would you store sensitive files?
- When should MediaStore be used instead?

### Senior/Lead Perspective

The important architectural decision is whether the file is **app-owned** or **user-owned**. If the user expects the file to remain visible and useful outside your app, shared storage is usually more appropriate.

### Common Mistake

Assuming every file on external storage is automatically private just because the app created it.

## Q3. What is the difference between persistent files and cache files?

**Difficulty:** 🟢 Basic

### Answer

Persistent app-specific files are expected to survive normal application operation and are used for data the app needs to retain. Cache files are explicitly disposable: Android may remove them when storage is constrained.

A cache should therefore never be the only copy of information required for correctness. If deleting the cache breaks the application, that data is probably not really cache data.

### Common Follow-ups

- What should go into cacheDir?
- Can Android delete cache files?
- How would you implement cache eviction?
- How does cache differ from a database?

### Senior/Lead Perspective

For a production cache, define an eviction policy such as maximum size, TTL, or LRU. Keep enough metadata to rebuild the cache after process death or eviction.

### Common Mistake

Treating cache as durable storage or assuming the OS will preserve cache indefinitely.

## Q4. When should data be stored in a database instead of a file?

**Difficulty:** 🟡 Intermediate

### Answer

Use a database when the data is **structured and queryable**. Typical signals are filtering, sorting, joins, relationships, indexes, partial updates, transactions, or independent records.

Use files for large binary or opaque content such as images, videos, PDFs, or archives. A common production design is to keep **metadata in Room and bytes in files**, rather than putting every binary object into a database.

### Common Follow-ups

- Would you store an image in Room?
- How would you model a file cache?
- How do you query metadata efficiently?
- What are the trade-offs of BLOB storage?

### Senior/Lead Perspective

A useful rule is: if the application needs SQL semantics for the content itself, use Room; if the content is opaque bytes and the app mainly needs a reference to it, store the bytes separately and keep metadata in Room.

### Common Mistake

Putting large media directly into Room simply because Room is already available.

## Q5. What happens to app-specific files after uninstall?

**Difficulty:** 🟢 Basic

### Answer

App-specific files are normally removed when the application is uninstalled. This is one of the key distinctions from user-owned shared storage, where files are intended to survive independently of the application.

This means app-specific storage is a good fit for application-owned downloads, generated data, and private working files, but not necessarily for files the user expects to keep after removing the app.

### Common Follow-ups

- Which storage survives uninstall?
- Would you store user documents in app-specific storage?
- How would you migrate old app-owned files to shared storage?

### Senior/Lead Perspective

Before moving user-visible data into app-specific storage, decide whether the product promises that the data belongs to the user independently of the app.

### Common Mistake

Assuming an app-specific file can be recovered after uninstall.

## Q6. Why should an Android app avoid hard-coded filesystem paths?

**Difficulty:** 🟡 Intermediate

### Answer

Android's storage model is purpose-based and can involve internal storage, removable volumes, media providers, document providers, and cloud-backed providers. A hard-coded path assumes a filesystem layout that may not exist or may not be appropriate.

Use framework APIs such as `filesDir`, `cacheDir`, `MediaStore`, and SAF URIs. Treat a `content://` URI as an opaque provider reference rather than automatically converting it into a filesystem path.

### Common Follow-ups

- Why are content URIs important?
- Can every content URI be converted to a path?
- How would you upload a SAF-selected file?

### Senior/Lead Perspective

A robust abstraction should accept streams or URIs where possible instead of requiring a local path. This also makes cloud/document-provider integrations much easier.

### Common Mistake

Writing utility code that assumes every URI maps to `/storage/emulated/0/...`.

## Q7. What is the purpose-based storage model?

**Difficulty:** 🟡 Intermediate

### Answer

Modern Android storage is organized around what the app is trying to do rather than giving every app unrestricted filesystem access. Private app data uses app-specific storage; user media uses MediaStore; user-selected documents use SAF; small state uses DataStore; structured data uses Room.

This model reduces unnecessary access to unrelated user files and makes ownership explicit.

### Common Follow-ups

- How does scoped storage fit into this model?
- When is broad file access justified?
- Why is SAF privacy-friendly?

### Senior/Lead Perspective

At architecture-review time, classify every stored artifact by ownership and access pattern before selecting an API. This usually exposes unnecessary permissions and overly broad storage access early.

### Common Mistake

Starting with MANAGE_EXTERNAL_STORAGE and designing the storage model around that permission.

## Q8. How should you decide where a new piece of data belongs?

**Difficulty:** 🟡 Intermediate

### Answer

Ask these questions in order:

1. Is it structured/queryable? → Room.
2. Is it small application state/preferences? → DataStore.
3. Is it private app-owned binary data? → app-specific files.
4. Is it disposable/recreatable? → cache.
5. Is it user-visible shared media? → MediaStore.
6. Does the user choose a document/location? → SAF.

Then evaluate security, backup behavior, migration, offline requirements, and expected size.

### Common Follow-ups

- Where would auth state go?
- Where would downloaded images go?
- Where would an exported PDF go?
- Where would an offline product catalog go?

### Senior/Lead Perspective

Senior-level design is often about defining the ownership boundary correctly. The API choice should follow that boundary, not the other way around.

### Common Mistake

Choosing storage based on familiarity instead of data ownership and lifecycle.

### References

- https://developer.android.com/training/data-storage
- https://developer.android.com/training/data-storage/app-specific
- https://developer.android.com/training/data-storage/shared