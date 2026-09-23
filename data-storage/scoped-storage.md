# Scoped Storage

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is Scoped Storage?

**Difficulty:** 🟢 Basic

### Answer

Scoped Storage is Android's modern model for accessing shared external storage. Instead of allowing apps broad arbitrary filesystem access by default, Android encourages purpose-specific access through app-specific directories, MediaStore, SAF, and other APIs.

The goal is to reduce unnecessary access to unrelated user files and reduce storage clutter.

### Common Follow-ups

- When did scoped storage become the default?
- What changed in Android 11?
- What APIs replace arbitrary paths?

### Senior/Lead Perspective

A senior answer should explain it as a data-ownership and privacy model, not merely as a permission change.

### Common Mistake

Describing scoped storage as 'Android removed external storage'—shared storage still exists.

## Q2. What changed with Scoped Storage on Android 10 and 11?

**Difficulty:** 🟡 Intermediate

### Answer

Android 10 introduced scoped-storage behavior for apps targeting API 29, with transitional compatibility behavior. For apps targeting Android 11/API 30 and higher, scoped storage became enforced and the old broad-storage opt-out was no longer available.

The exact behavior also depends on target SDK and legacy-device compatibility, so migration code should be tested across supported Android versions.

### Common Follow-ups

- What was requestLegacyExternalStorage?
- Does it still bypass storage restrictions for target 30+?
- How should old paths be migrated?

### Senior/Lead Perspective

A lead should treat storage behavior as a target-SDK compatibility concern and test upgrades, not just fresh installs.

### Common Mistake

Assuming requestLegacyExternalStorage remains a permanent solution for modern target SDKs.

## Q3. What is requestLegacyExternalStorage?

**Difficulty:** 🟡 Intermediate

### Answer

It was a compatibility mechanism for apps targeting Android 10 that allowed them to temporarily opt out of scoped storage.

It is not a strategy for modern target SDKs. Applications should migrate their storage model to purpose-specific APIs instead of building new functionality around legacy broad filesystem access.

### Common Follow-ups

- Why was it transitional?
- What should replace it?
- How do you migrate an old app?

### Senior/Lead Perspective

When modernizing a legacy app, inventory existing paths and classify each file by ownership and intended visibility before changing code.

### Common Mistake

Building new features around the legacy storage mode.

## Q4. What is MANAGE_EXTERNAL_STORAGE?

**Difficulty:** 🟡 Intermediate

### Answer

`MANAGE_EXTERNAL_STORAGE` grants broad access to shared storage for apps whose core functionality genuinely requires it. Android documentation gives examples such as file managers, backup/restore, antivirus, and certain document-management apps.

It is not a general replacement for MediaStore or SAF, and Google Play policy places additional requirements on apps requesting broad access.

### Common Follow-ups

- When is it justified?
- What can it access?
- Why should most apps avoid it?

### Senior/Lead Perspective

Start by proving that the core use case cannot be satisfied with user-selected SAF access, MediaStore, or app-specific storage. Broad access should be a last-resort architectural requirement.

### Common Mistake

Requesting all-files access because it makes implementation easier.

## Q5. How would you migrate an old app that writes to /sdcard/MyApp/?

**Difficulty:** 🔴 Advanced

### Answer

First inventory the files and classify them as cache, app-private data, user documents, or shared media. Then map each category to app-specific storage, MediaStore, or SAF.

Make migration idempotent: record progress, handle process death, verify copied data, and only delete legacy files after the new copy is confirmed. Test upgrades from several historical app versions.

### Common Follow-ups

- What if files are user-visible?
- How do you handle partial migration?
- How do you prevent duplicate files?

### Senior/Lead Perspective

Migration is both a storage and product decision. The team must define whether the old files are user-owned and what the user should see after upgrade.

### Common Mistake

Moving every old path into the app's private directory without considering user ownership.

## Q6. Can an app directly access another app's files on external storage?

**Difficulty:** 🟡 Intermediate

### Answer

Scoped storage prevents ordinary applications from arbitrarily accessing other apps' app-specific directories. Shared storage should instead be accessed through purpose-specific APIs and user-granted/document-provider mechanisms where applicable.

Broad file access does not simply turn another app's private app-specific directory into a normal public folder.

### Common Follow-ups

- What does MANAGE_EXTERNAL_STORAGE actually provide?
- How do you share a file between apps?
- What is FileProvider?

### Senior/Lead Perspective

Cross-app sharing should be explicit. If one app needs to hand another app a file, use a content URI/share contract rather than relying on shared filesystem paths.

### Common Mistake

Passing a raw private filesystem path to another application.

## Q7. How do you decide between MediaStore and SAF?

**Difficulty:** 🟡 Intermediate

### Answer

Use **MediaStore** when the content belongs in Android's shared media collections and should behave like user media.

Use **SAF** when the user should choose a document or directory, especially for non-media documents or a user-selected destination.

For example, a camera app saving a photo to the user's gallery naturally fits MediaStore; an editor asking the user where to save a PDF fits ACTION_CREATE_DOCUMENT.

### Common Follow-ups

- What about Photo Picker?
- Can SAF access cloud providers?
- Which survives uninstall?

### Senior/Lead Perspective

The choice is driven by who owns the destination and how much control the user should have over it.

### Common Mistake

Using SAF for every image just because it can select files.

## Q8. Why is broad filesystem access a poor default architecture?

**Difficulty:** 🔴 Advanced

### Answer

Broad access creates unnecessary privacy exposure, complicates permission handling, increases compatibility risk across Android versions, and couples the app to filesystem assumptions.

Purpose-specific APIs usually produce clearer ownership boundaries and more predictable behavior. They also make it easier to explain why the app needs a particular kind of storage access.

### Common Follow-ups

- How does this affect security reviews?
- How does it affect Play policy?
- How do you reduce permissions?

### Senior/Lead Perspective

A lead should challenge broad storage requirements during architecture review rather than accepting them as implementation convenience.

### Common Mistake

Treating broad access as harmless because the app only intends to read files.

### References

- https://developer.android.com/training/data-storage
- https://developer.android.com/training/data-storage/manage-all-files