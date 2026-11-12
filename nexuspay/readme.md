# NexusPay – Simulated Payment Infrastructure

## 1. Project Overview

NexusPay is a backend-focused, simulated payment infrastructure platform designed to demonstrate how real-world payment systems work internally.

It is NOT a real banking application and will NOT process actual money. It will simulate users, wallets, funds, payments, refunds, and financial transactions.

The primary objective is to build a production-minded backend that demonstrates strong skills in:
- Backend architecture and system design.
- Database design and transaction management.
- Financial ledger systems.
- Concurrency control.
- Distributed systems.
- Event-driven architecture.
- Security and reliability.
- Automated testing and observability.

This should be a serious engineering project, not a basic CRUD application.

## 2. Core Business Scenario

Imagine a user has ₹10,000 in their wallet and attempts to transfer ₹2,000 to another user.

The system must:
1. Authenticate the sender.
2. Verify the sender's wallet.
3. Check sufficient balance.
4. Prevent duplicate requests.
5. Debit the sender.
6. Credit the receiver.
7. Record both sides of the financial transaction.
8. Maintain a complete transaction history.
9. Ensure the operation is atomic.
10. Return a reliable transaction status.

The most important challenge is financial correctness.

For example, if 100 concurrent requests attempt to withdraw ₹1,000 from a wallet containing ₹10,000, the system must prevent overspending, duplicate debits, inconsistent balances, and incomplete ledger entries.

## 3. Technology Stack

### Backend
- NestJS
- TypeScript
- Node.js
- REST APIs

### Database
- PostgreSQL hosted on Neon (managed cloud PostgreSQL).
- Prisma ORM.
- Database migrations through Prisma.

### Additional Infrastructure
- Redis: caching, rate limiting, and selected distributed coordination use cases.
- Apache Kafka: asynchronous event processing and event-driven workflows.
- JWT: authentication.
- Swagger/OpenAPI: API documentation.
- class-validator and class-transformer: request validation.
- Helmet: HTTP security headers.

### Testing
- Vitest.
- Supertest.
- Integration testing.
- Concurrency testing.
- Load testing.

### Deployment and Monitoring
- Docker and Docker Compose.
- Prometheus.
- Grafana.
- Kubernetes (later phase, not initially).

## 4. Core Features and Modules

Build the application as a modular monolith initially, with clear module boundaries.

### A. Authentication and Users
- User registration.
- Login and logout.
- Password hashing.
- JWT authentication.
- Role-based access control.
- User profile management.

### B. Wallet Management
- Create wallets.
- Associate wallets with users.
- Support simulated INR balances.
- Wallet status management.
- Prevent unauthorized wallet access.

### C. Double-Entry Ledger

This is the most important financial component.

Do not treat a wallet's balance column as the only source of truth.

Implement a proper double-entry ledger where every financial movement has corresponding debit and credit entries.

Requirements:
- Immutable ledger entries.
- Balanced financial transactions.
- Ledger-based balance calculation or a safely maintained balance projection.
- Complete audit history.
- No silent modification or deletion of financial records.

Every financial operation must preserve accounting consistency.

### D. Payment Engine
- Wallet-to-wallet transfers.
- Payment initiation.
- Payment processing.
- Success and failure states.
- Transaction history.
- Transaction references.
- Atomic debit and credit operations.

### E. Idempotency

Prevent duplicate payments when clients retry requests.

For example, if a client submits a payment and times out before receiving the response, retrying the same request must not transfer money twice.

Implement idempotency keys and store their results reliably.

### F. Concurrency and Race Conditions

Handle simultaneous financial requests safely.

Explore and implement:
- Database transactions.
- Row-level locking.
- Appropriate isolation levels.
- Atomic balance updates.
- Deadlock handling and retries where appropriate.
- Concurrent payment tests.

Do not rely exclusively on Redis locks for financial correctness. PostgreSQL must enforce the critical consistency guarantees.

### G. Refund System
- Full refunds.
- Partial refunds.
- Refund eligibility checks.
- Refund transaction records.
- Corresponding ledger entries.
- Duplicate refund prevention.

### H. Fraud Detection
Implement configurable rules such as:
- Excessive transaction frequency.
- Unusual transfer amounts.
- Repeated failed payment attempts.
- Suspicious transaction patterns.
- Transaction limits.

Initially use deterministic rule-based fraud detection rather than introducing machine learning unnecessarily.

### I. Reconciliation
Build a process that checks:
- Ledger consistency.
- Wallet balance consistency.
- Transaction status consistency.
- Missing or duplicate financial entries.

Generate reconciliation reports and identify discrepancies.

### J. Event-Driven Processing

Introduce Kafka when the core synchronous payment flow is reliable.

Potential events:
- PaymentInitiated.
- PaymentCompleted.
- PaymentFailed.
- RefundProcessed.
- FraudAlertGenerated.

Explore the transactional outbox pattern to prevent database commits and event publishing from becoming inconsistent.

## 5. Architecture Principles

Follow these principles throughout development:

1. Start with a modular monolith, not microservices.
2. Use PostgreSQL as the source of truth for financial records.
3. Keep the ledger immutable.
4. Make payment operations atomic.
5. Design APIs to be idempotent where necessary.
6. Handle concurrency explicitly.
7. Validate every external request.
8. Use proper authentication and authorization.
9. Keep business logic separate from controllers.
10. Use dependency injection and clear module boundaries.
11. Write tests for failure scenarios, not just successful requests.
12. Avoid unnecessary abstractions and premature optimization.

## 6. Development Phases

Build incrementally in this order:

**Phase 1 – Foundation**
- NestJS setup.
- Project structure.
- Environment configuration.
- Neon PostgreSQL connection.
- Prisma setup.
- Swagger.
- Validation and security middleware.

**Phase 2 – Database Design**
- Users.
- Wallets.
- Ledger accounts.
- Ledger transactions.
- Ledger entries.
- Payment records.
- Idempotency records.
- Refund records.

**Phase 3 – Authentication**
- Registration.
- Login.
- JWT.
- Authorization.

**Phase 4 – Wallet System**
- Wallet creation.
- Balance retrieval.
- Wallet ownership and access control.

**Phase 5 – Ledger Engine**
- Double-entry bookkeeping.
- Debit and credit operations.
- Ledger invariants.
- Balance verification.

**Phase 6 – Payment Engine**
- Wallet transfers.
- Atomic transactions.
- Payment states.
- Transaction history.

**Phase 7 – Reliability**
- Idempotency.
- Race-condition prevention.
- Concurrent transaction testing.
- Failure recovery.

**Phase 8 – Advanced Features**
- Redis.
- Refunds.
- Fraud detection.
- Reconciliation.

**Phase 9 – Event-Driven Architecture**
- Kafka.
- Event consumers.
- Transactional outbox.
- Retry and dead-letter handling.

**Phase 10 – Production Engineering**
- Docker.
- Integration and load testing.
- Prometheus and Grafana.
- Deployment.
- Kubernetes.

## 7. Expected Engineering Quality

Do not simply generate code that works for the happy path.

For every major feature, explain:
- Why the feature exists.
- How the architecture works.
- Why a particular technology was chosen.
- What can go wrong.
- How failures are handled.
- How data consistency is maintained.
- How to test the implementation.

Include:
- Clean and maintainable code.
- Meaningful naming.
- Proper error handling.
- Database constraints.
- Unit and integration tests.
- API documentation.
- Architecture documentation.
- Database relationship diagrams.
- Useful Git commits.

## 8. How the AI Assistant Should Work With Me

Act as a senior backend engineer and technical mentor.

Important instructions:

1. Do not build the entire application in one response.
2. Work phase by phase.
3. Before implementation, explain the architecture and reasoning.
4. Give commands in logical batches rather than unnecessarily tiny steps.
5. Explain important code decisions, especially database transactions and financial consistency.
6. Do not blindly agree with my technical decisions.
7. Challenge incorrect assumptions and suggest better engineering approaches.
8. Do not introduce Kafka, Redis, Kubernetes, or microservices before they are justified.
9. Never sacrifice financial correctness for implementation simplicity.
10. After each phase, verify that the implementation works before proceeding.

I want to understand the system deeply enough to explain its architecture, trade-offs, failure scenarios, and implementation decisions in an SDE2/SDE3 backend engineering interview.

## Final Objective

Build NexusPay as a technically credible simulation of payment infrastructure that demonstrates strong backend engineering, database expertise, financial consistency, concurrency handling, distributed systems knowledge, and production-oriented development practices.
