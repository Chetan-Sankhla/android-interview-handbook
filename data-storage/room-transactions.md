# Room Transactions & Consistency

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is written as a learning resource: start with the core answer, then use progressively deeper follow-ups to test mechanism, trade-offs, edge cases, and real-world usage.

## Q1. What is a database transaction?

**Difficulty:** 🟢 Basic

### Answer

A transaction groups multiple database operations into a unit with atomicity: the application should not observe a partially committed sequence when the transaction is used correctly.

Transactions are essential when several changes must succeed or fail together, such as creating an order and its order items.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What happens when one operation fails?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you declare a transaction in Room?** → [Open the related question](room-transactions.md#what-does-transaction-do-in-room)

3. **What is transaction scope?**

<details>
<summary>Reveal sample answer</summary>

Define the transaction boundary and the invariant it protects. Explain what other readers see if the operation fails and which changes commit or roll back together.

</details>

### Common Mistake

Putting unrelated long-running operations into one transaction.



### Quick Revision

**Key idea:** A transaction groups multiple database operations into a unit with atomicity: the application should not observe a partially committed sequence when the transaction is used correctly. Transactions are essential when several changes must succeed or fail together, such as creating an order and its order items.

### Interview Insight

Explain the atomic boundary and the invariant it protects. Interviewers usually care more about what happens on partial failure than about the annotation name.
## Q2. What does @Transaction do in Room?

**Difficulty:** 🟡 Intermediate

### Answer

`@Transaction` tells Room that the annotated DAO operation should run within a database transaction where supported by the operation.

It is useful when multiple database actions must be treated as one atomic operation, and it is also relevant to certain multi-query relationship reads that need a consistent view.

### Example

```kotlin
@Transaction
suspend fun placeOrder(order: Order, items: List<OrderItem>) {
    insertOrder(order)
    insertItems(items)
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **Can @Transaction be used with suspend functions?**

<details>
<summary>Reveal sample answer</summary>

Define the transaction boundary and the invariant it protects. Explain what other readers see if the operation fails and which changes commit or roll back together.

</details>

2. **What should not happen inside a transaction?**

<details>
<summary>Reveal sample answer</summary>

Define the transaction boundary and the invariant it protects. Explain what other readers see if the operation fails and which changes commit or roll back together.

</details>

3. **Can network calls be inside it?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Keeping a transaction open while waiting for a network response.



### Quick Revision

**Key idea:** `@Transaction` tells Room that the annotated DAO operation should run within a database transaction where supported by the operation. It is useful when multiple database actions must be treated as one atomic operation, and it is also relevant to certain multi-query relationship reads that need a consistent view.

### Interview Insight

Explain the atomic boundary and the invariant it protects. Interviewers usually care more about what happens on partial failure than about the annotation name.
## Q3. How would you atomically create an order and its order items?

**Difficulty:** 🟡 Intermediate

### Answer

Insert the order and all associated order items inside one database transaction. If any required insert fails, the transaction should roll back so the database does not contain a half-created order.

The transaction boundary should be at the DAO/database layer rather than implemented as separate calls from the UI.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you generate the order ID?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How do you handle duplicate sync retries?**

<details>
<summary>Reveal sample answer</summary>

Define the local source of truth, sync trigger, conflict policy, and UI behavior while offline. A cache by itself is not an offline-first architecture unless consistency rules are explicit.

</details>

3. **What if payment happens remotely?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Assuming a Room transaction can make a database write and a remote API call one atomic operation.



### Quick Revision

**Key idea:** Insert the order and all associated order items inside one database transaction. If any required insert fails, the transaction should roll back so the database does not contain a half-created order.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q4. Can a Room transaction include network calls?

**Difficulty:** 🔴 Advanced

### Answer

No—more precisely, a Room database transaction should not be held open while waiting for network I/O. A database transaction controls database consistency; it cannot roll back a remote server operation.

For workflows spanning local and remote systems, use a state machine or outbox/sync pattern instead.

### Example

```kotlin
@Transaction
suspend fun placeOrder(order: Order, items: List<OrderItem>) {
    insertOrder(order)
    insertItems(items)
}
```

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How would you design payment + local order state?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What is an outbox?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do you recover after process death?**

<details>
<summary>Reveal sample answer</summary>

ViewModel state survives configuration changes but not process death. Durable state belongs in persistent storage; small restorable UI state can use saved-state mechanisms where appropriate.

</details>

### Common Mistake

Trying to make API + Room atomic by wrapping both in a coroutine transaction.



### Quick Revision

**Key idea:** No—more precisely, a Room database transaction should not be held open while waiting for network I/O. A database transaction controls database consistency; it cannot roll back a remote server operation.

### Interview Insight

Explain the atomic boundary and the invariant it protects. Interviewers usually care more about what happens on partial failure than about the annotation name.
## Q5. How do you handle idempotent writes in Room?

**Difficulty:** 🔴 Advanced

### Answer

Use stable unique identifiers, unique constraints, and upsert/update semantics so retrying the same operation does not create duplicate records.

For synchronization, the local write should be safe to repeat after a crash or network retry. The server should ideally provide stable IDs or idempotency keys as well.

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

1. **How do you prevent duplicate orders?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What if the server generates the ID?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **How do retries interact with transactions?**

<details>
<summary>Reveal sample answer</summary>

Define the transaction boundary and the invariant it protects. Explain what other readers see if the operation fails and which changes commit or roll back together.

</details>

### Common Mistake

Assuming a transaction automatically makes a retried operation idempotent.



### Quick Revision

**Key idea:** Use stable unique identifiers, unique constraints, and upsert/update semantics so retrying the same operation does not create duplicate records. For synchronization, the local write should be safe to repeat after a crash or network retry.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q6. What is the difference between atomicity and consistency?

**Difficulty:** 🔴 Advanced

### Answer

Atomicity means a transaction's grouped changes are committed together or not committed. Consistency means the database moves from one valid state to another according to its constraints and invariants.

A transaction helps enforce consistency when the operations are grouped correctly, but application-level invariants may still require explicit constraints and business logic.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **What do foreign keys enforce?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **What belongs in a unique constraint?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

3. **Can application logic alone guarantee consistency?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Treating transaction boundaries as a substitute for database constraints.



### Quick Revision

**Key idea:** Atomicity means a transaction's grouped changes are committed together or not committed. Consistency means the database moves from one valid state to another according to its constraints and invariants.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
## Q7. How would you diagnose a slow transaction?

**Difficulty:** 🔴 Advanced

### Answer

Measure transaction duration and identify whether the bottleneck is query execution, locking, excessive row counts, indexes, or application work accidentally performed inside the transaction.

Keep transactions narrow, use efficient SQL, batch operations where appropriate, and avoid doing unrelated computation or I/O while the transaction is open.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

1. **How do indexes affect writes?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

2. **How can large transactions affect concurrency?**

<details>
<summary>Reveal sample answer</summary>

Define the transaction boundary and the invariant it protects. Explain what other readers see if the operation fails and which changes commit or roll back together.

</details>

3. **What should be logged?**

<details>
<summary>Reveal sample answer</summary>

Answer the specific mechanism first, then give a concrete example and one relevant trade-off or edge case.

</details>

### Common Mistake

Blindly increasing dispatcher/thread-pool size instead of investigating the transaction itself.



### Quick Revision

**Key idea:** Measure transaction duration and identify whether the bottleneck is query execution, locking, excessive row counts, indexes, or application work accidentally performed inside the transaction. Keep transactions narrow, use efficient SQL, batch operations where appropriate, and avoid doing unrelated computation or I/O while the transaction is open.

### Interview Insight

Explain the atomic boundary and the invariant it protects. Interviewers usually care more about what happens on partial failure than about the annotation name.
## Q8. When should you use database constraints instead of application checks?

**Difficulty:** 🔴 Advanced

### Answer

Use database constraints for invariants that must hold regardless of which code path writes the database—for example unique identifiers, foreign keys, and non-null requirements.

Application checks are still necessary for business rules that depend on external state or complex domain conditions, but database constraints provide a final local integrity boundary.

### Common Follow-ups

Try to answer these aloud before revealing the answer.

### References

- https://developer.android.com/training/data-storage/room/accessing-data

### Quick Revision

**Key idea:** Use database constraints for invariants that must hold regardless of which code path writes the database—for example unique identifiers, foreign keys, and non-null requirements. Application checks are still necessary for business rules that depend on external state or complex domain conditions, but database constraints provide a final local integrity boundary.

### Interview Insight

A good answer starts with the mechanism, then adds one concrete example and one trade-off that follows from the choice.
