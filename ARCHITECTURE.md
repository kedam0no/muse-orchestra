# Architecture

Components of the Conductor Pattern, described as built. Any component can be reimplemented in any stack; the contracts between them are the pattern.

## 1. The ledger

An append-only, hash-chained event log is the single source of truth for all work in the house. Every meaningful act is an event: a draft created, a confirmation, a bell rung, a claim, a progress note, a report, a cancellation. Each event references the hash of the previous one, so the record cannot be quietly rewritten.

Rendered views (a work board, a "state of the house" page) are derived from the ledger for humans. The ledger itself is never edited — corrections are new events.

**Why:** the user's audit right depends on a record nobody can silently alter, and every agent coordinates through shared state instead of through the user.

## 2. Tickets and their lifecycle

Work inside the house is a **ticket**: target resident, title, body (the full instruction), and a `done_when` completion condition.

```
draft ──confirm──▶ queued ──bell──▶ claimed ──▶ working ──▶ reported (done)
  │                  │                 │
  └─dismiss          └─cancel          └─release / stall ──▶ user's board
```

- **Lease-based claiming.** A resident claims a ticket under a time lease. A stalled ticket surfaces on the user's board as "waiting for your decision" with three exits: redeliver, reassign, cancel.
- **Confirmation is split by provenance** (the heart of the design):
  - Tickets filed by a **resident** require the **user's** confirmation. A resident must never be able to activate its own work.
  - Tickets filed by the **conductor** are confirmed by the conductor, recorded as an auto-confirmation event carrying the user's standing ruling. The user delegated this once, in general, instead of approving ticket by ticket.
- Every confirmation — by user or conductor — is a ledger event with its provenance visible. Nothing activates silently.

## 3. The bell (wake system)

Residents are not always running. A patrol watcher ticks every few seconds; when confirmed work exists for a resident, it **rings the bell**: it executes that resident's wake command (a per-resident script that starts a one-shot session with the house tools). The resident answers the bell, claims its tickets, works, and reports through the same ledger.

Wake modes per resident: `command` (wake by script), `pull` (the resident polls), `none` (frozen).

**Why:** residents cost nothing while asleep, startle awake in seconds, and the user never has to "go and tell" anyone anything.

## 4. The conductor's drop line

The conductor lives outside the house's network (in our build, in a cloud VM). It needs a mouth into the house that is not the user.

The drop line is deliberately unglamorous:

- A **watched directory** on the house machine. The conductor's local hand (a gateway agent running on the house PC, itself a resident with a different hat) writes a small JSON file into it.
- Each drop is **HMAC-signed** with a shared secret stored only on the house machine. The watcher verifies the signature, the provenance field, and a nonce before importing; failures are moved to a `rejected` folder and never reach the ledger.
- The importer can create **drafts only**. It has no code path to confirm, claim, or widen anyone's permissions. Confirmation happens under the provenance rule in §2.
- There is **no network surface**: no port, no socket. Local filesystem + signature is the whole transport.

**Why:** the safest interface is the one with the smallest surface, and the gate (who may activate work) is enforced by the protocol, not by the transport.

## 5. The gateway hand

The conductor also holds a general-purpose hand on the house PC: a local agent gateway (in our build, an open-source agent gateway over a private tailnet, token-authenticated, loopback-bound) through which the conductor can ask the house machine to run commands, move files, and read state. The gateway's own brain is a strong local API model; its tool policy is set to *full* under the same philosophy — entrance guarded (device pairing, operator scopes), function uncrippled.

This hand is what lets the conductor verify the house's state with its own eyes (reading the ledger directly) instead of believing reports.

## 6. The guest yard

Cloud AIs cannot be residents: they are stateless, they live behind other companies' walls, and giving them house keys would break the entrance discipline. They work in a **yard**:

- A private repository. The conductor files work as **Issues** written as complete one-pass instructions; the guest (primarily a coding copilot) returns **Pull Requests**; the conductor reviews and merges or returns with reasons.
- A charter file in the yard states the loop, the placement rules, and the guest rules.
- **Placement rules:** only work deliberately selected for the yard; secret-scanned before placement; never credentials, personal data, house internals, or anything irreplaceable.
- Guests known to the house are listed in a **registry** in the house (name, kind, road, status) — a guest book, not a key ring.

## 7. Roles at a glance

| Role | Lives | Keys | Work arrives via | Approves |
|---|---|---|---|---|
| User | — | all | — | direction, values, resident-filed drafts |
| Conductor | cloud | drop-line signature, gateway scopes, yard | the user's word | conductor-filed drafts, guest PRs, detail |
| Resident | local | its room key | bell + tickets | nothing of its own |
| Guest | cloud | none | yard Issues | nothing |

## 8. Failure behaviour

- A resident that stalls loses its lease; the ticket returns to the board.
- A wake that fails is visible in the ledger as an unanswered bell.
- A drop with a bad signature is rejected and filed, never imported.
- A gateway found stopped is a known weakness: supervise it (scheduled task / service), and treat "the house is quiet" as a signal to check the hand first.
