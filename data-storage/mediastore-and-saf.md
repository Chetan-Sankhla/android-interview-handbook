# MediaStore & Storage Access Framework

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is MediaStore?

**Difficulty:** 🟢 Basic

### Answer

MediaStore is Android's indexed interface to shared media collections such as images, video, and audio. It is appropriate when media is intended to be user-visible or shared outside the app.

Media created in shared media storage can remain on the device after the app is uninstalled, which is a key ownership difference from app-specific storage.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When should you use app-specific external storage instead?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What is RELATIVE_PATH?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What is IS_PENDING?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using MediaStore for private cache images that have no value outside the app.



### Quick Revision

**Key idea:** MediaStore is Android's indexed interface to shared media collections such as images, video, and audio. It is appropriate when media is intended to be user-visible or shared outside the app.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. How do you save an image to MediaStore?

**Difficulty:** 🟡 Intermediate

### Answer

Insert metadata into the appropriate MediaStore collection, including display name, MIME type, and where appropriate a relative path. The returned content URI is then used to write the bytes through `ContentResolver`.

For larger writes, `IS_PENDING` can be used so the item is not treated as finalized content until writing is complete.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why use a content URI?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What happens if writing fails?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you handle cleanup?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Writing to a filesystem path obtained from a MediaStore query.



### Quick Revision

**Key idea:** Insert metadata into the appropriate MediaStore collection, including display name, MIME type, and where appropriate a relative path. The returned content URI is then used to write the bytes through `ContentResolver`.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is IS_PENDING in MediaStore?

**Difficulty:** 🟡 Intermediate

### Answer

`IS_PENDING` allows an app to mark newly inserted media as still being written. While pending, the item is not treated as completed shared media.

After the file is successfully written, the app clears the pending state. This prevents partially written files from being exposed as finished media.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What if the process dies?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you clean abandoned pending items?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Why is this better than writing directly to a public path?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating insertion as equivalent to completed media publication.



### Quick Revision

**Key idea:** `IS_PENDING` allows an app to mark newly inserted media as still being written. While pending, the item is not treated as completed shared media.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. What is the Storage Access Framework?

**Difficulty:** 🟢 Basic

### Answer

SAF lets the user select documents or directories through a system picker. The app receives a URI representing the selected location and can access it according to the granted permissions.

It works with document providers, including external storage and cloud-backed providers, without requiring broad filesystem access for the selected content.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What are ACTION_OPEN_DOCUMENT, ACTION_CREATE_DOCUMENT, and ACTION_OPEN_DOCUMENT_TREE?** → [Open the related question](mediastore-and-saf.md#what-is-actionopendocumenttree)

2. **How do you persist URI access?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Can a URI have no local path?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating SAF as simply another file-path picker.



### Quick Revision

**Key idea:** SAF lets the user select documents or directories through a system picker. The app receives a URI representing the selected location and can access it according to the granted permissions.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What is the difference between ACTION_OPEN_DOCUMENT and ACTION_GET_CONTENT?

**Difficulty:** 🟡 Intermediate

### Answer

`ACTION_OPEN_DOCUMENT` is part of the Storage Access Framework and is designed around document-provider access, including persistable URI permissions where supported.

`ACTION_GET_CONTENT` is generally used when the app wants content for immediate use and does not need to take persistent ownership/access through the document-provider model.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When would you need a persistable URI permission?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Why does this matter for cloud providers?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What happens after process death?**

<details>
<summary>Reveal sample answer</summary>

ViewModel state survives configuration changes but not process death. Durable state belongs in persistent storage; small restorable UI state can use saved-state mechanisms where appropriate.

</details>

### Common Mistake

Using GET_CONTENT when the app actually needs durable access to the selected document.



### Quick Revision

**Key idea:** `ACTION_OPEN_DOCUMENT` is part of the Storage Access Framework and is designed around document-provider access, including persistable URI permissions where supported. `ACTION_GET_CONTENT` is generally used when the app wants content for immediate use and does not need to take persistent ownership/access through the document-provider model.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. What is ACTION_OPEN_DOCUMENT_TREE?

**Difficulty:** 🟡 Intermediate

### Answer

It lets the user select a directory and grants the app access to that directory tree, subject to platform restrictions and provider capabilities.

It is useful for workflows such as choosing an export folder or working with a user-selected collection of documents.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you persist access?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can every directory be selected?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **When is direct filesystem access unnecessary?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Requesting broad storage permission when a single user-selected directory is enough.



### Quick Revision

**Key idea:** It lets the user select a directory and grants the app access to that directory tree, subject to platform restrictions and provider capabilities. It is useful for workflows such as choosing an export folder or working with a user-selected collection of documents.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you persist a SAF URI permission?

**Difficulty:** 🟡 Intermediate

### Answer

When the provider grants a persistable permission, the app can call `takePersistableUriPermission()` with the appropriate read/write flags and persist the URI string in its own state.

On later launches, the app can reconstruct the URI and access the document again as long as the persisted grant remains valid.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What if the user revokes access?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Where should the URI string be stored?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you recover gracefully?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming a persisted URI is guaranteed to remain accessible forever.



### Quick Revision

**Key idea:** When the provider grants a persistable permission, the app can call `takePersistableUriPermission()` with the appropriate read/write flags and persist the URI string in its own state. On later launches, the app can reconstruct the URI and access the document again as long as the persisted grant remains valid.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How would you upload a SAF-selected file?

**Difficulty:** 🟡 Intermediate

### Answer

Do not assume the URI maps to a filesystem path. Open an `InputStream` through `ContentResolver` and stream the bytes into the network request.

For large files, avoid reading the entire document into memory. If the provider supports efficient seeking or size metadata, use those capabilities where the networking stack benefits from them.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/training/data-storage/shared/media
- https://developer.android.com/training/data-storage/shared/documents-files

### Quick Revision

**Key idea:** Do not assume the URI maps to a filesystem path. Open an `InputStream` through `ContentResolver` and stream the bytes into the network request.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
