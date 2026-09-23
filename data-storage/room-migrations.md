# Room Migrations

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is a Room database migration?

**Difficulty:** 🟢 Basic

### Answer

A migration transforms an existing on-device database schema from one version to another while preserving data where required.

This matters because users do not all upgrade from the same application version. A device may contain an older schema that must be upgraded to the version shipped by the new app.

### Common Follow-ups

- Why do production apps need migrations?
- What happens when a path is missing?
- How do you test migrations?

### Senior/Lead Perspective

Treat schema history as a compatibility contract. A migration is not just a code change; it is code that must work against real databases created by older app versions.

### Common Mistake

Testing only a fresh database and never an upgrade from an older schema.

## Q2. What are automatic and manual Room migrations?

**Difficulty:** 🟡 Intermediate

### Answer

Room supports automated migrations for many straightforward schema changes. For ambiguous or complex changes, such as certain table/column renames or data transformations, you may need additional migration specifications or a manual `Migration`.

Manual migrations give you direct control over SQL/data transformation logic.

### Common Follow-ups

- When does AutoMigration need a spec?
- When is manual migration required?
- Can automatic and manual migrations coexist?

### Senior/Lead Perspective

Use automatic migration when it accurately expresses the schema change; use manual migration when data transformation or business semantics require explicit logic.

### Common Mistake

Choosing automatic migration just to reduce code when the data transformation is actually complex.

## Q3. What happens if a migration path is missing?

**Difficulty:** 🟡 Intermediate

### Answer

If Room cannot find a valid migration path from the installed database version to the current version, opening the database can fail with an `IllegalStateException`.

For disposable data, destructive fallback may be acceptable. For user-critical data, missing paths should normally be treated as a release-blocking defect rather than hidden with destructive recreation.

### Common Follow-ups

- What does fallbackToDestructiveMigration do?
- When is it acceptable?
- How can fallback be restricted?

### Senior/Lead Perspective

A senior release process should test migration paths from supported historical versions before rollout and monitor migration-related crashes during staged deployment.

### Common Mistake

Adding fallbackToDestructiveMigration to silence a migration crash in a user-data database.

## Q4. How do you add a non-null column to an existing table?

**Difficulty:** 🟡 Intermediate

### Answer

Existing rows need a valid value for the new non-null column. The migration can supply a safe default, populate values based on existing data, and then enforce the constraint.

The correct value is a product/data decision, not merely a SQL syntax decision. A fake default can silently corrupt business meaning.

### Common Follow-ups

- What if no sensible default exists?
- How do you backfill millions of rows?
- Should the column be nullable temporarily?

### Senior/Lead Perspective

For important data, separate schema evolution from data backfill when necessary and make the transition observable and recoverable.

### Common Mistake

Using an arbitrary default such as an empty string without checking business semantics.

## Q5. How would you rename a column without losing data?

**Difficulty:** 🟡 Intermediate

### Answer

Use a supported rename migration strategy, such as Room's migration specification for a straightforward rename or an explicit manual migration when the transformation is more complex.

The important requirement is to preserve the existing values and verify the resulting schema and indexes.

### Common Follow-ups

- How does Room know it is a rename rather than delete + add?
- When is a manual migration better?
- How do you test it?

### Senior/Lead Perspective

Schema-diff ambiguity is exactly why migration tooling needs explicit information for some renames. Never assume the database can infer semantic intent from two schemas.

### Common Mistake

Deleting the old column and adding a new one without copying data.

## Q6. How would you split one table into two tables?

**Difficulty:** 🔴 Advanced

### Answer

This is a good example of a manual migration. Create the new tables, transform/copy the existing rows into them, validate the mapping, then remove or deprecate the old structure as appropriate.

For large datasets, pay attention to migration duration, transaction size, device resources, and rollback strategy.

### Common Follow-ups

- How do you preserve foreign keys?
- How do you handle malformed legacy data?
- What if the migration takes too long?

### Senior/Lead Perspective

Large schema transformations should be treated like production data migrations: test representative datasets, instrument failures, and use staged rollout.

### Common Mistake

Assuming a table split is equivalent to adding a couple of columns.

## Q7. How should Room migrations be tested?

**Difficulty:** 🔴 Advanced

### Answer

Export Room schemas and keep them in version control. Use migration tests to create an older schema, apply the migration, and validate the resulting schema and important data.

Test both individual migrations and the complete migration path from older supported versions. Fresh-install tests do not cover upgrade behavior.

### Common Follow-ups

- Why export schemas?
- What should migration tests validate?
- Should you test every historical version?

### Senior/Lead Perspective

The exact starting versions depend on your application's supported upgrade paths. A lead should establish a policy for how far back migration compatibility must be guaranteed.

### Common Mistake

Only testing the latest schema or only checking that the database opens.

## Q8. When is destructive migration acceptable?

**Difficulty:** 🔴 Advanced

### Answer

Destructive migration deletes and recreates database data when no migration path exists. It can be acceptable for a rebuildable cache or other explicitly disposable data.

It is dangerous for user-generated or business-critical data because missing migration logic can become permanent data loss. If used, constrain it to clearly acceptable versions/scenarios where possible.

### Common Follow-ups

- What alternatives exist?
- How would you use fallbackToDestructiveMigrationFrom?
- How would you monitor it?

### Senior/Lead Perspective

The architectural question is whether the database is a source of truth for user data or merely a cache. That classification should drive the fallback policy.

### Common Mistake

Using destructive fallback as a generic safety net for all Room databases.

### References

- https://developer.android.com/training/data-storage/room/migrating-db-versions
- https://developer.android.com/training/data-storage/room/prepopulate