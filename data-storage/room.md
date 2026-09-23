# Room Fundamentals

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is Room?

**Difficulty:** 🟢 Basic

### Answer

Room is a Jetpack persistence library that provides an abstraction over SQLite. It models database tables as entities, database operations as DAOs, and the database itself through `RoomDatabase`.

Its benefits include structured database access, compile-time SQL validation, migration support, transaction APIs, and integration with Kotlin coroutines, Flow, and Paging.

### Common Follow-ups

- What are Entity, DAO, and RoomDatabase?
- Why Room instead of SQLite APIs?
- How does Room validate SQL?
- How does Room integrate with Flow?

### Senior/Lead Perspective

Senior developers should explain where Room belongs in architecture: normally behind a repository/data-source boundary rather than leaking database details throughout the application.

### Common Mistake

Calling Room a completely different database engine from SQLite.

## Q2. What are the main components of Room?

**Difficulty:** 🟢 Basic

### Answer

The core components are:

- **Entity**: maps application data to a table.
- **DAO**: defines database operations and queries.
- **RoomDatabase**: provides the database configuration and access to DAOs.

Room generates implementation code from these declarations at build time.

### Common Follow-ups

- Can a DAO be an interface?
- What does @Database define?
- How does Room generate implementations?

### Senior/Lead Perspective

A good senior answer connects these pieces to separation of concerns: the DAO isolates persistence operations, while repositories coordinate multiple sources.

### Common Mistake

Putting business logic and network orchestration directly into DAOs.

## Q3. How does Room verify SQL queries?

**Difficulty:** 🟡 Intermediate

### Answer

Room processes DAO definitions at compile time and checks queries against the known database schema. This can catch invalid table/column references and result-mapping problems before the application runs.

This is one of the practical advantages over constructing raw SQL dynamically at runtime.

### Common Follow-ups

- What happens if a column is renamed?
- What limitations remain?
- Can Room validate dynamically generated SQL?

### Senior/Lead Perspective

Compile-time validation is powerful but does not replace integration testing. Query correctness can still depend on actual data distributions, indexes, performance, and migration history.

### Common Mistake

Assuming compile-time validation means every query is automatically performant.

## Q4. Why shouldn't database operations run on the main thread?

**Difficulty:** 🟢 Basic

### Answer

Database operations can involve disk I/O, locking, query execution, and object mapping. Running them on the main thread can block rendering and contribute to jank or ANRs.

Use suspend DAO methods, Flow, or another asynchronous approach and keep expensive work off the UI thread.

### Common Follow-ups

- Does Room automatically make every query asynchronous?
- What is allowMainThreadQueries()?
- How would you find slow queries?

### Senior/Lead Perspective

A senior developer should also consider query design, indexes, result size, and transaction duration—not just moving a bad query to an IO dispatcher.

### Common Mistake

Thinking `Dispatchers.IO` alone fixes an inefficient query.

## Q5. What is an Entity?

**Difficulty:** 🟢 Basic

### Answer

An Entity is a class that Room maps to a database table. Its fields generally correspond to columns, and one or more fields identify the row through a primary key.

Entities describe persistence structure; they do not have to be the same objects used by the UI or domain layer.

### Common Follow-ups

- Can an Entity have a composite primary key?
- What are indexes?
- Should entities be exposed to UI?

### Senior/Lead Perspective

Keeping database entities separate from domain/UI models makes schema evolution less disruptive and prevents persistence concerns from spreading through the application.

### Common Mistake

Using one Entity class as the network DTO, domain model, and UI model.

## Q6. What is a DAO?

**Difficulty:** 🟢 Basic

### Answer

A DAO, or Data Access Object, defines operations for interacting with Room's database. It can contain convenience operations such as inserts and deletes as well as SQL query methods.

Room generates the DAO implementation, which lets the rest of the application depend on an explicit persistence contract.

### Common Follow-ups

- Can DAO methods be suspend?
- Can DAO methods return Flow?
- Where should business rules live?

### Senior/Lead Perspective

DAOs should normally express data access concerns. Cross-source orchestration, caching policy, and business decisions generally belong above the DAO.

### Common Mistake

Using a DAO as the application's entire repository layer.

## Q7. How does Room work with Flow?

**Difficulty:** 🟡 Intermediate

### Answer

A DAO can expose observable query results as `Flow`. When Room detects relevant database changes, the observable query can emit updated results.

This fits reactive Android architecture well: Room emits local state, the repository maps it, the ViewModel exposes UI state, and the UI collects it.

### Common Follow-ups

- What invalidates a Flow query?
- What happens with a large List result?
- How does Paging differ?

### Senior/Lead Perspective

For large collections, avoid emitting massive lists repeatedly. Use projections, pagination, and targeted queries when the UI does not need the whole dataset.

### Common Mistake

Assuming Flow automatically makes large database queries efficient.

## Q8. How does Room integrate with Paging?

**Difficulty:** 🟡 Intermediate

### Answer

Room can provide a `PagingSource` for paginated database queries. Paging then loads only the portions of a large dataset needed by the UI.

This is particularly useful for offline-first lists where Room is the local source of truth and the UI needs thousands or millions of records without loading them all at once.

### Common Follow-ups

- Why not return List<T>?
- How does invalidation affect PagingSource?
- How would you combine API and Room with Paging?

### Senior/Lead Perspective

At scale, pagination is part of the data model, not just a UI optimization. The query, indexes, ordering, and stable keys should all support the paging strategy.

### Common Mistake

Loading the entire table into memory and then slicing it in the ViewModel.

### References

- https://developer.android.com/training/data-storage/room
- https://developer.android.com/training/data-storage/room/accessing-data