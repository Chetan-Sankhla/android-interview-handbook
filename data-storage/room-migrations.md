# Room Migrations

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is a Room database migration?

**Difficulty:** 🟢 Basic

### Answer

A migration transforms an existing on-device database schema from one version to another while preserving data where required.

This matters because users do not all upgrade from the same application version. A device may contain an older schema that must be upgraded to the version shipped by the new app.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why do production apps need migrations?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

2. **What happens when a path is missing?** → [Open the related question](room-migrations.md#what-happens-if-a-migration-path-is-missing)

3. **How do you test migrations?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

### Common Mistake

Testing only a fresh database and never an upgrade from an older schema.



### Quick Revision

**Key idea:** A migration transforms an existing on-device database schema from one version to another while preserving data where required. This matters because users do not all upgrade from the same application version.

### Interview Insight

A complete answer covers the old and new representations, existing-user compatibility, migration testing, and rollout/data-loss safety.
## Q2. What are automatic and manual Room migrations?

**Difficulty:** 🟡 Intermediate

### Answer

Room supports automated migrations for many straightforward schema changes. For ambiguous or complex changes, such as certain table/column renames or data transformations, you may need additional migration specifications or a manual `Migration`.

Manual migrations give you direct control over SQL/data transformation logic.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When does AutoMigration need a spec?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

2. **When is manual migration required?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

3. **Can automatic and manual migrations coexist?** → [Open the related question](room-migrations.md#what-are-automatic-and-manual-room-migrations)

### Common Mistake

Choosing automatic migration just to reduce code when the data transformation is actually complex.



### Quick Revision

**Key idea:** Room supports automated migrations for many straightforward schema changes. For ambiguous or complex changes, such as certain table/column renames or data transformations, you may need additional migration specifications or a manual `Migration`.

### Interview Insight

A complete answer covers the old and new representations, existing-user compatibility, migration testing, and rollout/data-loss safety.
## Q3. What happens if a migration path is missing?

**Difficulty:** 🟡 Intermediate

### Answer

If Room cannot find a valid migration path from the installed database version to the current version, opening the database can fail with an `IllegalStateException`.

For disposable data, destructive fallback may be acceptable. For user-critical data, missing paths should normally be treated as a release-blocking defect rather than hidden with destructive recreation.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What does fallbackToDestructiveMigration do?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

2. **When is it acceptable?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How can fallback be restricted?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Adding fallbackToDestructiveMigration to silence a migration crash in a user-data database.



### Quick Revision

**Key idea:** If Room cannot find a valid migration path from the installed database version to the current version, opening the database can fail with an `IllegalStateException`. For disposable data, destructive fallback may be acceptable.

### Interview Insight

A complete answer covers the old and new representations, existing-user compatibility, migration testing, and rollout/data-loss safety.
## Q4. How do you add a non-null column to an existing table?

**Difficulty:** 🟡 Intermediate

### Answer

Existing rows need a valid value for the new non-null column. The migration can supply a safe default, populate values based on existing data, and then enforce the constraint.

The correct value is a product/data decision, not merely a SQL syntax decision. A fake default can silently corrupt business meaning.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What if no sensible default exists?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you backfill millions of rows?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Should the column be nullable temporarily?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using an arbitrary default such as an empty string without checking business semantics.



### Quick Revision

**Key idea:** Existing rows need a valid value for the new non-null column. The migration can supply a safe default, populate values based on existing data, and then enforce the constraint.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. How would you rename a column without losing data?

**Difficulty:** 🟡 Intermediate

### Answer

Use a supported rename migration strategy, such as Room's migration specification for a straightforward rename or an explicit manual migration when the transformation is more complex.

The important requirement is to preserve the existing values and verify the resulting schema and indexes.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How does Room know it is a rename rather than delete + add?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **When is a manual migration better?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

3. **How do you test it?**

<details>
<summary>Reveal sample answer</summary>

Test the smallest meaningful contract directly, then add integration or UI tests for Android framework behavior and cross-component interaction. Include error, empty, cancellation, concurrency, or lifecycle cases when they can change the result.

</details>

### Common Mistake

Deleting the old column and adding a new one without copying data.



### Quick Revision

**Key idea:** Use a supported rename migration strategy, such as Room's migration specification for a straightforward rename or an explicit manual migration when the transformation is more complex. The important requirement is to preserve the existing values and verify the resulting schema and indexes.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How would you split one table into two tables?

**Difficulty:** 🔴 Advanced

### Answer

This is a good example of a manual migration. Create the new tables, transform/copy the existing rows into them, validate the mapping, then remove or deprecate the old structure as appropriate.

For large datasets, pay attention to migration duration, transaction size, device resources, and rollback strategy.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do you preserve foreign keys?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you handle malformed legacy data?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **What if the migration takes too long?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

### Common Mistake

Assuming a table split is equivalent to adding a couple of columns.



### Quick Revision

**Key idea:** This is a good example of a manual migration. Create the new tables, transform/copy the existing rows into them, validate the mapping, then remove or deprecate the old structure as appropriate.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How should Room migrations be tested?

**Difficulty:** 🔴 Advanced

### Answer

Export Room schemas and keep them in version control. Use migration tests to create an older schema, apply the migration, and validate the resulting schema and important data.

Test both individual migrations and the complete migration path from older supported versions. Fresh-install tests do not cover upgrade behavior.

### Example

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Long,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User WHERE id = :id")
    fun observe(id: Long): Flow<User?>
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why export schemas?**

<details>
<summary>Reveal sample answer</summary>

A schema makes the stored shape explicit, gives stronger type guarantees, and forces compatibility decisions to be visible as the model evolves.

</details>

2. **What should migration tests validate?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

3. **Should you test every historical version?**

<details>
<summary>Reveal sample answer</summary>

Test the smallest meaningful contract directly, then add integration or UI tests for Android framework behavior and cross-component interaction. Include error, empty, cancellation, concurrency, or lifecycle cases when they can change the result.

</details>

### Common Mistake

Only testing the latest schema or only checking that the database opens.



### Quick Revision

**Key idea:** Export Room schemas and keep them in version control. Use migration tests to create an older schema, apply the migration, and validate the resulting schema and important data.

### Interview Insight

A complete answer covers the old and new representations, existing-user compatibility, migration testing, and rollout/data-loss safety.
## Q8. When is destructive migration acceptable?

**Difficulty:** 🔴 Advanced

### Answer

Destructive migration deletes and recreates database data when no migration path exists. It can be acceptable for a rebuildable cache or other explicitly disposable data.

It is dangerous for user-generated or business-critical data because missing migration logic can become permanent data loss. If used, constrain it to clearly acceptable versions/scenarios where possible.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/training/data-storage/room/migrating-db-versions
- https://developer.android.com/training/data-storage/room/prepopulate

### Quick Revision

**Key idea:** Destructive migration deletes and recreates database data when no migration path exists. It can be acceptable for a rebuildable cache or other explicitly disposable data.

### Interview Insight

A complete answer covers the old and new representations, existing-user compatibility, migration testing, and rollout/data-loss safety.
