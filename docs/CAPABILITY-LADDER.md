# Capability ladder — giving the Dot more power without giving it the production root

The Dot is driven by group messages. Any group member can inject text. A flat “root on the VPS” grant turns one bad message into a production takeover. The design below gives the Dot room to do more than it can today, while keeping the irreversible actions behind a broker.

## Principles

1. **Broker, not keys.** The Dot never holds the VPS root key, GitHub token, or Cloudflare token. It calls typed broker actions; the broker holds the credentials.
2. **Least privilege per action.** Each action has an allowlist, rate limit, timeout, and audit row.
3. **Reversibility.** Sandbox snapshots, staging deploys, git commits, and armed production deploys all have a rollback.
4. **Two-key high-risk.** Production deploy requires an arm token plus an Owner-visible record.
5. **Observable.** Every action is logged with actor, input hash, result, and duration; a daily digest goes to the Owner.

## Ladder

| Level | Power | Where | Guard |
|---|---|---|---|
| **L0** | Read status, logs, QQ history, repo | hub + broker | token, rate limit |
| **L1** | Send QQ messages/media, commit memory notes, open issues/PRs | hub + `openaidot` repo | content policy, redaction, per-repo deploy key |
| **L2** | Deploy to staging, restart its own staging units, run tests | staging hub | tests must pass; auto-rollback |
| **L3** | Full root, install packages, run experiments | disposable sandbox VM/container | snapshot before session; egress allowlist; no production network |
| **L4** | Production deploy, service restart, config change | production VPS | arm token + Owner approval + audit + rollback |

The Dot gets **L0–L3 by default**. L4 exists, but is time-boxed and armed, not standing.

## Components

- **Broker** (`capability-broker`): MCP server exposing `broker_status`, `broker_exec`, `broker_deploy`, `broker_git`, `broker_memory`. Holds credentials in root-only files; the Dot sees only action names and results.
- **Policy engine**: per-action allowlist (`systemctl restart dwgx-hub-*`, `docker logs napcat`, `git push openaidot/openaidot`), rate limits, and a deny list (`/etc/shadow`, cloud metadata, other tenants).
- **Sandbox**: a separate VM or privileged container with root, a snapshot/rollback button, and an egress allowlist. The Dot can break it freely.
- **Staging hub**: a second generation of the hub on a different port/tree; the Dot can deploy there without touching production.
- **Production deploy**: the existing `hub_deploy` + deploy-arm, extended with a broker approval and a canary health gate.
- **GitHub**: dedicated `openaidot` account; repo-scoped deploy key; `memory/` commits; GitHub Action validates schema and rejects secrets.
- **Audit**: every broker call writes to `audit_log`; a daily digest is posted to the Owner (not the group).

## First concrete steps

1. Create `dotdeploy` with sudo only for `dwgx-hubctl`, `systemctl restart dwgx-hub-r03d-*`, `journalctl -u dwgx-hub-*`, `docker ps/logs napcat`.
2. Stand up a sandbox VM/container with root and a snapshot; give the Dot `broker_exec` there.
3. Create the `openaidot` GitHub account/repo and a repo-scoped deploy key; implement `memory_commit`.
4. Add `broker_deploy` for staging; keep production behind the arm.
5. Add the daily audit digest and a kill switch (`broker_revoke`).

## Threat model

| Threat | Mitigation |
|---|---|
| Prompt injection in a group message | broker policy, no standing root, L4 arm |
| Credential exfiltration | broker holds keys; Dot never sees them |
| Runaway loop / cost | rate limits, per-action timeout, daily budget |
| Destructive command | deny list, snapshot, rollback |
| Public repo leak | redaction gate + GitHub Action + no secrets in memory |
