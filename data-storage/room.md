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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Why Room instead of SQLite APIs?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How does Room validate SQL?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

4. **How does Room integrate with Flow?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What does @Database define?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How does Room generate implementations?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What limitations remain?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Can Room validate dynamically generated SQL?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What are indexes?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Should entities be exposed to UI?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **Can DAO methods return Flow?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Where should business rules live?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What happens with a large List result?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How does Paging differ?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

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
