# Capability: restate-audit-export

One line: continuously export Restate invocation state to the audit trunk
(Kafka `audit-events` + PG `audit_events`), replayable and idempotent.

## Contract
- Source of truth: Restate 1.7 admin `POST /query` (DataFusion SQL over
  `sys_invocation`), `Accept: application/json` (no REST listing endpoint).
- Outputs: JSON events to Kafka `audit-events` (retention -1); upserts into
  PG `audit_events` keyed by invocation_id (idempotent, replay-safe).
- Cursor: singleton `audit_export_state` (created_at + invocation_id
  tiebreaker); poll interval configurable (default 10s).
- Modes: `export` (continuous), `replay` / `replay-wait` (earliest-offset
  rebuild via the same upsert — byte-identical replays).

## Implementation
Kernel repo (monorepo): `kernel/audit_export/` (restate_client / exporter /
consumer / __main__), DDL `ops/sql/0004_audit_export.sql`, image
`deploy/audit-export.Dockerfile` → `cloudcrane/audit-export:0.1.0`.
Delivered by WO-0001 (kernel PR#22, platform PR#17), CI 77/77, mypy strict.

## Operation
Compose service `audit-export` (internal-only). Env: RESTATE_ADMIN_URL,
KAFKA_BOOTSTRAP, KERNEL_PG_DSN, AUDIT_POLL_INTERVAL, AUDIT_EXPORT_MODE.

## Eval
eval-gate repo `suites/restate-audit-export.md` (E1 completeness, E2 kafka
mirror, E3 replay idempotency, E4 24h uptime, E5 governance conformance).

## Evidence
`evidence/` — signed by sign.yml (evaluator=eval-signer) per manual §6.3.
