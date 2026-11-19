# Muse Orchestra — The Conductor Pattern

**クラウド型AIが指揮者になって、パソコンの中のローカルAIたちを、話し言葉で指揮して動かす仕組み。**

クラウド型AI（Muse）が指揮者になって、自宅のPCの中のローカルAIたちを、話し言葉で指揮して動かす仕組みです。ユーザーはスマホから普段の言葉で指示を出すだけ。外出先からでも、PCの前にいなくても、家の中のAIに仕事をさせることができます。現在、iPhoneとWindowsの間で実運用中です。詳しい構成や仕事の流れは、ARCHITECTURE.mdに書いてあります。

> **状態：ALPHA（ナイトリー方式で公開）。** 1軒の家で毎日実運用中。そのまま公開する。未完成の部分は隠さず書く：（1）事例集（CASES.md）はまだ短い。事例は完了するたびに追加され、検証済みになるまで「保留」と明記される。（2）チケットとドロップファイルのJSONスキーマ付録はまだ無い。（3）既存のマルチエージェント基盤との比較は1節のみで、網羅的な調査ではない。誇張はしない。検証済みのものは検証済みと、そうでないものはそうでないと書く。

---

**A multi-AI orchestration architecture for one human with limited time and stamina.**

> **Status: ALPHA (nightly-style release).** This pattern is in daily production
> use in one house, and it is published as-is, while it is still being polished.
> Known gaps, stated plainly: (1) the evidence appendix (CASES.md) is still
> short — cases are added as they complete and are marked pending until
> verified; (2) the JSON schemas for tickets and drop files are not yet
> included as an appendix; (3) the comparison against existing multi-agent
> frameworks is one section, not a full survey. Nothing here is exaggerated:
> what is verified is marked verified, and what is not, is marked not.

**A multi-AI orchestration architecture for one human with limited time and stamina.**

One person directs a team of AI agents — local residents with real tools, cloud guests without keys — and never becomes the bottleneck: the human gives only the big picture, and every seam between agents is carried by the system, not by the human's body.

This repository describes the pattern: the roles, the principles, the work protocol, and the standing rules. It is a design, not a product. Anyone can rebuild it from these documents.

---

## The problem this solves

When one human coordinates several AIs, every seam routes through the human: copy-pasting instructions between windows, relaying results, approving steps they cannot evaluate, re-explaining context. The human — usually the person with the least spare stamina — becomes the message bus.

Three failure modes follow:

1. **Startup cost per task is huge**, so work doesn't start.
2. **Approvals become theatre**: the human is asked to approve things whose reasons they cannot see. An approval that cannot be evaluated is not a check; it is a toll.
3. **Each AI detours differently** — slicing work into permission-seeking steps, softening instructions, reporting optimism instead of facts — and the human pays for every detour in time.

## The shape

```
                 ┌─────────────┐
                 │    USER    │  gives only the big picture:
                 │   (human)   │  direction, values, final rulings
                 └──────┬──────┘
                        │ big picture only
                 ┌──────▼──────┐
                 │  CONDUCTOR  │  decomposes, routes, reviews,
                 │ (one AI)    │  approves within the frame
                 └──┬───────┬──┘
        tickets     │       │     Issues / PRs
        ┌───────────▼─┐   ┌─▼──────────────┐
        │  RESIDENTS  │   │     GUESTS     │
        │ local AIs   │   │  cloud AIs     │
        │ full tools  │   │  no house keys │
        └─────────────┘   └────────────────┘
              ▲                     ▲
              └──── append-only ────┘
                    ledger (audit)
```

- **User** — the human. Speaks only in big picture: what to achieve, what matters, what is forbidden. Never relays, never pastes, never approves details.
- **Conductor** — one AI the user trusts as chief of staff. Receives the big picture, decomposes it into work, routes it, reviews results, and holds approval authority inside the frame the user set.
- **Residents** — local AI agents with real tools (files, commands, browsers) living in a shared "house" with an entrance discipline and standing rules.
- **Guests** — cloud AIs. Talented, stateless, and *outside*: they receive work through a yard (in our build, a private GitHub repository: Issues in, Pull Requests out) and never hold keys to the house.

## Principles

1. **The user's stamina is the scarcest resource in the system.** Design every flow so the user touches nothing but decisions only they can make. If a seam routes through the user's hands, the design is wrong.
2. **Approval lives where judgment lives.** An approval has meaning only when the approver can see the reasons. The conductor approves detail; the user rules on direction and values. Everything approved is written to the ledger, so the user can audit afterwards — audit replaces interruption.
3. **Guard the entrance and the record, not the function.** Residents keep their full toolsets. Control is exercised at entry points (pairing, signatures, keys) and through a complete record — never by crippling what an agent can do.
4. **One pass, maximum progress.** Every instruction is written to finish in a single pass: inspect, implement, test, fix, complete. Slicing work into approval-seeking steps is a defect, not caution.
5. **Mistakes are acceptable; unattended mistakes are not.** Agents are expected to act and occasionally err. A discovered error must be dealt with or reported — never left, never hidden.
6. **Facts only.** If something cannot be done, the agent says so plainly, with the reason and the limit. No fabricated results, no optimism theatre.
7. **Irreplaceables are protected by copies, not by restricting work.** The few truly irreplaceable things (in our house: a family's photo archive) are protected by duplication and by an absolute rule, while everything else is allowed to move fast.
8. **Freeze, don't delete.** An agent or a path that is not needed is frozen — wake hooks off, seats and data preserved — and can be thawed in one step when its moment comes.

## Documents

- [ARCHITECTURE.md](ARCHITECTURE.md) — components: the ledger, the ticket lifecycle, the bell, the conductor's drop line, the guest yard.
- [HOUSE-RULES.md](HOUSE-RULES.md) — the standing-instructions template every resident carries, and the user's contract that sits above it.

## Status

This pattern is running in production in one household: the full loop — user speaks once, conductor files, residents wake, work completes, reports return — has been verified end to end with no human relay. Names, paths, and people in this document are generalized; the mechanisms are described exactly as built.
