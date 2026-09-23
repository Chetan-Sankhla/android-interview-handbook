# Storage Fundamentals

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

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

Try to answer these aloud before revealing the answer.

1. **How do DataStore and Room differ?**

<details>
<summary>Reveal sample answer</summary>

Room is a better fit when the data needs queries, indexes, relationships, or partial updates. DataStore is intended for small application state rather than acting as a general-purpose database.

</details>

2. **When should a file be used instead of a database?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **When should data go into shared storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **How does uninstall affect each storage type?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Choosing Room for every kind of local data, or storing large binary files as database blobs without a specific reason.



### Quick Revision

**Key idea:** Android provides several storage mechanisms, and the correct choice depends on **ownership, size, structure, visibility, and lifecycle**. - **App-specific internal storage**: private files and cache.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What is app-specific storage?

**Difficulty:** 🟢 Basic

### Answer

App-specific storage is intended for files that belong to your application and are not meant to be independently accessed by other apps.

Android provides app-specific **internal** and **external** directories. Internal storage is private to the app. App-specific external storage is still intended for the app, but its access characteristics differ from internal storage.

Use it for things such as downloaded content that only your app needs, generated files, private exports, or local working data. If the data can be recreated, cache storage is usually more appropriate.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Internal vs app-specific external storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What happens when the app is uninstalled?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Where would you store sensitive files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **When should MediaStore be used instead?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming every file on external storage is automatically private just because the app created it.



### Quick Revision

**Key idea:** App-specific storage is intended for files that belong to your application and are not meant to be independently accessed by other apps. Android provides app-specific **internal** and **external** directories.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is the difference between persistent files and cache files?

**Difficulty:** 🟢 Basic

### Answer

Persistent app-specific files are expected to survive normal application operation and are used for data the app needs to retain. Cache files are explicitly disposable: Android may remove them when storage is constrained.

A cache should therefore never be the only copy of information required for correctness. If deleting the cache breaks the application, that data is probably not really cache data.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What should go into cacheDir?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can Android delete cache files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you implement cache eviction?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **How does cache differ from a database?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating cache as durable storage or assuming the OS will preserve cache indefinitely.



### Quick Revision

**Key idea:** Persistent app-specific files are expected to survive normal application operation and are used for data the app needs to retain. Cache files are explicitly disposable: Android may remove them when storage is constrained.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. When should data be stored in a database instead of a file?

**Difficulty:** 🟡 Intermediate

### Answer

Use a database when the data is **structured and queryable**. Typical signals are filtering, sorting, joins, relationships, indexes, partial updates, transactions, or independent records.

Use files for large binary or opaque content such as images, videos, PDFs, or archives. A common production design is to keep **metadata in Room and bytes in files**, rather than putting every binary object into a database.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Would you store an image in Room?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How would you model a file cache?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you query metadata efficiently?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **What are the trade-offs of BLOB storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Putting large media directly into Room simply because Room is already available.



### Quick Revision

**Key idea:** Use a database when the data is **structured and queryable**. Typical signals are filtering, sorting, joins, relationships, indexes, partial updates, transactions, or independent records.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What happens to app-specific files after uninstall?

**Difficulty:** 🟢 Basic

### Answer

App-specific files are normally removed when the application is uninstalled. This is one of the key distinctions from user-owned shared storage, where files are intended to survive independently of the application.

This means app-specific storage is a good fit for application-owned downloads, generated data, and private working files, but not necessarily for files the user expects to keep after removing the app.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Which storage survives uninstall?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Would you store user documents in app-specific storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you migrate old app-owned files to shared storage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming an app-specific file can be recovered after uninstall.



### Quick Revision

**Key idea:** App-specific files are normally removed when the application is uninstalled. This is one of the key distinctions from user-owned shared storage, where files are intended to survive independently of the application.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. Why should an Android app avoid hard-coded filesystem paths?

**Difficulty:** 🟡 Intermediate

### Answer

Android's storage model is purpose-based and can involve internal storage, removable volumes, media providers, document providers, and cloud-backed providers. A hard-coded path assumes a filesystem layout that may not exist or may not be appropriate.

Use framework APIs such as `filesDir`, `cacheDir`, `MediaStore`, and SAF URIs. Treat a `content://` URI as an opaque provider reference rather than automatically converting it into a filesystem path.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why are content URIs important?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can every content URI be converted to a path?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you upload a SAF-selected file?** → [Open the related question](mediastore-and-saf.md#how-would-you-upload-a-saf-selected-file)

### Common Mistake

Writing utility code that assumes every URI maps to `/storage/emulated/0/...`.



### Quick Revision

**Key idea:** Android's storage model is purpose-based and can involve internal storage, removable volumes, media providers, document providers, and cloud-backed providers. A hard-coded path assumes a filesystem layout that may not exist or may not be appropriate.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. What is the purpose-based storage model?

**Difficulty:** 🟡 Intermediate

### Answer

Modern Android storage is organized around what the app is trying to do rather than giving every app unrestricted filesystem access. Private app data uses app-specific storage; user media uses MediaStore; user-selected documents use SAF; small state uses DataStore; structured data uses Room.

This model reduces unnecessary access to unrelated user files and makes ownership explicit.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How does scoped storage fit into this model?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **When is broad file access justified?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Why is SAF privacy-friendly?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Starting with MANAGE_EXTERNAL_STORAGE and designing the storage model around that permission.



### Quick Revision

**Key idea:** Modern Android storage is organized around what the app is trying to do rather than giving every app unrestricted filesystem access. Private app data uses app-specific storage; user media uses MediaStore; user-selected documents use SAF; small state uses DataStore; structured data uses Room.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
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

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/training/data-storage
- https://developer.android.com/training/data-storage/app-specific
- https://developer.android.com/training/data-storage/shared

### Quick Revision

**Key idea:** Ask these questions in order: 1. Is it structured/queryable?

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
