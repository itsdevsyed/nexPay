# NexusPay Architecture

## Principles
1. Modular monolith, not microservices.
2. PostgreSQL is the source of truth for financial records.
3. The ledger is immutable and append-only.
4. Only the `ledger` module writes ledger entries.
5. Payment operations are atomic; PostgreSQL enforces consistency, not Redis.

## Decisions log
(Each decision gets a date, the options considered, and why we chose one.)
