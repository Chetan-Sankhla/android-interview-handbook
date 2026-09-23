# MediaStore & Storage Access Framework

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is MediaStore?

**Difficulty:** 🟢 Basic

### Answer

MediaStore is Android's indexed interface to shared media collections such as images, video, and audio. It is appropriate when media is intended to be user-visible or shared outside the app.

Media created in shared media storage can remain on the device after the app is uninstalled, which is a key ownership difference from app-specific storage.

### Common Follow-ups

- When should you use app-specific external storage instead?
- What is RELATIVE_PATH?
- What is IS_PENDING?

### Senior/Lead Perspective

The key design question is whether the media has value independently of the app. If yes, shared media storage is often appropriate.

### Common Mistake

Using MediaStore for private cache images that have no value outside the app.

## Q2. How do you save an image to MediaStore?

**Difficulty:** 🟡 Intermediate

### Answer

Insert metadata into the appropriate MediaStore collection, including display name, MIME type, and where appropriate a relative path. The returned content URI is then used to write the bytes through `ContentResolver`.

For larger writes, `IS_PENDING` can be used so the item is not treated as finalized content until writing is complete.

### Common Follow-ups

- Why use a content URI?
- What happens if writing fails?
- How do you handle cleanup?

### Senior/Lead Perspective

Treat the URI as the stable handle to the media item. Keep the write lifecycle explicit: create, write, finalize, and recover from failures.

### Common Mistake

Writing to a filesystem path obtained from a MediaStore query.

## Q3. What is IS_PENDING in MediaStore?

**Difficulty:** 🟡 Intermediate

### Answer

`IS_PENDING` allows an app to mark newly inserted media as still being written. While pending, the item is not treated as completed shared media.

After the file is successfully written, the app clears the pending state. This prevents partially written files from being exposed as finished media.

### Common Follow-ups

- What if the process dies?
- How do you clean abandoned pending items?
- Why is this better than writing directly to a public path?

### Senior/Lead Perspective

For production media pipelines, pending-state cleanup should be considered part of recovery design, especially for large files and process death.

### Common Mistake

Treating insertion as equivalent to completed media publication.

## Q4. What is the Storage Access Framework?

**Difficulty:** 🟢 Basic

### Answer

SAF lets the user select documents or directories through a system picker. The app receives a URI representing the selected location and can access it according to the granted permissions.

It works with document providers, including external storage and cloud-backed providers, without requiring broad filesystem access for the selected content.

### Common Follow-ups

- What are ACTION_OPEN_DOCUMENT, ACTION_CREATE_DOCUMENT, and ACTION_OPEN_DOCUMENT_TREE?
- How do you persist URI access?
- Can a URI have no local path?

### Senior/Lead Perspective

SAF is particularly valuable when the user should decide where a document lives. It makes the ownership decision explicit and avoids assuming a particular storage volume.

### Common Mistake

Treating SAF as simply another file-path picker.

## Q5. What is the difference between ACTION_OPEN_DOCUMENT and ACTION_GET_CONTENT?

**Difficulty:** 🟡 Intermediate

### Answer

`ACTION_OPEN_DOCUMENT` is part of the Storage Access Framework and is designed around document-provider access, including persistable URI permissions where supported.

`ACTION_GET_CONTENT` is generally used when the app wants content for immediate use and does not need to take persistent ownership/access through the document-provider model.

### Common Follow-ups

- When would you need a persistable URI permission?
- Why does this matter for cloud providers?
- What happens after process death?

### Senior/Lead Perspective

Choose the intent based on the lifecycle of access. If the application needs to reopen the same user-selected document later, document-provider semantics are usually important.

### Common Mistake

Using GET_CONTENT when the app actually needs durable access to the selected document.

## Q6. What is ACTION_OPEN_DOCUMENT_TREE?

**Difficulty:** 🟡 Intermediate

### Answer

It lets the user select a directory and grants the app access to that directory tree, subject to platform restrictions and provider capabilities.

It is useful for workflows such as choosing an export folder or working with a user-selected collection of documents.

### Common Follow-ups

- How do you persist access?
- Can every directory be selected?
- When is direct filesystem access unnecessary?

### Senior/Lead Perspective

A tree permission is powerful, so the UI should explain why the app needs access and the data it will operate on.

### Common Mistake

Requesting broad storage permission when a single user-selected directory is enough.

## Q7. How do you persist a SAF URI permission?

**Difficulty:** 🟡 Intermediate

### Answer

When the provider grants a persistable permission, the app can call `takePersistableUriPermission()` with the appropriate read/write flags and persist the URI string in its own state.

On later launches, the app can reconstruct the URI and access the document again as long as the persisted grant remains valid.

### Common Follow-ups

- What if the user revokes access?
- Where should the URI string be stored?
- How do you recover gracefully?

### Senior/Lead Perspective

Treat persisted URI access as revocable. Every real access should be prepared for `SecurityException` or a provider that no longer exposes the document.

### Common Mistake

Assuming a persisted URI is guaranteed to remain accessible forever.

## Q8. How would you upload a SAF-selected file?

**Difficulty:** 🟡 Intermediate

### Answer

Do not assume the URI maps to a filesystem path. Open an `InputStream` through `ContentResolver` and stream the bytes into the network request.

For large files, avoid reading the entire document into memory. If the provider supports efficient seeking or size metadata, use those capabilities where the networking stack benefits from them.

### Common Follow-ups

- How do you determine MIME type?
- How do you show upload progress?
- What if the provider is cloud-backed?

### Senior/Lead Perspective

A storage abstraction that accepts streams/URIs rather than local paths will support more providers and reduce coupling to device filesystem details.

### Common Mistake

Calling `File(uri.path!!)` and assuming the resulting path points to the user's document.

### References

- https://developer.android.com/training/data-storage/shared/media
- https://developer.android.com/training/data-storage/shared/documents-files