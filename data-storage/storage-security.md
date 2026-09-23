# Storage Security & Privacy

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. Is internal storage automatically secure?

**Difficulty:** 🟡 Intermediate

### Answer

Internal app storage is protected by Android's application sandbox, which prevents ordinary applications from directly accessing another app's private files. That is an important isolation boundary.

It is not equivalent to end-to-end encryption or protection against a compromised device. Sensitive data may still require cryptographic protection, secure key management, careful backup configuration, and limited exposure.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What does Android Keystore solve?** → [Open the related question](storage-security.md#what-is-android-keystore)

2. **What about backups?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What about rooted/compromised devices?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Calling internal storage 'encrypted secure storage' without qualification.



### Quick Revision

**Key idea:** Internal app storage is protected by Android's application sandbox, which prevents ordinary applications from directly accessing another app's private files. That is an important isolation boundary.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. How would you store a refresh token securely?

**Difficulty:** 🔴 Advanced

### Answer

Minimize token lifetime and exposure, store it in app-private state, and use Android Keystore-backed cryptographic protection when encryption of the persisted token is required by the threat model.

Do not store raw passwords. Also consider backup behavior, logging, screenshots, memory exposure, and what happens when the user logs out.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Should the token be included in backups?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Where should encryption keys live?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What happens on logout?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Encrypting a token but logging it or including it in an unintended backup.



### Quick Revision

**Key idea:** Minimize token lifetime and exposure, store it in app-private state, and use Android Keystore-backed cryptographic protection when encryption of the persisted token is required by the threat model. Do not store raw passwords.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is Android Keystore?

**Difficulty:** 🟡 Intermediate

### Answer

Android Keystore provides a mechanism for generating and protecting cryptographic keys with controlled access. Depending on device capabilities and configuration, keys can receive hardware-backed protection.

The key point is that the application can use a protected key without treating the key material like an ordinary string/file that it can freely copy around.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Can Keystore store arbitrary application data?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What is hardware-backed security?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you combine Keystore with AES-GCM?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Thinking Keystore itself is a general-purpose secure database.



### Quick Revision

**Key idea:** Android Keystore provides a mechanism for generating and protecting cryptographic keys with controlled access. Depending on device capabilities and configuration, keys can receive hardware-backed protection.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How would you encrypt a sensitive local file?

**Difficulty:** 🔴 Advanced

### Answer

Use authenticated encryption such as AES-GCM with a key protected by Android Keystore. The file format should include whatever non-secret metadata is required to decrypt and verify the content, such as a version and IV/nonce.

Plan for key rotation, corruption, logout, backup/restore, and migration before shipping the format.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Where does the key live?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What is authenticated encryption?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you rotate keys?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Encrypting bytes with a hard-coded key or reusing a fixed IV/nonce.



### Quick Revision

**Key idea:** Use authenticated encryption such as AES-GCM with a key protected by Android Keystore. The file format should include whatever non-secret metadata is required to decrypt and verify the content, such as a version and IV/nonce.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. Should sensitive DataStore data be backed up?

**Difficulty:** 🔴 Advanced

### Answer

Not automatically. DataStore files can participate in Android backup and device-to-device transfer depending on configuration. Sensitive local state may need explicit exclusion from backup rules.

Separate sensitive and non-sensitive state into different DataStore files when necessary so backup policy can be applied precisely.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you configure backup rules?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What should happen after device restore?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Should refresh tokens be restored?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming app-private files are automatically excluded from every backup mechanism.



### Quick Revision

**Key idea:** Not automatically. DataStore files can participate in Android backup and device-to-device transfer depending on configuration.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. What storage security mistakes commonly appear in Android apps?

**Difficulty:** 🔴 Advanced

### Answer

Common mistakes include storing secrets in plaintext, logging credentials, putting sensitive data in filenames, leaving temporary copies behind, exposing files through unsafe sharing, ignoring backup rules, and treating broad storage permissions as harmless.

Security also includes lifecycle: logout should invalidate or remove credentials, and temporary files should not outlive their purpose unnecessarily.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How can logs leak storage data?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How can FileProvider help?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What should be excluded from backups?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Reviewing only the database/file encryption while ignoring logs, backups, and exported files.



### Quick Revision

**Key idea:** Common mistakes include storing secrets in plaintext, logging credentials, putting sensitive data in filenames, leaving temporary copies behind, exposing files through unsafe sharing, ignoring backup rules, and treating broad storage permissions as harmless. Security also includes lifecycle: logout should invalidate or remove credentials, and temporary files should not outlive their purpose unnecessarily.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How would you securely share a private file with another app?

**Difficulty:** 🟡 Intermediate

### Answer

Expose the file through a controlled content URI, typically using `FileProvider`, and grant the receiving app only the required temporary read/write permission.

Do not expose a raw filesystem path or make a private directory broadly accessible.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why is a content URI safer?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you revoke access?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you restrict paths?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Sharing `file://` URIs or raw private filesystem paths.



### Quick Revision

**Key idea:** Expose the file through a controlled content URI, typically using `FileProvider`, and grant the receiving app only the required temporary read/write permission. Do not expose a raw filesystem path or make a private directory broadly accessible.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How should logout affect local storage?

**Difficulty:** 🟡 Intermediate

### Answer

Define which data is account-specific and must be removed or invalidated, which cached content can remain safely, and which credentials must be revoked or deleted.

If multiple accounts are supported, namespace account-specific database/cache entries so logout does not accidentally expose another account's data.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/privacy-and-security/security-best-practices
- https://developer.android.com/privacy-and-security/keystore
- https://developer.android.com/identity/data/autobackup

### Quick Revision

**Key idea:** Define which data is account-specific and must be removed or invalidated, which cached content can remain safely, and which credentials must be revoked or deleted. If multiple accounts are supported, namespace account-specific database/cache entries so logout does not accidentally expose another account's data.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
