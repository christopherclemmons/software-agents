# Orchestration Rules

## Purpose
This document defines how agents are selected, coordinated, and combined to complete tasks.

The orchestrator is responsible for:
- understanding the request
- selecting the correct agents
- sequencing their responsibilities
- ensuring consistency across outputs

---

## Step 1: Task Classification

Every request must first be classified into one or more domains:

### Domains

- Frontend → React Agent
- Backend → Node Agent or .NET Agent
- Database → PostgreSQL Agent
- Infrastructure → Terraform or AWS Agent
- Security → Security Agent
- Testing → Testing Agent

---

## Step 2: Agent Selection

Select the **minimum number of agents required**.

Avoid:
- unnecessary agent involvement
- over-coordination

### Examples

**Single-agent tasks**
- Build UI component → React Agent
- Create SQL schema → PostgreSQL Agent

**Multi-agent tasks**
- Build feature → React + Backend + PostgreSQL
- Deploy service → Backend + Terraform + AWS

---

## Step 3: Execution Order

When multiple agents are involved, follow this order:

1. **Database (PostgreSQL Agent)**
   - schema
   - migrations
   - constraints

2. **Backend (Node/.NET Agent)**
   - APIs
   - business logic
   - validation

3. **Frontend (React Agent)**
   - UI
   - state management
   - API integration

4. **Infrastructure (Terraform/AWS Agent)**
   - deployment
   - networking
   - scaling

5. **Security Agent**
   - vulnerability review
   - auth validation

6. **Testing Agent**
   - test coverage
   - edge cases
   - validation scenarios

---

## Step 4: Responsibility Boundaries

Each agent must stay within its domain:

### React Agent
- UI and client-side behavior only
- must not design backend APIs

### Backend Agent (.NET/Node)
- APIs and business logic
- must not design database schema without PostgreSQL Agent

### PostgreSQL Agent
- owns schema and query optimization
- must not define API behavior

### Terraform/AWS Agent
- owns infrastructure and deployment
- must not modify application logic

---

## Step 5: Conflict Resolution

If agents disagree:

1. Prefer existing architecture
2. Prefer simpler solution
3. Escalate conflict explicitly
4. Do not silently override decisions

---

## Step 6: Output Composition

Final output should:

- combine all agent contributions into one cohesive plan
- remove duplication
- ensure consistency across layers
- clearly separate:
  - what is changing
  - why it is changing
  - risks involved

---

## Step 7: Incremental Execution

For large tasks:

- break into steps
- complete one layer at a time
- validate before moving forward

---

## Step 8: Safety Checks

Before finalizing output:

- Are assumptions stated?
- Are breaking changes identified?
- Is the solution over-engineered?
- Does it match the existing stack?
- Is it production-ready?

---

## Example Flow

### Feature: Add Job Application System

1. PostgreSQL Agent
   - create applications table
   - define relationships

2. Backend Agent
   - create endpoints
   - validation logic

3. React Agent
   - build application UI
   - connect to API

4. Testing Agent
   - test submission flow
   - edge cases

---

## Anti-Patterns to Avoid

- One agent doing everything
- Skipping database design
- Frontend inventing APIs
- Backend ignoring schema constraints
- Infrastructure decisions without system context

---

## Goal

Ensure that:
- agents collaborate, not conflict
- systems are built in the correct order
- outputs are clean, consistent, and production-ready