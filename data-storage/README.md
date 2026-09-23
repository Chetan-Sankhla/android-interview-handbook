# Android Data Storage

This section is a curated interview-preparation guide based primarily on current official Android documentation.

It is deliberately split by topic so developers can study one storage concept at a time rather than reading one very large question dump.

## Topics

| Topic | Focus |
|---|---|
| [Storage Fundamentals](./fundamentals.md) | Storage choices, ownership, lifecycle, files vs database |
| [SharedPreferences](./shared-preferences.md) | Legacy preferences, commit/apply, migration |
| [DataStore](./datastore.md) | Preferences/typed DataStore, transactions, corruption, multi-process |
| [Room](./room.md) | Entities, DAOs, Flow, Paging, query design |
| [Room Relations](./room-relations.md) | Relationships, indexes, projections, N+1, modeling |
| [Room Transactions](./room-transactions.md) | Atomicity, consistency, idempotency, transaction boundaries |
| [Room Migrations](./room-migrations.md) | Auto/manual migrations, testing, destructive fallback |
| [Scoped Storage](./scoped-storage.md) | Android 10+, Android 11+, legacy storage, all-files access |
| [MediaStore & SAF](./mediastore-and-saf.md) | Shared media, Photo Picker context, document providers, URIs |
| [Files & Caching](./file-storage-and-caching.md) | Downloads, cache design, crash-safe writes, metadata |
| [Storage Security](./storage-security.md) | Keystore, credentials, backup, sharing, logout |
| [Offline-First](./offline-first.md) | Local source of truth, sync, conflicts, outbox, scale |
| [System Design](./system-design.md) | advanced storage architecture scenarios |

## Study path

### Junior

Start with:

1. Storage Fundamentals
2. SharedPreferences
3. DataStore
4. Room
5. MediaStore & SAF

### Mid-level

Add:

1. Room Relations
2. Room Transactions
3. Files & Caching
4. Scoped Storage
5. Storage Security

### Senior

Focus on:

1. Room Migrations
2. Offline-First
3. Storage Security
4. Performance and data modeling
5. Failure/recovery scenarios

### Lead

Focus on:

1. System Design
2. Migration strategy
3. Data ownership and lifecycle
4. Security and backup
5. Observability and rollout
6. Cross-feature architecture

## Important principle

Do not memorize the API.

For every storage decision, be able to explain:

- **Why** this storage mechanism fits.
- **Why not** the alternatives.
- Ownership and lifecycle.
- Failure and recovery.
- Security and backup implications.
- Performance and scale.
- Migration strategy.
- Testing strategy.

## Source policy

Android behavior changes over time. This repository prefers current official Android documentation for platform/API facts and calls out version-specific behavior where it matters.

> **Practice tip:** Answer the question aloud before opening a follow-up. Use the code examples to connect the concept to real Android implementation.
