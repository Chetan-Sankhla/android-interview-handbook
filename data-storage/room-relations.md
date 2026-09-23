# Room Relations, Modeling & Performance

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. How do you model a one-to-many relationship in Room?

**Difficulty:** 🟡 Intermediate

### Answer

Use separate entities and a foreign key from the child table to the parent. For example, `User` can have many `Order` rows, each containing `userId`.

Room can represent the relationship in a query result using `@Relation`, but explicit SQL joins/projections are often preferable for performance-sensitive screens because they make the selected columns and query shape obvious.

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

1. **What is @Relation?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **When should you use a JOIN instead?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Should foreign keys be indexed?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating @Relation as a magic replacement for query design.



### Quick Revision

**Key idea:** Use separate entities and a foreign key from the child table to the parent. For example, `User` can have many `Order` rows, each containing `userId`.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. How do you model many-to-many relationships?

**Difficulty:** 🟡 Intermediate

### Answer

Use a junction table. For example, `Student`, `Course`, and `StudentCourse(studentId, courseId)` represent students enrolled in courses.

The junction table normally has a composite primary key or unique constraint to prevent duplicate associations and foreign keys to preserve referential integrity.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Why not store IDs as a comma-separated string?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What indexes are needed?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you query both directions?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using a JSON/list column for a relationship that needs relational queries.



### Quick Revision

**Key idea:** Use a junction table. For example, `Student`, `Course`, and `StudentCourse(studentId, courseId)` represent students enrolled in courses.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. What is the difference between @Embedded and @Relation?

**Difficulty:** 🟡 Intermediate

### Answer

`@Embedded` flattens fields from another object into the same result/table representation. It is useful for value-like groups of fields.

`@Relation` represents data associated with another entity/table. It is appropriate when the related data has its own table and identity.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **When should an embedded object become an Entity?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can embedded fields collide?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do relations affect query cost?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Using @Embedded for data that actually requires independent persistence and relationships.



### Quick Revision

**Key idea:** `@Embedded` flattens fields from another object into the same result/table representation. It is useful for value-like groups of fields.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. How do you prevent N+1 queries with Room?

**Difficulty:** 🔴 Advanced

### Answer

N+1 occurs when the app loads a parent list and then executes another query for each parent. This can create hundreds or thousands of database operations.

Prefer a single well-designed JOIN/projection, a batched `IN (...)` query, or an appropriate relationship query. Measure the resulting SQL and indexes rather than assuming one pattern is always fastest.

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

1. **How would you batch child IDs?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **When is @Relation acceptable?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How would you measure the problem?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Fixing N+1 by moving all queries to IO without reducing query count.



### Quick Revision

**Key idea:** N+1 occurs when the app loads a parent list and then executes another query for each parent. This can create hundreds or thousands of database operations.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What are indexes and when should you add them?

**Difficulty:** 🟡 Intermediate

### Answer

An index is an additional database structure that can speed up lookups, filtering, joins, and sometimes ordering on indexed columns.

Add indexes based on actual query patterns. Indexes are not free: they consume storage and add work to inserts/updates. A useful index should support a meaningful workload rather than simply indexing every column.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Which columns would you index?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What is a composite index?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Can too many indexes hurt writes?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Adding indexes to every column without considering write cost or query patterns.



### Quick Revision

**Key idea:** An index is an additional database structure that can speed up lookups, filtering, joins, and sometimes ordering on indexed columns. Add indexes based on actual query patterns.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. How would you model a product catalog with categories and variants?

**Difficulty:** 🔴 Advanced

### Answer

Separate product identity from variant-level attributes. A typical model could use `Product`, `Category`, `ProductCategory`, and `ProductVariant`, with stable server IDs and appropriate foreign keys/indexes.

If the UI needs a flattened listing, use a projection query rather than denormalizing everything into one huge entity.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Where would price/stock belong?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How would you support offline search?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

3. **How would you page products?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Creating a single enormous Product table containing every category-specific field.



### Quick Revision

**Key idea:** Separate product identity from variant-level attributes. A typical model could use `Product`, `Category`, `ProductCategory`, and `ProductVariant`, with stable server IDs and appropriate foreign keys/indexes.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. When would you use a projection instead of returning an Entity?

**Difficulty:** 🟡 Intermediate

### Answer

Use a projection when a screen needs only a subset of columns or a calculated/joined result. Returning only required fields reduces mapping work and can reduce I/O and memory usage.

For example, a product-list screen may need `id`, `name`, `thumbnailUri`, and `price`, but not the full product description or metadata.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Can a projection return a DTO?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How does this help performance?**

<details>
<summary>Reveal sample answer</summary>

Measure the actual bottleneck first. Make the smallest change that addresses it and verify the result with profiling or a reproducible benchmark.

</details>

3. **How does it affect schema changes?**

<details>
<summary>Reveal sample answer</summary>

Define the old and new schemas, test representative existing data, and provide a valid path from every supported version. Treat destructive migration as an explicit data-loss decision rather than a default shortcut.

</details>

### Common Mistake

Returning full entities everywhere and then mapping away most fields.



### Quick Revision

**Key idea:** Use a projection when a screen needs only a subset of columns or a calculated/joined result. Returning only required fields reduces mapping work and can reduce I/O and memory usage.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. Why does Room avoid object references between entities?

**Difficulty:** 🔴 Advanced

### Answer

Room explicitly avoids treating entities like an in-memory object graph with arbitrary references. Relational databases represent relationships through keys and tables, while object references can hide query cost and loading behavior.

You should explicitly query related data, which makes the database access pattern visible and controllable.

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

### References

- https://developer.android.com/training/data-storage/room/relationships
- https://developer.android.com/training/data-storage/room/defining-data

### Quick Revision

**Key idea:** Room explicitly avoids treating entities like an in-memory object graph with arbitrary references. Relational databases represent relationships through keys and tables, while object references can hide query cost and loading behavior.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
