# Room Transactions & Consistency

> Interview preparation from Android fundamentals to Senior/Lead-level reasoning.

Each question is intentionally written as a learning resource: the answer explains the concept, then the follow-ups and senior discussion push toward production reasoning.

## Q1. What is a database transaction?

**Difficulty:** 🟢 Basic

### Answer

A transaction groups multiple database operations into a unit with atomicity: the application should not observe a partially committed sequence when the transaction is used correctly.

Transactions are essential when several changes must succeed or fail together, such as creating an order and its order items.

### Common Follow-ups

- What happens when one operation fails?
- How do you declare a transaction in Room?
- What is transaction scope?

### Senior/Lead Perspective

The senior concern is defining the business invariant first: which writes must be atomic? Only then should you decide the transaction boundary.

### Common Mistake

Putting unrelated long-running operations into one transaction.

## Q2. What does @Transaction do in Room?

**Difficulty:** 🟡 Intermediate

### Answer

`@Transaction` tells Room that the annotated DAO operation should run within a database transaction where supported by the operation.

It is useful when multiple database actions must be treated as one atomic operation, and it is also relevant to certain multi-query relationship reads that need a consistent view.

### Common Follow-ups

- Can @Transaction be used with suspend functions?
- What should not happen inside a transaction?
- Can network calls be inside it?

### Senior/Lead Perspective

Never use a database transaction as a general-purpose application workflow boundary. Network calls and long waits should normally be outside the DB transaction.

### Common Mistake

Keeping a transaction open while waiting for a network response.

## Q3. How would you atomically create an order and its order items?

**Difficulty:** 🟡 Intermediate

### Answer

Insert the order and all associated order items inside one database transaction. If any required insert fails, the transaction should roll back so the database does not contain a half-created order.

The transaction boundary should be at the DAO/database layer rather than implemented as separate calls from the UI.

### Common Follow-ups

- How would you generate the order ID?
- How do you handle duplicate sync retries?
- What if payment happens remotely?

### Senior/Lead Perspective

Local atomicity does not make a remote payment atomic. For distributed workflows, use explicit states such as pending/confirmed/failed and idempotency keys.

### Common Mistake

Assuming a Room transaction can make a database write and a remote API call one atomic operation.

## Q4. Can a Room transaction include network calls?

**Difficulty:** 🔴 Advanced

### Answer

No—more precisely, a Room database transaction should not be held open while waiting for network I/O. A database transaction controls database consistency; it cannot roll back a remote server operation.

For workflows spanning local and remote systems, use a state machine or outbox/sync pattern instead.

### Common Follow-ups

- How would you design payment + local order state?
- What is an outbox?
- How do you recover after process death?

### Senior/Lead Perspective

This is a common Senior/Lead distinction: **database transactions solve local consistency; distributed workflows require coordination and idempotency**.

### Common Mistake

Trying to make API + Room atomic by wrapping both in a coroutine transaction.

## Q5. How do you handle idempotent writes in Room?

**Difficulty:** 🔴 Advanced

### Answer

Use stable unique identifiers, unique constraints, and upsert/update semantics so retrying the same operation does not create duplicate records.

For synchronization, the local write should be safe to repeat after a crash or network retry. The server should ideally provide stable IDs or idempotency keys as well.

### Common Follow-ups

- How do you prevent duplicate orders?
- What if the server generates the ID?
- How do retries interact with transactions?

### Senior/Lead Perspective

Idempotency should be designed across the whole sync path, not just the database. A local unique key can prevent duplicate local rows, but it cannot by itself prevent duplicate remote side effects.

### Common Mistake

Assuming a transaction automatically makes a retried operation idempotent.

## Q6. What is the difference between atomicity and consistency?

**Difficulty:** 🔴 Advanced

### Answer

Atomicity means a transaction's grouped changes are committed together or not committed. Consistency means the database moves from one valid state to another according to its constraints and invariants.

A transaction helps enforce consistency when the operations are grouped correctly, but application-level invariants may still require explicit constraints and business logic.

### Common Follow-ups

- What do foreign keys enforce?
- What belongs in a unique constraint?
- Can application logic alone guarantee consistency?

### Senior/Lead Perspective

A strong design uses database constraints for invariants the database can enforce and application/domain logic for rules that require broader context.

### Common Mistake

Treating transaction boundaries as a substitute for database constraints.

## Q7. How would you diagnose a slow transaction?

**Difficulty:** 🔴 Advanced

### Answer

Measure transaction duration and identify whether the bottleneck is query execution, locking, excessive row counts, indexes, or application work accidentally performed inside the transaction.

Keep transactions narrow, use efficient SQL, batch operations where appropriate, and avoid doing unrelated computation or I/O while the transaction is open.

### Common Follow-ups

- How do indexes affect writes?
- How can large transactions affect concurrency?
- What should be logged?

### Senior/Lead Perspective

Production observability should capture enough information to identify slow operations without logging sensitive data or raw user content.

### Common Mistake

Blindly increasing dispatcher/thread-pool size instead of investigating the transaction itself.

## Q8. When should you use database constraints instead of application checks?

**Difficulty:** 🔴 Advanced

### Answer

Use database constraints for invariants that must hold regardless of which code path writes the database—for example unique identifiers, foreign keys, and non-null requirements.

Application checks are still necessary for business rules that depend on external state or complex domain conditions, but database constraints provide a final local integrity boundary.

### Common Follow-ups

- What happens under concurrent writes?
- Why is a unique index stronger than 'check then insert'?
- Which business rules belong in the domain layer?

### Senior/Lead Perspective

If two threads/processes can write the same database, a check-then-insert sequence without a constraint can race. A database-enforced uniqueness rule is the stronger guarantee.

### Common Mistake

Assuming a pre-insert existence check is enough to guarantee uniqueness.

### References

- https://developer.android.com/training/data-storage/room/accessing-data