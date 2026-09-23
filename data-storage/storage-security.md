# Storage Security & Privacy

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. Is internal storage automatically secure?

**Difficulty:** 🟡 Intermediate

### Answer

Internal app storage is protected by Android's application sandbox, which prevents ordinary applications from directly accessing another app's private files. That is an important isolation boundary.

It is not equivalent to end-to-end encryption or protection against a compromised device. Sensitive data may still require cryptographic protection, secure key management, careful backup configuration, and limited exposure.

### Common Follow-ups

- What does Android Keystore solve?
- What about backups?
- What about rooted/compromised devices?

### Senior/Lead Perspective

Security decisions should be based on the threat model. Sandbox isolation, encryption at rest, and key protection address different threats.

### Common Mistake

Calling internal storage 'encrypted secure storage' without qualification.

## Q2. How would you store a refresh token securely?

**Difficulty:** 🔴 Advanced

### Answer

Minimize token lifetime and exposure, store it in app-private state, and use Android Keystore-backed cryptographic protection when encryption of the persisted token is required by the threat model.

Do not store raw passwords. Also consider backup behavior, logging, screenshots, memory exposure, and what happens when the user logs out.

### Common Follow-ups

- Should the token be included in backups?
- Where should encryption keys live?
- What happens on logout?

### Senior/Lead Perspective

The secure-storage design is broader than one API. A senior engineer should define the complete credential lifecycle: issuance, persistence, refresh, rotation, revocation, logout, and backup/restore behavior.

### Common Mistake

Encrypting a token but logging it or including it in an unintended backup.

## Q3. What is Android Keystore?

**Difficulty:** 🟡 Intermediate

### Answer

Android Keystore provides a mechanism for generating and protecting cryptographic keys with controlled access. Depending on device capabilities and configuration, keys can receive hardware-backed protection.

The key point is that the application can use a protected key without treating the key material like an ordinary string/file that it can freely copy around.

### Common Follow-ups

- Can Keystore store arbitrary application data?
- What is hardware-backed security?
- How would you combine Keystore with AES-GCM?

### Senior/Lead Perspective

Use Keystore for key protection, not as a general database. Store encrypted application data separately and keep the encryption design explicit.

### Common Mistake

Thinking Keystore itself is a general-purpose secure database.

## Q4. How would you encrypt a sensitive local file?

**Difficulty:** 🔴 Advanced

### Answer

Use authenticated encryption such as AES-GCM with a key protected by Android Keystore. The file format should include whatever non-secret metadata is required to decrypt and verify the content, such as a version and IV/nonce.

Plan for key rotation, corruption, logout, backup/restore, and migration before shipping the format.

### Common Follow-ups

- Where does the key live?
- What is authenticated encryption?
- How do you rotate keys?

### Senior/Lead Perspective

A production encryption format is an application protocol. Version it so the team can change algorithms or key-handling strategy later without losing data.

### Common Mistake

Encrypting bytes with a hard-coded key or reusing a fixed IV/nonce.

## Q5. Should sensitive DataStore data be backed up?

**Difficulty:** 🔴 Advanced

### Answer

Not automatically. DataStore files can participate in Android backup and device-to-device transfer depending on configuration. Sensitive local state may need explicit exclusion from backup rules.

Separate sensitive and non-sensitive state into different DataStore files when necessary so backup policy can be applied precisely.

### Common Follow-ups

- How do you configure backup rules?
- What should happen after device restore?
- Should refresh tokens be restored?

### Senior/Lead Perspective

Backup is part of the threat model. A secret that is safe only because it is device-local can become a different security problem when copied to another device or cloud backup.

### Common Mistake

Assuming app-private files are automatically excluded from every backup mechanism.

## Q6. What storage security mistakes commonly appear in Android apps?

**Difficulty:** 🔴 Advanced

### Answer

Common mistakes include storing secrets in plaintext, logging credentials, putting sensitive data in filenames, leaving temporary copies behind, exposing files through unsafe sharing, ignoring backup rules, and treating broad storage permissions as harmless.

Security also includes lifecycle: logout should invalidate or remove credentials, and temporary files should not outlive their purpose unnecessarily.

### Common Follow-ups

- How can logs leak storage data?
- How can FileProvider help?
- What should be excluded from backups?

### Senior/Lead Perspective

A security review should trace sensitive data end-to-end: acquisition → memory → persistence → backup → sharing → deletion.

### Common Mistake

Reviewing only the database/file encryption while ignoring logs, backups, and exported files.

## Q7. How would you securely share a private file with another app?

**Difficulty:** 🟡 Intermediate

### Answer

Expose the file through a controlled content URI, typically using `FileProvider`, and grant the receiving app only the required temporary read/write permission.

Do not expose a raw filesystem path or make a private directory broadly accessible.

### Common Follow-ups

- Why is a content URI safer?
- How do you revoke access?
- How do you restrict paths?

### Senior/Lead Perspective

Sharing should be explicit and least-privilege: identify the recipient, grant only the needed operation, and make the grant temporary where possible.

### Common Mistake

Sharing `file://` URIs or raw private filesystem paths.

## Q8. How should logout affect local storage?

**Difficulty:** 🟡 Intermediate

### Answer

Define which data is account-specific and must be removed or invalidated, which cached content can remain safely, and which credentials must be revoked or deleted.

If multiple accounts are supported, namespace account-specific database/cache entries so logout does not accidentally expose another account's data.

### Common Follow-ups

- What about offline data?
- What about refresh tokens?
- How do you handle multiple accounts?

### Senior/Lead Perspective

Logout is a data-lifecycle event. A senior design should specify it alongside authentication rather than treating it as a UI button that clears one preference.

### Common Mistake

Clearing one token while leaving account-specific files, cache, or database rows accessible.

### References

- https://developer.android.com/privacy-and-security/security-best-practices
- https://developer.android.com/privacy-and-security/keystore
- https://developer.android.com/identity/data/autobackup