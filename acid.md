## What's ACID

**A**tomicity  
**C**onsistency  
**I**solation  
**D**urability

## What is a database transaction

A transaction is a collection of queries
necessary to complete a action.

**Example**

Transfering money from bank account $\alpha$ to bank account $\beta$.

Let's say we want to transfer 10 from account $\alpha$ to account $\beta$.

```sql
BEGIN
```

First we `SELECT` the balance from account $\alpha$

```sql
SELECT balance FROM accounts WHERE account_id = α
```

Then we check that account $\alpha$ has enough money to make a transfer to account $\beta$.

If it does have enough money, we deposit the amount into account $\beta$

```sql
UPDATE accounts
SET balance = balance + 10
where account_id = β
```

and then we update account $\alpha$ balance

```sql
UPDATE accounts
SET balance = balance - 10
where account_id = α
```

and then we commit the transaction

```sql
COMMIT
```

## Implicit transactions

Whenever we execute a query, if we do not start a transaction manually, the database starts one and immediately commits it.

`BEGIN` and `COMMIT` are
added to this query if we don't add them

```sql
UPDATE table_name
SET column_name = α
```

In practice, the query looks like this

```sql
BEGIN
UPDATE table_name
SET column_name = α
COMMIT
```

## Atomicity

Every query inside the transaction should succeed, if one query fails, the transaction is rolled back and no changes are persisted to the database.

## Isolation

## Isolation levels

### Read uncommitted

No isolation, any change from the outside is visible to the transaction, committed or not.

### Read committed

Each query in a transaction only sees changes that were committed by other transactions.

### Repeatable read

The transaction will make sure that when a query reads a row, that row will remain unchanged while the transaction is running.

### Snapshot

Each query in a transaction only sees changes that were commited before the transaction started.

### Serializable

Transactions are ran in sequence, there is no concurrency.

## Consistency

### Consistency in data

It can be inforced at the application level or by the database.

**Example**

Inforced by integrity checks. Table $\alpha$ can
have a foreign key ta references table $\beta$ only if the row being referenced by table $\alpha$ actually exists in table $\beta$.

### Consistency in reads

**Example**

We have a database $\alpha $ used for writes and a database $\beta$ used for reads.

If we write to $\alpha$ and immediately read from $\beta$ we may:

- Not find the value we expected to find because the read database has not been synced yet
- Get a stale result because the read database has not been synced yet

Both SQL and NoSQL databases have this problem and they usually solve it with eventual consistency.

## Durability

Changes made by committed transactions must be persisted in a durable non-volatile storage.

Durability is the process of persisting writes made to the database to a non-volatile storage.

Persisting to disk is a slow operation and because of that different databases have different ways of doing it.

**Redis**

Redis persists changes to memory and saves a snapshot of the data that's in memory to disk from time to time.
Which means there's a change of data being lost if the redis server dies for some reason before the data in memory is saved to disk and it is also not saved anywhere else.

### Durability techniques

**WAL - Write ahead log**

Write only log that the database writes changes to.

If the database crashes, the database can to through the write ahead log and rebuild the state it was previously in.

Persisting to disk is used with the write ahead log. The WAL is just there to makes things faster.

**Asynchronous snapshot**

We persist data to memory and after some time persist a snapshot of the data that's in memory to disk.

Redis does that.
