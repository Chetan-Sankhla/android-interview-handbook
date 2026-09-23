# Room Relations, Modeling & Performance

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. How do you model a one-to-many relationship in Room?

**Difficulty:** 🟡 Intermediate

### Answer

Use separate entities and a foreign key from the child table to the parent. For example, `User` can have many `Order` rows, each containing `userId`.

Room can represent the relationship in a query result using `@Relation`, but explicit SQL joins/projections are often preferable for performance-sensitive screens because they make the selected columns and query shape obvious.

### Common Follow-ups

- What is @Relation?
- When should you use a JOIN instead?
- Should foreign keys be indexed?

### Senior/Lead Perspective

A senior developer chooses the representation based on query needs. Convenience relationship APIs are useful, but they should not replace understanding the SQL being executed.

### Common Mistake

Treating @Relation as a magic replacement for query design.

## Q2. How do you model many-to-many relationships?

**Difficulty:** 🟡 Intermediate

### Answer

Use a junction table. For example, `Student`, `Course`, and `StudentCourse(studentId, courseId)` represent students enrolled in courses.

The junction table normally has a composite primary key or unique constraint to prevent duplicate associations and foreign keys to preserve referential integrity.

### Common Follow-ups

- Why not store IDs as a comma-separated string?
- What indexes are needed?
- How would you query both directions?

### Senior/Lead Perspective

Normalized relationships make querying, updates, constraints, and indexing predictable. Serialized ID lists become difficult to query and validate as the dataset grows.

### Common Mistake

Using a JSON/list column for a relationship that needs relational queries.

## Q3. What is the difference between @Embedded and @Relation?

**Difficulty:** 🟡 Intermediate

### Answer

`@Embedded` flattens fields from another object into the same result/table representation. It is useful for value-like groups of fields.

`@Relation` represents data associated with another entity/table. It is appropriate when the related data has its own table and identity.

### Common Follow-ups

- When should an embedded object become an Entity?
- Can embedded fields collide?
- How do relations affect query cost?

### Senior/Lead Perspective

Use `@Embedded` when the nested object is part of the same row's value. Use a separate entity when the data has independent lifecycle, identity, or query requirements.

### Common Mistake

Using @Embedded for data that actually requires independent persistence and relationships.

## Q4. How do you prevent N+1 queries with Room?

**Difficulty:** 🔴 Advanced

### Answer

N+1 occurs when the app loads a parent list and then executes another query for each parent. This can create hundreds or thousands of database operations.

Prefer a single well-designed JOIN/projection, a batched `IN (...)` query, or an appropriate relationship query. Measure the resulting SQL and indexes rather than assuming one pattern is always fastest.

### Common Follow-ups

- How would you batch child IDs?
- When is @Relation acceptable?
- How would you measure the problem?

### Senior/Lead Perspective

A senior answer should identify N+1 as an architectural/query-shape problem, not a dispatcher problem. Moving 1,001 queries to a background thread still leaves 1,001 queries.

### Common Mistake

Fixing N+1 by moving all queries to IO without reducing query count.

## Q5. What are indexes and when should you add them?

**Difficulty:** 🟡 Intermediate

### Answer

An index is an additional database structure that can speed up lookups, filtering, joins, and sometimes ordering on indexed columns.

Add indexes based on actual query patterns. Indexes are not free: they consume storage and add work to inserts/updates. A useful index should support a meaningful workload rather than simply indexing every column.

### Common Follow-ups

- Which columns would you index?
- What is a composite index?
- Can too many indexes hurt writes?

### Senior/Lead Perspective

Use query plans and production-like data to validate indexing decisions. For a large table, an index strategy should be reviewed alongside the most expensive queries.

### Common Mistake

Adding indexes to every column without considering write cost or query patterns.

## Q6. How would you model a product catalog with categories and variants?

**Difficulty:** 🔴 Advanced

### Answer

Separate product identity from variant-level attributes. A typical model could use `Product`, `Category`, `ProductCategory`, and `ProductVariant`, with stable server IDs and appropriate foreign keys/indexes.

If the UI needs a flattened listing, use a projection query rather than denormalizing everything into one huge entity.

### Common Follow-ups

- Where would price/stock belong?
- How would you support offline search?
- How would you page products?

### Senior/Lead Perspective

The senior-level concern is balancing normalized data with read patterns. Keep the source model correct, then optimize read projections and indexes for actual screens.

### Common Mistake

Creating a single enormous Product table containing every category-specific field.

## Q7. When would you use a projection instead of returning an Entity?

**Difficulty:** 🟡 Intermediate

### Answer

Use a projection when a screen needs only a subset of columns or a calculated/joined result. Returning only required fields reduces mapping work and can reduce I/O and memory usage.

For example, a product-list screen may need `id`, `name`, `thumbnailUri`, and `price`, but not the full product description or metadata.

### Common Follow-ups

- Can a projection return a DTO?
- How does this help performance?
- How does it affect schema changes?

### Senior/Lead Perspective

Projections are especially valuable for large tables and frequently refreshed UI lists. They also make query intent explicit.

### Common Mistake

Returning full entities everywhere and then mapping away most fields.

## Q8. Why does Room avoid object references between entities?

**Difficulty:** 🔴 Advanced

### Answer

Room explicitly avoids treating entities like an in-memory object graph with arbitrary references. Relational databases represent relationships through keys and tables, while object references can hide query cost and loading behavior.

You should explicitly query related data, which makes the database access pattern visible and controllable.

### Common Follow-ups

- How do foreign keys represent relationships?
- How does @Relation help?
- Why can automatic object graphs be dangerous?

### Senior/Lead Perspective

This is an important senior concept: persistence APIs should not make expensive I/O look like cheap property access. Explicit data access makes performance easier to reason about.

### Common Mistake

Expecting `order.user.name` to behave like an in-memory object graph without considering database queries.

### References

- https://developer.android.com/training/data-storage/room/relationships
- https://developer.android.com/training/data-storage/room/defining-data