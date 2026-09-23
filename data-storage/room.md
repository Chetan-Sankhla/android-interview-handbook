# Room Fundamentals

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is Room?

**Difficulty:** 🟢 Basic

### Answer

Room is a Jetpack persistence library that provides an abstraction over SQLite. It models database tables as entities, database operations as DAOs, and the database itself through `RoomDatabase`.

Its benefits include structured database access, compile-time SQL validation, migration support, transaction APIs, and integration with Kotlin coroutines, Flow, and Paging.

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

1. **What are Entity, DAO, and RoomDatabase?**

<details>
<summary>Reveal sample answer</summary>

An **Entity** represents a table and its columns, a **DAO** defines the operations and SQL used to access that data, and `RoomDatabase` is the database holder that ties the entities and DAOs together. For example, `User` can be an `@Entity`, `UserDao` can expose `getUser(id)`, and an `AppDatabase` can expose `userDao()`. The important boundary is that DAOs own persistence access; business decisions should normally stay outside them.

</details>

2. **Why Room instead of SQLite APIs?**

<details>
<summary>Reveal sample answer</summary>

Room still uses SQLite underneath, but adds compile-time query validation, generated DAO implementations, migration support, and integration with Kotlin/Android APIs such as `Flow`. For example, an invalid column name in an `@Query` can be caught during compilation instead of failing only when that code path runs. The trade-off is that Room imposes its own abstractions and supported patterns, so raw SQLite APIs may still be appropriate for unusual low-level requirements.

</details>

3. **How does Room validate SQL?**

<details>
<summary>Reveal sample answer</summary>

Room's annotation processor/compiler knows the declared schema and analyzes DAO queries at build time. It checks things such as referenced tables/columns and whether the query result can be mapped to the declared return type. For example, `@Query("SELECT username FROM User") fun users(): List<User>` can fail compilation when the result does not match the expected mapping. This protects against many runtime SQL errors, but it does not guarantee that a query is efficient or logically correct for the product requirement.

</details>

4. **How does Room integrate with Flow?**

<details>
<summary>Reveal sample answer</summary>

A DAO can return a `Flow`, and Room observes the tables relevant to that query and emits updated results when those tables are invalidated. For example, `@Query("SELECT * FROM User") fun observeUsers(): Flow<List<User>>` can emit a new list after an insert or update affecting `User`. The important caveat is that this is database-driven invalidation, not a general-purpose event stream; consumers should still collect it with appropriate lifecycle handling.

</details>

### Common Mistake

Calling Room a completely different database engine from SQLite.



### Quick Revision

**Key idea:** Room is a Jetpack persistence library that provides an abstraction over SQLite. It models database tables as entities, database operations as DAOs, and the database itself through `RoomDatabase`.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q2. What are the main components of Room?

**Difficulty:** 🟢 Basic

### Answer

The core components are:

- **Entity**: maps application data to a table.
- **DAO**: defines database operations and queries.
- **RoomDatabase**: provides the database configuration and access to DAOs.

Room generates implementation code from these declarations at build time.

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

1. **Can a DAO be an interface?**

<details>
<summary>Reveal sample answer</summary>

Yes. DAO interfaces are a normal Room pattern: you declare methods with annotations such as `@Query`, `@Insert`, and `@Delete`, and Room generates the implementation. For example, `interface UserDao { @Query("SELECT * FROM User") fun users(): Flow<List<User>> }`. The benefit is that the application depends on a small persistence contract while Room supplies the implementation.

</details>

2. **What does @Database define?**

<details>
<summary>Reveal sample answer</summary>

`@Database` declares the Room database configuration, including the entity classes, database version, and usually the DAOs exposed by the database class. For example, `@Database(entities = [User::class], version = 1) abstract class AppDatabase : RoomDatabase() { abstract fun userDao(): UserDao }`. The version becomes important when the schema changes because Room uses it to determine whether a migration is required.

</details>

3. **How does Room generate implementations?**

<details>
<summary>Reveal sample answer</summary>

Room's compiler processes the annotations on entities, DAOs, and the database definition and generates the concrete database/DAO implementation during the build. You write the contract, such as an abstract `UserDao`, and Room generates the code that executes the corresponding SQLite operations. This moves a lot of boilerplate and validation to compile time, although the generated code still ultimately performs SQLite work at runtime.

</details>

### Common Mistake

Putting business logic and network orchestration directly into DAOs.



### Quick Revision

**Key idea:** The core components are: - **Entity**: maps application data to a table. - **DAO**: defines database operations and queries.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q3. How does Room verify SQL queries?

**Difficulty:** 🟡 Intermediate

### Answer

Room processes DAO definitions at compile time and checks queries against the known database schema. This can catch invalid table/column references and result-mapping problems before the application runs.

This is one of the practical advantages over constructing raw SQL dynamically at runtime.

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

1. **What happens if a column is renamed?**

<details>
<summary>Reveal sample answer</summary>

A column rename is a schema change, so changing the entity alone is not enough for an existing installed database. You need a migration that transforms the old schema into the new schema, and Room's migration tooling can require explicit handling when it cannot safely infer a rename. For example, renaming `name` to `displayName` should preserve existing values rather than creating a new empty column. The key risk is shipping an entity change without a valid migration path, which can cause upgrade failures or data loss if destructive fallback is used.

</details>

2. **What limitations remain?**

<details>
<summary>Reveal sample answer</summary>

Room's compile-time checks do not prove that a query is performant, that an index is appropriate, or that the returned data matches the product's business semantics. For example, `SELECT *` may compile successfully while becoming expensive as a table grows. You still need query-plan/performance testing and correct schema design; Room reduces a class of errors but does not replace database expertise.

</details>

3. **Can Room validate dynamically generated SQL?**

<details>
<summary>Reveal sample answer</summary>

Room's strongest query validation applies to SQL known at compile time in DAO annotations. If you construct SQL dynamically at runtime, Room cannot perform the same compile-time verification of the final statement. For example, building arbitrary table or WHERE clauses as strings moves validation to runtime. If dynamic querying is required, keep the dynamic surface narrow and validate inputs carefully, or use structured Room APIs where possible.

</details>

### Common Mistake

Assuming compile-time validation means every query is automatically performant.



### Quick Revision

**Key idea:** Room processes DAO definitions at compile time and checks queries against the known database schema. This can catch invalid table/column references and result-mapping problems before the application runs.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. Why shouldn't database operations run on the main thread?

**Difficulty:** 🟢 Basic

### Answer

Database operations can involve disk I/O, locking, query execution, and object mapping. Running them on the main thread can block rendering and contribute to jank or ANRs.

Use suspend DAO methods, Flow, or another asynchronous approach and keep expensive work off the UI thread.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Does Room automatically make every query asynchronous?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

2. **What is allowMainThreadQueries()?**

<details>
<summary>Reveal sample answer</summary>

No. Room prevents main-thread database access by default, but a DAO method's return type determines how the operation is exposed. A `suspend` function performs the operation as a suspendable call, while a `Flow` represents observable asynchronous data. For example, `suspend fun findUser(id: Long): User?` can be called from a coroutine, but a normal synchronous DAO method is still synchronous. You should not assume Room magically makes arbitrary synchronous work non-blocking.

</details>

3. **How would you find slow queries?**

<details>
<summary>Reveal sample answer</summary>

Measure the actual bottleneck first. Make the smallest change that addresses it and verify the result with profiling or a reproducible benchmark.

</details>

### Common Mistake

Thinking `Dispatchers.IO` alone fixes an inefficient query.



### Quick Revision

**Key idea:** Database operations can involve disk I/O, locking, query execution, and object mapping. Running them on the main thread can block rendering and contribute to jank or ANRs.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q5. What is an Entity?

**Difficulty:** 🟢 Basic

### Answer

An Entity is a class that Room maps to a database table. Its fields generally correspond to columns, and one or more fields identify the row through a primary key.

Entities describe persistence structure; they do not have to be the same objects used by the UI or domain layer.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Can an Entity have a composite primary key?**

<details>
<summary>Reveal sample answer</summary>

`allowMainThreadQueries()` explicitly permits Room database operations on the main thread. It can be useful for controlled tests or very small experiments, but it is generally a poor production choice because disk I/O can block UI rendering and cause jank or ANRs. If a production design seems to require it, the better question is why the database work cannot be moved behind suspend/coroutine or observable APIs.

</details>

2. **What are indexes?**

<details>
<summary>Reveal sample answer</summary>

Start by measuring rather than guessing: inspect slow user journeys, use Android/database profiling tools where appropriate, and examine SQLite query plans with `EXPLAIN QUERY PLAN` when investigating a query. For example, a query filtering by `email` may become much faster after adding an appropriate index, but an index also adds write/storage overhead. In production, correlate database timing with real traces or telemetry rather than optimizing every query preemptively.

</details>

3. **Should entities be exposed to UI?**

<details>
<summary>Reveal sample answer</summary>

Yes. Room supports multiple columns as a composite primary key using `primaryKeys` on `@Entity`. For example, `@Entity(primaryKeys = ["userId", "courseId"]) data class Enrollment(...)` uniquely identifies a user's enrollment in a course. Composite keys are useful when the combination represents identity, but they should be chosen deliberately because foreign keys, indexes, and queries may become more complex.

</details>

### Common Mistake

Using one Entity class as the network DTO, domain model, and UI model.



### Quick Revision

**Key idea:** An Entity is a class that Room maps to a database table. Its fields generally correspond to columns, and one or more fields identify the row through a primary key.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. What is a DAO?

**Difficulty:** 🟢 Basic

### Answer

A DAO, or Data Access Object, defines operations for interacting with Room's database. It can contain convenience operations such as inserts and deletes as well as SQL query methods.

Room generates the DAO implementation, which lets the rest of the application depend on an explicit persistence contract.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Can DAO methods be suspend?**

<details>
<summary>Reveal sample answer</summary>

An index is an additional database structure that helps SQLite locate rows efficiently for particular query patterns. In Room, you can declare indexes on an entity, for example `@Entity(indices = [Index(value = ["email"], unique = true)])`. The trade-off is that indexes consume storage and add work to inserts/updates, so they should support actual query or uniqueness requirements rather than being added indiscriminately.

</details>

2. **Can DAO methods return Flow?**

<details>
<summary>Reveal sample answer</summary>

They can be, but separating persistence models from UI models is often useful when the database schema and UI requirements evolve independently. For example, a `UserEntity` may contain database-specific fields while a `UserUiModel` exposes only the name and formatted display state. For a small application the mapping layer may be unnecessary overhead; the decision should follow the amount of coupling and transformation needed.

</details>

3. **Where should business rules live?**

<details>
<summary>Reveal sample answer</summary>

Yes. Room supports `suspend` DAO methods for one-shot database operations. For example, `@Query("SELECT * FROM User WHERE id = :id") suspend fun getUser(id: Long): User?` can be called from a coroutine without blocking the caller's thread while the database operation runs. The important point is that `suspend` gives you a coroutine-friendly API; it does not mean the operation should be launched arbitrarily from UI code without lifecycle consideration.

</details>

### Common Mistake

Using a DAO as the application's entire repository layer.



### Quick Revision

**Key idea:** A DAO, or Data Access Object, defines operations for interacting with Room's database. It can contain convenience operations such as inserts and deletes as well as SQL query methods.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How does Room work with Flow?

**Difficulty:** 🟡 Intermediate

### Answer

A DAO can expose observable query results as `Flow`. When Room detects relevant database changes, the observable query can emit updated results.

This fits reactive Android architecture well: Room emits local state, the repository maps it, the ViewModel exposes UI state, and the UI collects it.

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

1. **What invalidates a Flow query?**

<details>
<summary>Reveal sample answer</summary>

Yes. Room supports observable queries that return `Flow`, allowing callers to react when the relevant database data changes. For example, `@Query("SELECT * FROM User ORDER BY name") fun observeUsers(): Flow<List<User>>` can emit new results after changes to the observed tables. For large result sets, returning an entire `List` on every emission may be expensive, so Paging can be a better fit.

</details>

2. **What happens with a large List result?**

<details>
<summary>Reveal sample answer</summary>

Business rules should generally live above the DAO, such as in a use case, domain/service layer, or repository depending on the application's architecture. A DAO should focus on persistence operations: queries, inserts, updates, and deletes. For example, a rule such as `only verified users can place an order` should not be hidden inside a `UserDao`; it belongs in the business layer where it can be tested independently of SQLite. Keeping this boundary clear also prevents persistence concerns from leaking into business decisions.

</details>

3. **How does Paging differ?**

<details>
<summary>Reveal sample answer</summary>

Room tracks the database tables involved in an observable query and invalidates the query when relevant changes occur, causing the Flow to emit updated data. For example, a Flow querying `User` can emit after an insert or update to the `User` table. A subtle point is that invalidation does not mean every database change causes every Flow to emit; the observed query's tables determine relevance, and the resulting query still has to execute to produce the new value.

</details>

### Common Mistake

Assuming Flow automatically makes large database queries efficient.



### Quick Revision

**Key idea:** A DAO can expose observable query results as `Flow`. When Room detects relevant database changes, the observable query can emit updated results.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q8. How does Room integrate with Paging?

**Difficulty:** 🟡 Intermediate

### Answer

Room can provide a `PagingSource` for paginated database queries. Paging then loads only the portions of a large dataset needed by the UI.

This is particularly useful for offline-first lists where Room is the local source of truth and the UI needs thousands or millions of records without loading them all at once.

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

- https://developer.android.com/training/data-storage/room
- https://developer.android.com/training/data-storage/room/accessing-data

### Quick Revision

**Key idea:** Room can provide a `PagingSource` for paginated database queries. Paging then loads only the portions of a large dataset needed by the UI.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
