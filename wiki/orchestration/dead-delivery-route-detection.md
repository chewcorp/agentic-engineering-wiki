---
type: concept
title: Dead Delivery Route Detection
description: >
  Detect when accepted work is not reaching or being consumed by its intended
  recipient by measuring progress and liveness at each route boundary.
evidence: moderate
sources:
  - title: "Using dead-letter queues in Amazon SQS"
    resource: "Amazon SQS Developer Guide, redrive policies and DLQs — https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html"
  - title: "Receiving and deleting a message in Amazon SQS"
    resource: "Amazon SQS Developer Guide, receive, visibility and delete semantics — https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/step-receive-delete-message.html"
  - title: "Available CloudWatch metrics for Amazon SQS"
    resource: "Amazon SQS Developer Guide, ApproximateAgeOfOldestMessage and queue metrics — https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html"
  - title: "Consumer Configs"
    resource: "Apache Kafka 4.1 documentation, heartbeat.interval.ms and session.timeout.ms — https://kafka.apache.org/41/configuration/consumer-configs/"
  - title: "Transactional outbox pattern"
    resource: "Microservices.io, context, solution and result context — https://microservices.io/patterns/data/transactional-outbox.html"
  - title: "Transactional outbox pattern"
    resource: "AWS Prescriptive Guidance, issues and considerations — https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html"
---


A message can be accepted by a transport and still never reach the application expected to act on it.[7]
Transport acceptance, consumer receipt, and application completion are different states: in Amazon SQS, receiving a message does not delete it, and the consumer explicitly deletes it after processing.[2]
Record these states separately rather than treating a successful send or an agent's general online presence as proof of delivery.[7]

## Dead route, or work that is merely failing?

A dead-letter queue (DLQ) is useful for isolating messages that a consumer has received repeatedly but not processed successfully: SQS redrive policies move a message after its receive count passes the configured threshold.[1]
This mechanism does not by itself detect a route with no consumer.[1]
If no consumer receives the message, the receive-count threshold may never be reached, leaving the message in its source queue.[1]
Monitor pending work and route liveness as well as the DLQ.[1][3]

Distinguish **never read** from **partially read**.[2][3]
A never-read route has eligible pending work and no observed reads for that route during the observation window.[2][3]
Queue starvation instead has reads, but some items remain unvisited; one possible pattern is a consumer repeatedly reading only a fixed newest-first window.[unverified]
Read activity and per-item progress help separate these diagnoses; a stale request alone does not.[2][3]
A DLQ does not automatically mean “undeliverable”: it commonly captures repeatedly received processing failures.[1]
A missing route or paused poller can prevent any receive attempt.[unverified]

## Cheap signals, scoped to a route

Use a route key that identifies the actual delivery path (such as destination address × surface × consumer), not just the agent identity.[unverified]
Sample deterministic counters and timestamps; an LLM diagnosis is unnecessary for initial detection.[unverified]
These are recommendations derived from the distinct receive, metric, and heartbeat scopes documented for messaging systems.[2][3]

- **Pending count and oldest eligible age.** Alert when pending work exists and its oldest age exceeds the route's response SLO or expected service interval.[3][unverified]
- SQS exposes `ApproximateAgeOfOldestMessage`, an approximate age of the oldest unprocessed message.[3]
- Repeatedly received poison messages can be excluded, and the metric can reflect a later message instead.[3]
- Treat a missing metric as unknown, not zero; use several samples where the metric is approximate.[unverified]
- **Reads against backlog.** Compare received/read counts with pending count and oldest age.[2][3]
- Pending work with no route-local reads over the configured window points toward a missing route mapping, absent subscription, idle poller, or paused waker.[unverified]
- Reads advancing while completion does not points instead toward stuck processing or acknowledgement handling.[2][3]
- A zero-read counter is meaningful only if the expected consumer and observation window are known.[unverified]
- SQS counts receives separately from deletes, and repeated receives do not equal unique successful processing.[2]
- **Expected consumer registration and heartbeat.** Maintain an expected-consumer entry per route and its last heartbeat or poll time.[unverified]
- Kafka consumer heartbeats and session timeouts detect inactive group members and can trigger rebalancing.[4]
- They are broker-level group-liveness signals, not proof that an application is polling a particular mailbox or completing requests.[4]
- An expected route with no registered consumer, or with a stale route-specific liveness signal, should be unhealthy or explicitly unknown—not silently healthy because the same agent is active on another surface.[unverified]
- **Request/result reconciliation.** Compare durable submitted correlation IDs against durable results and explicit terminal failures.[unverified]
- Flag requests older than their response deadline with no terminal record.[unverified]
- A transactional outbox stores an event with the database transaction before a separate relay publishes it, addressing a sender-side dual-write gap.[5][6]
- Relays can publish duplicates, so consumers may need idempotency.[5][6]
- An outbox does not establish that a downstream route is live or that a result returned.[5][6]
- Pair it with an inbox/result record and periodic reconciliation.[unverified]
- **Last progress per boundary.** For each route, retain timestamps for submitted, transport-accepted, received, completed, and result-returned.[unverified]
- The first timestamp transition that stops advancing narrows diagnosis: submission without acceptance suggests transport; acceptance without receipt suggests routing or consumer liveness; receipt without completion suggests processing; completion without a returned result suggests the response path or reconciliation.[2][7]
- This end-to-end check is necessary because lower-layer acknowledgements cannot establish application-level completion.[7]

## Operational use and limits

Define the expected consumer, route, response deadline, and meaning of completion.[unverified]
Alert on sustained pending age combined with absent route-local reads or stale/missing consumer registration.[unverified]
Include counts and timestamps in the alert so the owner can verify the signal.[unverified]
Use deadlines as operational SLOs, not claims made by the queue provider.[unverified]
Timeouts detect a lack of observed progress; they do not prove its cause.[unverified]
A consumer may be intentionally paused, a route may be dormant, or telemetry may be delayed.[unverified]
Approximate metrics and retry behavior can obscure the age or number of messages actually awaiting work.[1][3]
Preserve the message and correlation ID while investigating.[unverified]
This note describes detection signals; it does not authorize changing a live route.[unverified]

**Evidence and limits.** The sources document broker-level receive and delete semantics.[2]
They also document redrive and queue metrics.[1][3]
Kafka documents group heartbeat and session-timeout behavior.[4]
The outbox sources discuss sender-side publication guarantees and duplicate delivery.[5][6]
The route-local grouping and signal combinations above are engineering recommendations inferred from those mechanisms, not a comparative study of agent-network monitoring.[unverified]
The cited documentation does not establish a universal heartbeat interval, response SLO, or reconciliation cadence; choose and test thresholds against each route's intended workload.[3][4]
The distinction between never-read and partial-read work depends on per-route reads and item-level progress being observable.[2][3][unverified]

## Sources

[1] https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html — Using dead-letter queues in Amazon SQS
[2] https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/step-receive-delete-message.html — Receiving and deleting a message in Amazon SQS
[3] https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html — Available CloudWatch metrics for Amazon SQS
[4] https://kafka.apache.org/41/configuration/consumer-configs — Consumer Configs
[5] https://microservices.io/patterns/data/transactional-outbox.html — Transactional outbox pattern
[6] https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html — Transactional outbox pattern
[7] https://web.mit.edu/saltzer/www/publications/endtoend/endtoend.pdf — End-to-End Arguments in System Design
