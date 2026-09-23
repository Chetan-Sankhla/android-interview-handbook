# Scoped Storage

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is Scoped Storage?

**Difficulty:** 🟢 Basic

### Answer

Scoped Storage is Android's modern model for accessing shared external storage. Instead of allowing apps broad arbitrary filesystem access by default, Android encourages purpose-specific access through app-specific directories, MediaStore, SAF, and other APIs.

The goal is to reduce unnecessary access to unrelated user files and reduce storage clutter.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When did scoped storage become the default?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What changed in Android 11?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What APIs replace arbitrary paths?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Describing scoped storage as 'Android removed external storage'—shared storage still exists.



### Quick Revision

**Key idea:** Scoped Storage is Android's modern model for accessing shared external storage. Instead of allowing apps broad arbitrary filesystem access by default, Android encourages purpose-specific access through app-specific directories, MediaStore, SAF, and other APIs.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What changed with Scoped Storage on Android 10 and 11?

**Difficulty:** 🟡 Intermediate

### Answer

Android 10 introduced scoped-storage behavior for apps targeting API 29, with transitional compatibility behavior. For apps targeting Android 11/API 30 and higher, scoped storage became enforced and the old broad-storage opt-out was no longer available.

The exact behavior also depends on target SDK and legacy-device compatibility, so migration code should be tested across supported Android versions.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What was requestLegacyExternalStorage?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Does it still bypass storage restrictions for target 30+?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How should old paths be migrated?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming requestLegacyExternalStorage remains a permanent solution for modern target SDKs.



### Quick Revision

**Key idea:** Android 10 introduced scoped-storage behavior for apps targeting API 29, with transitional compatibility behavior. For apps targeting Android 11/API 30 and higher, scoped storage became enforced and the old broad-storage opt-out was no longer available.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is requestLegacyExternalStorage?

**Difficulty:** 🟡 Intermediate

### Answer

It was a compatibility mechanism for apps targeting Android 10 that allowed them to temporarily opt out of scoped storage.

It is not a strategy for modern target SDKs. Applications should migrate their storage model to purpose-specific APIs instead of building new functionality around legacy broad filesystem access.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why was it transitional?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What should replace it?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you migrate an old app?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Building new features around the legacy storage mode.



### Quick Revision

**Key idea:** It was a compatibility mechanism for apps targeting Android 10 that allowed them to temporarily opt out of scoped storage. It is not a strategy for modern target SDKs.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. What is MANAGE_EXTERNAL_STORAGE?

**Difficulty:** 🟡 Intermediate

### Answer

`MANAGE_EXTERNAL_STORAGE` grants broad access to shared storage for apps whose core functionality genuinely requires it. Android documentation gives examples such as file managers, backup/restore, antivirus, and certain document-management apps.

It is not a general replacement for MediaStore or SAF, and Google Play policy places additional requirements on apps requesting broad access.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When is it justified?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What can it access?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Why should most apps avoid it?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Requesting all-files access because it makes implementation easier.



### Quick Revision

**Key idea:** `MANAGE_EXTERNAL_STORAGE` grants broad access to shared storage for apps whose core functionality genuinely requires it. Android documentation gives examples such as file managers, backup/restore, antivirus, and certain document-management apps.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How would you migrate an old app that writes to /sdcard/MyApp/?

**Difficulty:** 🔴 Advanced

### Answer

First inventory the files and classify them as cache, app-private data, user documents, or shared media. Then map each category to app-specific storage, MediaStore, or SAF.

Make migration idempotent: record progress, handle process death, verify copied data, and only delete legacy files after the new copy is confirmed. Test upgrades from several historical app versions.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What if files are user-visible?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you handle partial migration?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

3. **How do you prevent duplicate files?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Moving every old path into the app's private directory without considering user ownership.



### Quick Revision

**Key idea:** First inventory the files and classify them as cache, app-private data, user documents, or shared media. Then map each category to app-specific storage, MediaStore, or SAF.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. Can an app directly access another app's files on external storage?

**Difficulty:** 🟡 Intermediate

### Answer

Scoped storage prevents ordinary applications from arbitrarily accessing other apps' app-specific directories. Shared storage should instead be accessed through purpose-specific APIs and user-granted/document-provider mechanisms where applicable.

Broad file access does not simply turn another app's private app-specific directory into a normal public folder.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What does MANAGE_EXTERNAL_STORAGE actually provide?** → [Open the related question](scoped-storage.md#what-is-manageexternalstorage)

2. **How do you share a file between apps?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What is FileProvider?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Passing a raw private filesystem path to another application.



### Quick Revision

**Key idea:** Scoped storage prevents ordinary applications from arbitrarily accessing other apps' app-specific directories. Shared storage should instead be accessed through purpose-specific APIs and user-granted/document-provider mechanisms where applicable.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How do you decide between MediaStore and SAF?

**Difficulty:** 🟡 Intermediate

### Answer

Use **MediaStore** when the content belongs in Android's shared media collections and should behave like user media.

Use **SAF** when the user should choose a document or directory, especially for non-media documents or a user-selected destination.

For example, a camera app saving a photo to the user's gallery naturally fits MediaStore; an editor asking the user where to save a PDF fits ACTION_CREATE_DOCUMENT.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What about Photo Picker?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can SAF access cloud providers?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Which survives uninstall?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using SAF for every image just because it can select files.



### Quick Revision

**Key idea:** Use **MediaStore** when the content belongs in Android's shared media collections and should behave like user media. Use **SAF** when the user should choose a document or directory, especially for non-media documents or a user-selected destination.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. Why is broad filesystem access a poor default architecture?

**Difficulty:** 🔴 Advanced

### Answer

Broad access creates unnecessary privacy exposure, complicates permission handling, increases compatibility risk across Android versions, and couples the app to filesystem assumptions.

Purpose-specific APIs usually produce clearer ownership boundaries and more predictable behavior. They also make it easier to explain why the app needs a particular kind of storage access.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/training/data-storage
- https://developer.android.com/training/data-storage/manage-all-files

### Quick Revision

**Key idea:** Broad access creates unnecessary privacy exposure, complicates permission handling, increases compatibility risk across Android versions, and couples the app to filesystem assumptions. Purpose-specific APIs usually produce clearer ownership boundaries and more predictable behavior.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
