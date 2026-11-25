# ADR 014 — Reconfirmed POC persistence and recovery

- **Status:** accepted
- **Affects:** Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Reviewed-example governance, Evaluation harness

## Decision

Supersede ADR 005 with the user's explicit answer to reopened interview Decision 6: **1. Accept Decision 6**.

Use SQLite for structured POC state and managed local directories for screenshots and artifacts. Structured state includes conversations, messages, evidence references, immutable report versions, review actions, and approval state. Keep records and managed files behind their owning component interfaces. Selection of job persistence fields and execution mechanisms remains for Decision 7; this decision establishes the POC storage technology, not an accepted worker design.

Durably save screenshots and user inputs before starting analysis. Retain acknowledged committed state across application restart. Preserve prior reports when analysis or revision fails. Approval refers to the exact completed report version reviewed; a new revision never inherits that approval automatically.

SQLite transactions do not atomically cover filesystem operations. Track incomplete saves and deletions durably; use staged artifact publication and restart reconciliation so interrupted work cannot expose missing evidence as a successful submission. Reject unsaved input as durable and expose unresolved cleanup rather than silently declaring it complete. Detailed staging, reconciliation, and schema contracts are implementation-design work.

Manual deletion first blocks access and new publication for the deleted source, then removes dependent records and managed artifacts through their owners. Track interrupted cleanup and resume it after restart. Delayed or retried inference results must not recreate deleted conversations, evidence, or reports. Preserve only the minimum non-content cleanup metadata needed to complete the operation, subject to the applicable lifecycle policy. Production retention/reuse obligations remain open for Decision 9 and later policy work.

Choose actual database and artifact paths only after Windows/WSL placement is confirmed and filesystem behavior is checked in that environment. This acceptance does not select a Windows directory, WSL Linux path, mounted Windows path, or network filesystem. Test persistence, file staging, interrupted saves/deletion, version-specific approval, restart recovery, and late-result rejection on the selected storage arrangement in component notebooks and reproducible integrated evaluation.

This is POC storage only. Production storage, backup/restore policy, host/disk-loss recovery, and secure-erasure guarantees are not established. Durable execution, production authorization, and reviewed-example lifecycle mechanisms remain pending Decisions 7–9.

## Why

The user explicitly re-confirmed SQLite plus managed local directories and retained the proposed restart recovery, version-specific approval, and deletion behavior. Embedded storage avoids additional database/object-storage services while preserving inspectable local state. Separate database and file operations require explicit recovery coordination. Choosing paths after deployment placement avoids claiming filesystem compatibility before it has been evaluated. Restart durability of committed data does not imply recovery from disk loss.

## Rejected

- PostgreSQL plus object storage immediately — adds infrastructure and operational work before the local POC demonstrates a need.
- Selecting physical storage paths before Windows/WSL placement is confirmed — leaves filesystem assumptions unverified.
- Treating database and file writes as one atomic transaction — ignores partial saves and cleanup after interruption.
- Applying approval to all revisions or allowing late results to restore deleted data — violates the accepted approval and deletion behavior.
