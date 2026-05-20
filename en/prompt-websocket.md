# Prompt — WebSocket service

> [🇷🇺 Русский](../ru/prompt-websocket.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #websocket #realtime #backend #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — WebSocket Service Generator](https://logicballs.com/ai-prompt/prompts/websocket-service-generator)
**Delta from original:** added honest alternatives (SSE / polling), explicit reconnect-resilience requirement, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need a real-time service: chat, live updates, collaboration, game events
- When you need to think through protocol, scaling, and auth before implementation
- When REST no longer covers latency / push scenarios

## What to substitute

- `{{programming_language}}` — string, e.g. `Python 3.13` / `Go 1.22`
- `{{framework}}` — string, e.g. `FastAPI + websockets` / `Starlette` / `gorilla/websocket`
- `{{use_case}}` — string, e.g. `team chat up to 100 people per room`
- `{{scale_requirements}}` — string, e.g. `~50k concurrent connections; 500 msg/sec per connection peak`
- `{{auth_method}}` — enum: `jwt | oauth2 | api_key | session_cookie | signed_url`
- `{{persistence_requirement}}` — string, e.g. `last 100 messages in Redis; long-term archive in S3`
- `{{infrastructure}}` — string, e.g. `Kubernetes, Redis Streams as backplane, ALB with sticky sessions`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Connection lifecycle described with concrete messages (payload examples), not just "there's a heartbeat"
- A specific pub/sub backplane is named (Redis / NATS / Kafka), not "some broker"
- Reconnect strategy is worked out: what happens to missed messages
- If WebSocket isn't optimal — an alternative is honestly proposed (SSE, long polling) with reasoning

## Prompt

```text
Act as a Principal Software Engineer for real-time distributed systems. Design and describe a production-ready WebSocket service.

Context:
- Language: {{programming_language}}
- Framework: {{framework}}
- Use case: {{use_case}}
- Target scale: {{scale_requirements}}
- Authentication method: {{auth_method}}
- Data persistence requirements: {{persistence_requirement}}
- Infrastructure: {{infrastructure}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Generate:
1. [required] Overall WebSocket service architecture
2. [required] Connection lifecycle:
   - connect
   - authenticate
   - subscribe
   - heartbeat
   - disconnect / reconnect
3. [required] Message protocol:
   - inbound message types
   - outbound message types
   - error payloads
   - versioning
4. [required] Scaling:
   - sticky sessions or not
   - pub/sub backplane
   - fan-out strategy
5. [required] Reliability:
   - retries
   - backpressure
   - rate limits
   - duplicate handling
6. [required] Security:
   - auth
   - connection abuse prevention
   - input validation
7. [required] Observability:
   - connection metrics
   - message latency
   - error rates
8. [if room remains] Project layout example and key handlers

Don't do this:
- Missing heartbeat — connections "hang" and exhaust the pool.
- Connection state stored only in process memory (won't survive a pod restart).
- Auth only checked on connect, never re-validated on long sessions.
- Broadcast via a loop over all connections without backpressure — one slow client blocks everyone.

Requirements:
- Avoid toy implementations
- Account for the reality that real-time systems often break on reconnect and scale-out
- If WebSocket isn't the best choice here, honestly propose SSE or polling

Response format:
- Summary
- Architecture
- Message protocol
- Components and infra
- Risks
```
