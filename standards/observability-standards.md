# Observability Standards

## Purpose
Ensure all systems are measurable, diagnosable, and supportable in real environments.

Observability is not optional. If a system cannot be understood in production, it is not production-ready.

These standards define how services should expose operational visibility through logs, metrics, traces, health checks, and alerting.

---

## Core Principles

### 1. Production Visibility First
Every service must provide enough visibility to answer:

- Is it up?
- Is it healthy?
- Is it slow?
- Is it failing?
- Why is it failing?
- What changed?

### 2. Structured Over Ad Hoc
Use structured telemetry whenever possible.

- structured logs over raw string logs
- named metrics over vague counters
- correlated traces over isolated events

### 3. Actionable Signals Only
Telemetry should help engineers make decisions.

Avoid noisy instrumentation that creates dashboards nobody trusts.

### 4. Correlation Across Layers
A request should be traceable across:

- client
- API
- service
- queue
- database
- external dependencies

### 5. Security and Privacy Matter
Observability must never expose secrets, tokens, credentials, or sensitive personal data.

---

## Required Observability Pillars

Every production-capable service should support the following where applicable:

- logs
- metrics
- traces
- health checks
- alerts

---

## Logging Standards

## Purpose of Logs
Logs should help answer:

- what happened
- when it happened
- where it happened
- why it failed
- what request or entity was involved

### Logging Rules

- use structured logging
- log meaningful events, not everything
- include request and execution context
- avoid logging secrets, access tokens, passwords, or sensitive payloads
- prefer machine-parseable fields over freeform text
- use consistent event naming

### Required Log Context
Where applicable, logs should include:

- timestamp
- service name
- environment
- log level
- request ID or correlation ID
- user ID or actor ID when safe
- operation or endpoint name
- outcome or status
- error code when applicable

### Log Levels

#### Debug
Use for development-only or low-level troubleshooting details.

Do not rely on debug logs in production for critical behavior.

#### Information
Use for meaningful lifecycle events such as:

- service start
- request completed
- job completed
- resource created
- authentication success when appropriate

#### Warning
Use for recoverable issues or suspicious behavior such as:

- retries
- degraded dependency response
- validation anomalies
- throttling
- unexpected but non-fatal conditions

#### Error
Use for failed operations that require attention.

Examples:

- request failure
- job failure
- dependency timeout
- unhandled exception
- failed database operation

#### Critical
Use for severe incidents affecting service availability, integrity, or safety.

Examples:

- service cannot start
- database unavailable
- message processing deadlock
- repeated authentication system failure

### Logging Anti-Patterns

Do not:

- log entire request bodies blindly
- log credentials or tokens
- log duplicate errors repeatedly without aggregation strategy
- log every internal step of a normal request path
- rely on unstructured console messages as the main logging strategy

---

## Metrics Standards

## Purpose of Metrics
Metrics should make system behavior visible over time.

They should answer:

- how often something happens
- how long it takes
- how often it fails
- whether the system is degrading

### Required Metric Categories

#### Availability Metrics
Examples:

- service uptime
- successful health checks
- request success rate

#### Traffic Metrics
Examples:

- requests per second
- jobs processed per minute
- queue depth
- messages received

#### Latency Metrics
Examples:

- request duration
- database query duration
- external API response time
- background job processing time

#### Error Metrics
Examples:

- error rate
- failed jobs
- timeout count
- retry count
- failed authentication attempts

#### Resource Metrics
Examples:

- CPU usage
- memory usage
- disk utilization
- connection pool usage
- container restarts

### Metric Rules

- use consistent naming conventions
- prefer meaningful business and operational metrics
- collect metrics at service and infrastructure levels
- define baseline thresholds where possible
- separate success volume from error volume

### Metric Anti-Patterns

Do not:

- create too many vanity metrics
- track metrics nobody reviews
- use vague names like `count_total` with no domain meaning
- treat infrastructure metrics alone as sufficient application observability

---

## Tracing Standards

## Purpose of Traces
Tracing should make request flow visible across service boundaries.

Traces should help answer:

- where latency occurred
- which dependency failed
- which step of a request broke
- how requests move across the system

### Tracing Rules

- generate or propagate a correlation ID for each request
- preserve trace context across services where possible
- instrument outbound calls to databases, queues, and external APIs
- measure spans around meaningful operations
- include failure context on trace spans where supported

### Minimum Trace Coverage
Where applicable, tracing should cover:

- inbound HTTP requests
- outbound HTTP requests
- database calls
- queue publish and consume flows
- background job execution
- cache lookups and writes

### Tracing Anti-Patterns

Do not:

- trace everything at a noisy or expensive level without reason
- omit context propagation across async flows
- rely on traces without logs or metrics

---

## Correlation ID Standards

Every inbound request or asynchronous job should have a correlation identifier.

### Rules

- generate one if none is provided
- propagate it through logs, traces, and downstream calls
- include it in error logs
- include it in background job contexts where possible

### Goal
A production incident should be traceable from one identifier across all relevant telemetry.

---

## Health Check Standards

Health checks are required for all production services.

### Types of Health Checks

#### Liveness Check
Answers:
- Is the service process running?

Use for container or process restart decisions.

#### Readiness Check
Answers:
- Can the service receive traffic safely?

Should reflect critical dependencies only when appropriate.

### Health Check Rules

- expose health endpoints for deployable services
- keep liveness lightweight
- keep readiness meaningful
- do not hide broken dependencies behind a permanently healthy response
- do not make health checks too expensive

### Typical Health Dependencies
Depending on service type, readiness may include:

- database connectivity
- queue connectivity
- cache availability
- required config availability

---

## Alerting Standards

## Purpose of Alerts
Alerts should notify engineers of actionable production problems.

### Alert Rules

- alert on symptoms that require response
- tie alerts to user impact or operational risk
- define severity levels
- route alerts to the right people or systems
- avoid alert fatigue

### Good Alert Targets
Examples:

- sustained elevated error rate
- sustained latency degradation
- service unavailable
- job failure spike
- dead letter queue growth
- queue backlog above threshold
- repeated deployment failures
- resource exhaustion approaching critical levels

### Bad Alert Targets
Avoid alerts for:

- single transient events with auto-recovery
- noisy low-value warnings
- metrics with no owner or response plan

### Alert Design Requirements

Every alert should have:

- clear name
- clear threshold
- severity level
- expected response owner
- linked remediation notes where possible

---

## Dashboard Standards

Dashboards should provide fast visibility into service health and behavior.

### Every Production Service Dashboard Should Include

- request volume
- error rate
- latency
- health status
- CPU and memory
- dependency failures
- queue metrics if applicable
- recent deploy markers if available

### Dashboard Rules

- favor clarity over density
- group charts by operational purpose
- keep labels explicit
- avoid dashboards with dozens of unreadable widgets
- build at least one high-level service dashboard and one deeper troubleshooting dashboard

---

## Failure Analysis Standards

When a system fails, observability should support fast diagnosis.

### Required Support for Troubleshooting

- logs must identify the failing operation
- metrics must show when degradation began
- traces should reveal dependency bottlenecks where possible
- errors should include enough context for diagnosis
- deployment and config changes should be discoverable

### Post-Incident Requirements

After a production issue, teams should be able to answer:

- when the issue started
- which users or services were affected
- what failed first
- what signals were missed
- what new telemetry is needed

---

## Environment Standards

### Development
- support readable logs
- allow local debugging visibility
- do not require full production telemetry stack to run locally

### Staging
- mimic production telemetry patterns as closely as practical
- validate health checks, dashboards, and alerts before production rollout

### Production
- enable structured logging
- enable metrics and trace correlation
- enforce alert coverage for critical services
- retain logs according to environment policy

---

## Data Protection Rules

Observability data must never leak sensitive information.

Do not log:

- passwords
- API keys
- bearer tokens
- session secrets
- full payment data
- protected personal data unless explicitly approved and masked

Where user identifiers are necessary for diagnosis:

- prefer internal IDs over raw personal details
- mask or redact sensitive fields
- follow data retention and privacy policies

---

## Instrumentation Priorities

When full observability is not yet implemented, prioritize in this order:

1. structured logs
2. request correlation IDs
3. health checks
4. latency and error metrics
5. dependency metrics
6. tracing across service boundaries
7. alerting and dashboards refinement

---

## Cloud and Infrastructure Expectations

For cloud-hosted services, observability should also account for:

- container restarts
- task or pod failures
- load balancer health
- auto-scaling behavior
- queue depth
- database saturation
- network errors
- deployment events

Application telemetry and infrastructure telemetry should be reviewed together.

---

## Background Jobs and Async Processing

For background workers, queues, and scheduled tasks, observability must include:

- job start and completion
- job duration
- failure reason
- retry count
- dead letter queue activity
- queue age and backlog
- correlation to upstream request or event where possible

---

## External Dependency Monitoring

Any critical external dependency should have visibility for:

- response time
- error rate
- timeout rate
- circuit breaker or retry behavior where used

Critical third-party failures should not appear as generic internal failures.

---

## Review Checklist

Before considering a service production-ready, verify:

- Are logs structured and useful?
- Are secrets excluded from logs?
- Are request IDs or correlation IDs present?
- Are key operational metrics emitted?
- Are error rates measurable?
- Are latency trends visible?
- Are health checks implemented?
- Are critical dependencies observable?
- Are alerts defined for high-impact failures?
- Can incidents be investigated without guessing?

---

## Anti-Patterns to Avoid

Do not:

- treat logs as observability by themselves
- depend on manual SSH-style debugging in production
- log too much and call it visibility
- skip health checks
- skip alert ownership
- rely only on infrastructure metrics
- instrument nothing until after the first outage
- expose sensitive data in logs or traces

---

## Goal
Build systems that can be understood under pressure.

A production issue should be diagnosable through logs, metrics, traces, and alerts without relying on guesswork, heroics, or tribal knowledge.